Maybe use `validator` module instead of those?

    digits =
      (v) -> typeof v is 'string' and v.match /^\d+$/
    number =
      (v) -> typeof v is 'number'
    text =
      (v) -> typeof v is 'string' and v.length > 0
    boolean =
      (v) -> v is true or v is false
    array =
      (v) -> typeof v is 'object' and v?.length?
    timezone =
      (v) -> typeof v is 'string'
    language =
      (v) -> typeof v is 'string'

Call Forwards

    cf = (name) ->
      (v,doc) ->
        if doc["cf#{name}_enabled"] and not doc["cf#{name}_voicemail"]
          digits v
        else
          optional digits v

    any = (args...) ->
      (v,doc) ->
        for a in args
          return true if a v,doc
        return false

    optional = (t) ->
      (v) ->
        not v? or t v

    required = (t) ->
      (v) ->
        assert v?, "#{t} is required"

    assert = (t,m) ->
      if not t
        throw forbidden: m ? 'assertion failed'

    module.exports = {
      digits
      number
      text
      boolean
      array
      timezone
      language

      cf
      any
      optional
      required
      assert
    }
