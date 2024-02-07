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
    public static void setDescriptionOfAccounts(List<Account> newAccountList){
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
## Write a roll up summary trigger ?

## Write a trigger for below scenario ?
> On the Account when the Account is updated check all opportunities related to the account. Update all Opportunities Stage to close lost if an opportunity created date is greater than 30 days from today and stage not equal to close won

## Write a trigger to make sure there are no duplicate contacts based on email ?

```JAVA
trigger duplicatePreventionTrigger on contact (before insert){
    if(Trigger.isBefore && Trigger.isInsert){
        ContactBeforeInsertHandler.checkForDuplicates(Trigger.new);
    }
}

Public Class ContactBeforeInsertHandler {

    public static void checkForDuplicates(List<Contact> contactList){
        
        Set<String> incomingContactEmails = new Set<String>();
        Set<String> existingContactEmails = new Set<String>();

        for(Contact con : contactList){
            String currentEmail = con.email;
            if(!String.isEmpty(currentEmail)){
                incomingContactEmails.add(currentEmail);
            }
        }

        List<Contact> existingContactsWithEmail = new List<Contact>();

        if(!incomingContactEmails.isEmpty()){
            existingContactsWithEmail = [Select id, email from Contact where email in:incomingContactEmails ];
        }

        if(!existingContactsWithEmail.isEmpty()){
            for(Contact con : existingContactsWithEmail){
                String currentEmail = con.email;
                existingContactEmails.add(currentEmail);
            }            
        }

        for(Contact con : contactList){
            String conEmail = con.email;
            if(existingContactEmails.contains(conEmail)){
                con.addError('There already exist a contact with same email address');
            }
        }
    }

}
```

## Write a trigger on the Opportunity line item when a line item is deleted delete an opportunity as well.

## Write for below scenario 
> When the account Status field is updated and if the related contact is more than zero then show an error and if the related contact is equal to zero then do nothing

```JAVA
trigger accountTrigger on account (before update){

}

public class AccountTriggerHandler {
    public static void handleContactValidation(List<Account> accList,Map<String,Account> oldAccountMap){

        List<Account> updatedStatusAccounts = List<Account>();

        for(Account acc : accList){
            String currentStatus = acc.Status;
            String oldStatus = oldAccountMap.get(acc.id).status;

            if(currentStatus != oldStatus){
                updatedStatusAccounts.add(acc);
            }
        }

        List<Account> accountsWithContacts = [Select id , status, (Select id from contacts) from account where id in: updatedStatusAccounts];

        for(Account acc : accountsWithContacts){
            if(acc.contacts.size() > 0){
                acc.addError('Something is wrong');
            }
        }
    }
}
```


## Write for below scenario 
> Once an Account is inserted an email should go to the System Admin user with specified text below.An account has been created and the name is “Account Name”.

## Write a simple trigger framework ?

## Write for below scenario ?

>Business Use Case: A Sales Organization wants to track the largest Opportunity associated with each of its Accounts. This information could be used to prioritize sales efforts, allocate resources, or identify opportunities for cross-selling or upselling.

>Pre Work: Create a Custom field on Account Object named maxOpp__c (Text) to store Opportunity Name.

```JAVA
trigger oppTrigger on Opportunity (after update , after insert , after delete , after undelete){
    
    if(Trigger.isUndelete){
        OpportunityManagement.handleLargestOpportunity(Trigger.new);
    }else{
        OpportunityManagement.handleLargestOpportunity(Trigger.old);
    }
}

public class OpportunityManagement(){
    
    public static void handleLargestOpportunity(List<opportunity> opportunityList){ 
        Set<String> accountIds = new Set<String>();

        for(Opportunity opp : opportunityList){
            accountIds.add(opp.accountId);
        }

        List<Account> accountList = new List<Account>();
        
        if(!accountIds.isEmpty()){
            accountList = [Select id ,maxOppField, (Select id , name from opportunities limit 1 order by revenue desc) from Account where id in : accountIds];            
        }

        List<Account> accountsToBeUpdated = new List<Account>();

        for(Account acc : accountList){
            if(!acc.opportunities.isEmpty()){

                String currentOppName = acc.maxOppField;
                String newOppName = acc.opportunities[0].name;
                if(currentOppName != newOppName){                    
                    acc.maxOppField = acc.opportunities[0].name;
                    accountsToBeUpdated.add(acc);
                }
            }
        }

        if(!accountsToBeUpdated.isEmpty()){
            update accountsToBeUpdated;
        }
    }
    
}
```
## Write for below scenario ?
> Business Use Case: Your Company ABC Corp. wants to keep track of the highest and lowest salaries paid by each of its tech firms to gain insights into how salaries are distributed across different parts of the organization and take appropriate actions to ensure that employee salaries are fair and equitable.

> Pre Work:  You need to create 2 Custom Objects . Object – 1 : Tech_Firm__c Fields : Max_Salary__c (Currency) , Min_Salary__c (Currency)

> Object – 2 : Employee__c Fields : Salary__c (Currency) , Tech_Firm__c (Lookup)

## Write for below scenario ?

>Business Use Case: Let’s say a sales rep is working on an account and marks the “Close_all_Opps__c” field as true. Without this trigger, they would have to manually go through each open opportunity for that account and close them as won, which can be time-consuming and prone to errors. With this trigger in place, the opportunities will be automatically closed as won if they meet the criteria specified in the code.

> Pre Work: Create a custom checkbox field on Account named Close_all_Opps__c.

## Write for below scenario ?
> Business Use Case: Your organization sells products to its customers through opportunities. Each opportunity can have multiple products (Opportunity Line Items) associated with it. The organization wants to keep track of the total number of products sold to each account and display it on the Account record for reporting purposes.

> Pre Work: Create a custom field on the Account object named Number_of_Products__c (Number) to count the total number of products related to all Opportunities associated with the Account.

## Write for below scenario ?
> Business Use Case : Your company XYZ corp. wants to automate the creation of follow-up tasks for sales representatives whenever an opportunity’s stage changes in the Salesforce org 

## Write for below scenario ?
> Business Use Case: A sales organization sells multiple products to the same account. The “Number of Products” field on the Account object could be used to track how many different products the company has sold to each account. By updating this field automatically based on the number of Opportunity Line Items related to each Account, sales representatives and account managers can easily view this information in Salesforce and use it to inform their sales and account management strategies.

> Pre Work: Create a custom field on the Account object named Number_of_Products__c (Number).

## Write for below scenario ?
> Business Use Case: Automate the assignment of an “Account Rating” to Accounts based on the number of closed Cases associated with each Account.
## References
1. [Roll-Up Summary Trigger](https://www.pantherschools.com/create-a-rollup-summary-trigger-in-salesforce/)
1. 