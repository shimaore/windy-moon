    describe 'The module', ->
      {main} = require '..'

      describe '@required', ->
        it 'should detect', (done) ->
          f = main ->
            @required 'beast'
            done()

          f {beast:3}, {}, {}, {admins:{},members:{}}

        it 'should report', (done) ->
          g = main ->
            try
              @required 'beast'
            catch error
              done()

          g {}, {}, {}, {admins:{},members:{}}

      describe '@unchanged', ->
        it 'should detect', (done) ->
          f = main ->
            @unchanged 'beast'
            done()
          f {beast:3}, {beast:3}, {}, {admins:{},members:{}}

        it 'should report', (done) ->
          g = main ->
            try
              @unchanged 'beast'
            catch
              done()
          g {beast:3}, {beast:4}, {}, {admins:{},members:{}}

      describe '@forbid_adding_fields', ->
        it 'should detect', (done) ->
          f = main ->
            @forbid_adding_fields()
            done()
          f {beast:3}, {beast:3}, {}, {admins:{},members:{}}

        it 'should report', (done) ->
          g = main ->
            try
              @forbid_adding_fields()
            catch
              done()
          g {beast:3,fauna:2}, {beast:4}, {}, {admins:{},members:{}}

      describe '@forbid_removing_fields', ->
        it 'should detect', (done) ->
          f = main ->
            @forbid_removing_fields()
            done()
          f {beast:3}, {beast:3}, {}, {admins:{},members:{}}

        it 'should report', (done) ->
          g = main ->
            try
              @forbid_removing_fields()
            catch
              done()
          g {}, {beast:4}, {}, {admins:{},members:{}}

      describe '@forbid_modifying_fields', ->
        it 'should detect', (done) ->
          f = main ->
            @forbid_modifying_fields()
            done()
          f {beast:3}, {beast:3}, {}, {admins:{},members:{}}

        it 'should report', (done) ->
          g = main ->
            try
              @forbid_modifying_fields()
            catch
              done()
          g {beast:3}, {beast:4}, {}, {admins:{},members:{}}

      describe '@validate_type', ->
        it 'should detect', (done) ->
          f = main ->
            @validate_type()
            done()
          f {_id:'number:1234',type:'number',number:'1234'}, {}, {}, {admins:{},members:{}}

        it 'should report', (done) ->
          g = main ->
            try
              @validate_type()
            catch
              done()
          g {_id:'number:1234',type:'number'}, {}, {}, {admins:{},members:{}}
