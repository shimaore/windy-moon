A DSL for CCNQ4 validation functions
====================================================

Main DSL
--------

Usage: `main -> @unauthorized 'Not admin' unless @is_admin()  `

    deepEqual = require './deepEqual'
    stateChange = require './stateChange'

    @main = (f) ->
      (doc,oldDoc,userCtx,secObj) ->

### Error reporting

        forbidden = (reason) -> throw forbidden: reason
        unauthorized = (reason) -> throw unauthorized: reason

### Field presence

        has = (field) -> field of doc
        had = (field) -> field of oldDoc

        required = (field) ->
          return if has field
          forbidden "Field `#{field}` is required."

        unchanged = (field) ->
          return if deepEqual oldDoc[field], doc[field]
          forbidden "Field `#{k}` was modified."

### Field validation

        validate_fields = (validator, except = []) ->
          for own k,v of doc when k not in except
            unless validator[k]?(v)
              forbidden "Field `#{k}` has invalid value `#{JSON.stringify v}`."

        forbid_adding_fields = (except = []) ->
          for own k of doc when k not in except
            unless had k
              forbidden "Field `#{k}` was added."

        forbid_removing_fields = (except = []) ->
          for own k of oldDoc when k not in except
            unless has k
              forbidden "Field `#{k}` was removed."

        forbid_modifying_fields = (validate_field, except = []) ->
          for own k of doc when k not in except
            unchanged k

### Document type

        is_design = doc._id?[0] is '_'

CCNQ4 conventions for document types and keys

        $ = doc._id?.match /^(\w+):(.+)$/
        type = $?[1]
        key = $?[2]

        validate_type = ->
          unless doc.type is type
            forbidden 'Missing or invalid `type` field.'

          unless doc[type] is key
            forbidden "Field `#{type}` must contain `#{key}`."

          switch type
            when 'number'
              if '@' in key
                'local-number'
              else
                'global-number'
            else
              type

### User

        {db,name,roles} = userCtx

        is_logged_in = name?
        enforce_logged_in = ->
          unless is_logged_in
            unauthorized 'Not logged in.'

The `updated_by` field in the document is a `charming-circle` convention.

        enforce_updated_by = ->
          required 'updated_by'
          unless doc.updated_by is name
            forbidden "Field `updated_by` must contain `#{name}`."

### Roles

        may = (role) -> role in roles
        is_admin = -> may '_admin'

### Database security

The `owner` field in the security document is a `charming-circle` convention.
(At least in CouchDB 1.6.1, the content of the security object is passed as-is even if the fields are not in the documented ones.)

        {admins,members,owner} = secObj

        is_owner = name is owner

        enforce_ownership = ->
          unless is_owner
            unauthorized 'Not owner.'

### State changes

        event = stateChange oldDoc, doc

        forbid_deletion = ->
          if doc._deleted
            forbidden "You may not delete `#{type}` documents."

        forbid_creation = ->
          if not oldDoc? or oldDoc._deleted
            forbidden "You may not create `#{type}` documents."

### Document-level access

This uses the convention that a user is authorized to access/modify a document iff that document ID is in the user's roles.

        might = ->
          return false if not doc._id?
          return true if may doc._id
          if m = doc._id.match /^(?:number|endpoint):\d+@(\S+)$/
            domain = m[1]
            return true if may "number_domain:#{domain}"
          return false

        enforce_might = ->
          unless might()
            forbidden 'Not permitted to modify this record.'

### Context

        ctx = {

Parameters

          doc
          oldDoc
          userCtx
          secObj

          id: doc._id
          rev: doc._rev

Error reporting

          forbidden
          unauthorized

Field presence

          has
          had
          required
          unchanged

Field validation

          validate_fields
          forbid_adding_fields
          forbid_removing_fields
          forbid_modifying_fields

Document type

          is_design

          type
          key
          validate_type

User

          db

          name
          is_logged_in
          enforce_logged_in
          enforce_updated_by

Roles

          roles
          may
          is: may
          is_admin

Database security

          admins
          admins_names: admins.names
          admins_roles: admins.roles

          members
          members_names: members.names
          members_roles: members.roles

          owner
          is_owner
          enforce_ownership

State changes

          event
          forbid_deletion
          forbid_creation

Document-level access

          might
          enforce_might

        }

        f.call ctx, ctx
