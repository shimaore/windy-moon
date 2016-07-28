Maybe use `validator` module instead of those?

    @digits = digits =
      (v) -> typeof v is 'string' and v.match /^\d+$/
    @boolean = boolean =
      (v) -> v is true or v is false
    @optional_digits = optional_digits =
      (v) -> not v? or digits v
    @array = array =
      (v) -> typeof v is 'object' and v?.length?
    @timezone = timezone =
      (v) -> typeof v is 'string'
    @language = language =
      (v) -> typeof v is 'string'

    @assert = (t,m) ->
       if not t
         throw error: m ? 'assertion failed'
