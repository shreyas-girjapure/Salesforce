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
Where `trigger_events` are common separated list of one or more following events
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
## What is order of execution ? 
In Salesforce, the order of execution for triggers follows a specific sequence. Here's an overview of the trigger order of execution:

1. **System validation** : May perform following 
    1.  System validation rules are checked, including field-level and object-level validations.
    1. If Request if from UI , Layout specifiec validations are checked.
    1. For requests from multiline item creation such as quote line items and opportunity line items, Salesforce runs custom validation rules.
    1. For requests from other sources such as an Apex application or a SOAP API call, Salesforce validates only the foreign keys and restricted picklists.
1. **Before Triggered Flows** : It executes record triggered before flows , in unspecified order. 
1. **Before Triggers**: These triggers fire before the record is saved to the database. The order of execution is as follows:
    1.  System validation rules are checked, including field-level and object-level validations.
    1. Before triggers on the same object are executed in an unspecified order.
    1. Custom validation rules are evaluated.
    1. Any duplicate rules defined on the object are checked.
    1. Before triggers on parent objects are executed, but only for operations that involve a relationship with a parent object.

2. **Validation Rules**: After the before triggers complete, Salesforce checks the record against all active validation rules. If any rule fails, the entire transaction is rolled back.

3. **After Triggers**: After the record is saved to the database, after triggers are executed in the following order:
    1. After triggers on the same object are executed in an unspecified order.
    1. Workflow rules are evaluated, which can include field updates, email alerts, outbound messages, and more.
    1. Processes (Visual Workflow, Process Builder) are executed.
    1. Escalation rules are applied to cases.

4. **Assignment Rules**: If the record is an assignment object, such as a lead or case, assignment rules are applied.

5. **Auto-Response Rules**: If the record is an email message that meets the criteria of an auto-response rule, an automatic email response is generated.

6. **Workflow Rules**: After the record is saved, any additional workflow rules are evaluated again, as some criteria might now be met due to changes made during the before and after triggers.

7. **Processes (Visual Workflow, Process Builder)**: Any additional processes are executed again, similar to workflow rules.

1. **Record Triggered Flows**

8. **Roll-up Summary Fields**: If there are any roll-up summary fields on the object, they are updated.

9. **DML Operations**: The record and its related records are committed to the database.

10. **Post-Commit Logic**: Any post-commit logic, such as sending emails or making callouts, is executed after the record is saved.
    1. Sending email
    1. Enqueued asynchronous Apex jobs, including queueable jobs and future methods
    1. Asynchronous paths in record-triggered flows

It's important to note that if a trigger performs a DML operation (insert, update, delete) on a related record, the entire trigger order of execution is repeated for the related records.

Understanding the trigger order of execution is crucial for designing efficient and predictable automation in Salesforce. It helps in avoiding recursion, optimizing performance, and ensuring the desired outcomes of trigger-based actions.
## Write a trigger If Rating is Hot of the account , add `Hot` at the end of description ?
```JAVA
trigger AccountTrigger on Account (before insert){
    if(isBefore){
        if(isInsert){
            AccountTriggerHandler.setDescriptionOfAccounts(Trigger.new);
        }
    }
}


Public class AccountTriggerHandler{
    publlic static void setDescriptionOfAccounts(List<Account> newAccountList){
        for(Account acc : newAccountList){
            if(acc.rating == 'HOT'){
                acc.Description += 'HOT'
            }
        }
    }
}
```

## Write a trigger for , Every Account should have 1 contact inserted with it.
```JAVA
trigger AccountTrigger on Account (After Insert){
    if(isAfter){
        if(isInsert){
            AccountTriggerHandler.associatedContactsLogic(Trigger.new);
        }
    }
}

Public class AccountTriggerHandler{
    publlic static void associatedContactsLogic (List<Account> newAccountList){
        List<Contact> contactList = new List<Contact>();
        for(Account acc : newAccountList){
            Contact con = new Contact();
            con.accountId = acc.Id;
            contactList.add(con);
        }
        if(contactList != null && contactList.size() > 0){
            insert contactList;
        }        
    }
}
``` 