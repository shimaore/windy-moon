    chai = require 'chai'
    chai.should()

    describe 'stateChange', ->
      stateChange = require '../stateChange'
      it 'should detect create', ->
        stateChange null, _id:'beast'
        .should.equal 'create'
      it 'should detect modify', ->
        stateChange {_id:'beast'}, {_id:'beast'}
        .should.equal 'modify'
      it 'should detect delete', ->
        stateChange {_id:'beast'}, {_id:'beast',_deleted:true}
        .should.equal 'delete'
