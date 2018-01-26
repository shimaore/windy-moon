Maybe use `validator` module instead of those?

    digits =
      (v) -> typeof v is 'string' and v.match /^\d+$/
    boolean =
      (v) -> v is true or v is false
    array =
      (v) -> typeof v is 'object' and v?.length?
    timezone =
      (v) -> typeof v is 'string'
    language =
      (v) -> typeof v is 'string'


    optional = (t) ->
      (v) ->
        not v? or t v

    assert = (t,m) ->
      if not t
        throw forbidden: m ? 'assertion failed'

    module.exports = {
      digits
      boolean
      array
      timezone
      language

      optional
      assert
    }
