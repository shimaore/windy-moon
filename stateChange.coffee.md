    module.exports = stateChange = (oldDoc,doc) ->
      state_changes = [
        #                           oldDoc?  oldDoc._deleted doc? doc._deleted
        null                    #       0             0         0            0   not notified in this case?
        null                    #       0             0         0            1   invalid
        'create'                #       0             0         1            0
        null                    #       0             0         1            1   new deleted doc
        null                    #       0             1         0            0   invalid
        null                    #       0             1         0            1   invalid
        null                    #       0             1         0            0   invalid
        null                    #       0             1         0            1   invalid
        null                    #       1             0         0            0   not notified in this case?
        null                    #       1             0         0            1   invalid
        'modify'                #       1             0         1            0
        'delete'                #       1             0         1            1
        null                    #       1             1         0            0   not notified in this case? invalid?
        null                    #       1             1         0            1   invalid
        'create'                #       1             1         1            0   created *again*
        null                    #       1             1         1            1   remains deleted, not notified in this case?
      ]

      c = 0
      c += 1 if doc?._deleted
      c += 2 if doc?
      c += 4 if oldDoc?._deleted
      c += 8 if oldDoc?

      state_changes[c]
