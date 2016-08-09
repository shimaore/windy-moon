    describe 'The module', ->
      {main} = require '..'

      describe '@required', ->
        it 'should detect', (done) ->
          f = main ->
            @required 'beast'
            done()

          f {beast:3}, {}, {}, {}

        it 'should report', (done) ->
          g = main ->
            try
              @required 'beast'
            catch error
              done()

          g {}, {}, {}, {}

      describe '@unchanged', ->
        it 'should detect', (done) ->
          f = main ->
            @unchanged 'beast'
            done()
          f {beast:3}, {beast:3}, {}, {}

        it 'should report', (done) ->
          g = main ->
            try
              @unchanged 'beast'
            catch
              done()
          g {beast:3}, {beast:4}, {}, {}

      describe '@restrict_adding_fields', ->
        it 'should detect', (done) ->
          f = main ->
            @restrict_adding_fields()
            done()
          f {beast:3}, {beast:3}, {}, {}

        it 'should report', (done) ->
          g = main ->
            try
              @restrict_adding_fields()
            catch
              done()
          g {beast:3,fauna:2}, {beast:4}, {}, {}

      describe '@restrict_removing_fields', ->
        it 'should detect', (done) ->
          f = main ->
            @restrict_removing_fields()
            done()
          f {beast:3}, {beast:3}, {}, {}

        it 'should report', (done) ->
          g = main ->
            try
              @restrict_removing_fields()
            catch
              done()
          g {}, {beast:4}, {}, {}

      describe '@restrict_modifying_fields', ->
        it 'should detect', (done) ->
          f = main ->
            @restrict_modifying_fields()
            done()
          f {beast:3}, {beast:3}, {}, {}

        it 'should report', (done) ->
          g = main ->
            try
              @restrict_modifying_fields()
            catch
              done()
          g {beast:3}, {beast:4}, {}, {}

      describe '@validate_type', ->
        it 'should detect', (done) ->
          f = main ->
            @validate_type()
            done()
          f {_id:'number:1234',type:'number',number:'1234'}, {}, {}, {}

        it 'should report', (done) ->
          g = main ->
            try
              @validate_type()
            catch
              done()
          g {_id:'number:1234',type:'number'}, {}, {}, {}

      describe '@enforce_ownership', ->
        it 'should detect', (done) ->
          f = main ->
            @enforce_ownership()
            done()
          f {}, {}, {name:'jane'}, {members:{names:['jane']}}

        it 'should report', (done) ->
          f = main ->
            try
              @enforce_ownership()
            catch
              done()
          f {}, {}, {name:'joe'}, {members:{names:['jane']}}
