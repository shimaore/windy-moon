    chai = require 'chai'
    chai.should()

    describe 'deepEqual', ->
      deepEqual = require '../deepEqual'

      it 'should match null', ->
        deepEqual(null,null).should.be.true
        deepEqual(null,[]).should.be.false
        deepEqual(null,{}).should.be.false
        deepEqual(null,0).should.be.false
        deepEqual(null,'').should.be.false

      it 'should match boolean', ->
        deepEqual(true, false).should.be.false
        deepEqual(true, true).should.be.true
        deepEqual(false, false).should.be.true
        deepEqual(false, true).should.be.false
        deepEqual(true, 1).should.be.false
        deepEqual(false, 0).should.be.false
        deepEqual(false, null).should.be.false

      it 'should match numbers', ->
        deepEqual(0, 0).should.be.true
        deepEqual(0, 1).should.be.false
        deepEqual(0, null).should.be.false
        deepEqual(1, 0).should.be.false
        deepEqual(1, 1).should.be.true

      it 'should match strings', ->
        deepEqual('a','a').should.be.true
        deepEqual('a','b').should.be.false
        deepEqual('abcdef','abcdef').should.be.true
        deepEqual('','').should.be.true
        deepEqual('',null).should.be.false

      it 'should match arrays', ->
        deepEqual([],[]).should.be.true
        deepEqual([],null).should.be.false
        deepEqual([],0).should.be.false
        deepEqual([],{}).should.be.false
        deepEqual([1],[1]).should.be.true
        deepEqual([2],[1]).should.be.false
        deepEqual([1,2,3,4,5],[1,2,3,4,5]).should.be.true
        deepEqual([1,2,3,4],[1,2,3,4,5]).should.be.false
        deepEqual(['a',2,3,4],[1,2,3,4]).should.be.false
        deepEqual(['a',2,3,4],['a',2,3,4]).should.be.true
        deepEqual([0,b:4,3,4],[0,b:4,3,4]).should.be.true
        deepEqual([0,b:4,3,4],[0,b:5,3,4]).should.be.false

      it 'should match objects', ->
        deepEqual({},{}).should.be.true
        deepEqual({},null).should.be.false
        deepEqual({a:3},{a:3}).should.be.true
        deepEqual({a:3},{a:4}).should.be.false
        deepEqual({a:[1,2,3]},{a:[1,2,3]}).should.be.true
        deepEqual({a:[1,2,3]},{b:[1,2,3]}).should.be.false
        deepEqual({a:[1,2,3]},{a:null}).should.be.false
        deepEqual({a:[1,2,3]},{a:[1,2,3,4]}).should.be.false
