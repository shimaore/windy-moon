Based on https://github.com/substack/node-deep-equal/blob/master/index.js
except that
doc content is supposed to be JSON, so do not account for
Date, Buffer, arguments, etc.

    module.exports = (a,b) ->
      deepEqual = (a,b) ->

Identical values: null, string, numbers, booleans

        return true if a is b
        return false if typeof a isnt typeof b

Otherwise should be object (recursively compare)

        return false unless a? and typeof a is 'object'
        return false unless b? and typeof b is 'object'
        if a instanceof Array
          return false unless b instanceof Array

Must have identical key sets

        ka = Object.keys a
        kb = Object.keys b

        return false unless ka.length is kb.length

        ka.sort()
        kb.sort()
        for v,i in ka
          return false unless v is kb[i]

Must have identical contents

        for v in ka
          return false unless deepEqual a[v], b[v]

        true

      deepEqual a,b
