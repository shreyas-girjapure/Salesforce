# Trigger Concepts

## What are different trigger operations ?
1. Insert
1. Update 
1. Delete
1. Undelete
1. Merge
1. Upsert
## What are different types of trigger events and Context variables available ?
There are 2 major events `Before` and `After`.

### Insert
1. Before Insert 
    1. `Trigger.New`
        1. Context variable can be modified
        1. No DMLs allowed - it has not been created yet.
1. After Insert
    1. `Trigger.New` and `Trigger.newMap`
        1. Read Only
        1. Can be deleted - But Unnecessary
### Update
1. Before Update
    1. `Trigger.New` and `Trigger.newMap`
        1. Context variable can be modified
        1. DML Update is not allowed
        1. DML Delete is not allowed
    1. `Trigger.Old` and `Trigger.oldMap`
        1. Read Only
        1. DML Update is not allowed
        1. DML Delete is not allowed
1. After Update
    1. `Trigger.New` and `Trigger.newMap`
        1. Read Only 
        1. Can be deleted
        1. DML Update is allowed - But will cause recursion
    1. `Trigger.Old` and `Trigger.oldMap`
        1. Read Only
        1. Can be deleted 
### Delete
1. Before Delete 
    1. `Trigger.old` and `Trigger.oldMap`
        1. Read only
        1. Can not be deleted
1. After Delete
    1. `Trigger.old` and `Trigger.oldMap`
        1. Read only
        1. Can not be deleted - Already deleted
### Undelete 
1. After Delete
    1. `Trigger.new` and `Trigger.newMap`
        1. `Trigger.new` is restricted from update
        1. Can be deleted - But doesnt make much sense to delete again !

## What are trigger context variables ?
All triggers define implicit variables that allow developers to access run-time context. These variables are contained in the `System.Trigger` class.

1. `isExecuting`
    1. Returns true if the current context for the Apex code is a trigger, not a Visualforce page, a Web service, or an executeanonymous() API call.
1. `isInsert` , `isUpdate` , `isDelete` , `isUndelete`
1. `isBefore` `isAfter`
1. `new`
    1. Returns a list of the new versions of the sObject records.
1. `newMap`
    1. A map of IDs to the new versions of the sObject records.
1. `old`
    1. Returns a list of the old versions of the sObject records.
1. `oldMap`
    1. A map of IDs to the old versions of the sObject records.
1. `operationType`
    1. OperationType Returns an enum of type `System.TriggerOperation`corresponding to the current operation.
        1. `BEFORE_INSERT`
        1. `BEFORE_UPDATE`
        1. `BEFORE_DELETE`
        1. `AFTER_INSERT`
        1. `AFTER_UPDATE`
        1. `AFTER_DELETE`
        1. `AFTER_UNDELETE`

## How to write a trigger ?
We can write triggers on most standard and custom objects

```SQL
trigger TriggerName on ObjectName (trigger_events) {
    code_block
}
```
Where `trigger_events` are comman separated list of one or more following events
1. `before insert`
1. `before update`
1. `before delete`
1. `after insert`
1. `after update`
1. `after delete`
1. `after undelete`
## What are trigger best practices ?
1.  `One trigger per object`
    1. If we have multiple managing their order of execution becomes difficult
1. `Bulkify triggers and logic` : Always assume context varibles have more than 1 record
    1. As there are many ways trigger can be invoked. Not always single user will trigger operation.
1. `Do not write logic in trigger` block , Make separate handler class for the trigger.
    1. Make code more readable and maintainable in long term.
1. `Understand business scenario , Order of execution , context variables` and then decide which type of operation to perform.
    
    example : If if values of same record has to be changed , write in Before Triggers

    If validations are to be set , write them on before triggers
## How to choose correct trigger for business scenario ?
## What is `merge` dml ?
## What happens on `merge` in trigger ?
## How to know if trigger has already executed ? How to prevent recursion in triggers ?
## How to write validations on records in trigger ?
Writing .addError() on record item is a programmatic way of addin validation before dmls.
Simply adding .addError() will restrict from any dml.

Example : 
```SQL
trigger oppTrigger on Opportunity (before insert) {
    for (Account a : Trigger.new ) {
        a.addError('Cannot insert Account');
    }
}
```
all the records will be prevented from further DML , in this case from insertion
## If there are `400` records inserted will they be inserted in same execution context ? Will they share static variables for all `400` records ?
## Can trigger's have static members ? Can we access trigger's static members from outside trigger ?
## Write a trigger on contact , check if account's annual revenue field is empty , if it is add 500 there ?