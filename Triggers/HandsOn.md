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