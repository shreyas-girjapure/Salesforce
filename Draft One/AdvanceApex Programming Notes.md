### Assignments

#### Get from which context code is invoked
>Request class provides us info of the question , this request represents apex invocation request or execution context request. Not to be confused with resource request.

##### Code / Solution :  
`Request.getQuiddity();` 

##### Resources Referred : 
1. [Request class](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex_class_System_Request.htm#apex_System_Request_getQuiddity)

#### Create and Call a future method and observe Limit
> Future methods differ in terms of 
	1. Number of SOQL queries allowed : 200 - 100 [Future and normal]
	2. Maximum CPU time  : 120000 - 60000
	3. Maximum Heap size : 12000000 - 6000000
	4. Number of queueable jobs added to the queue : 1 - 50
##### Code / Solution :  

    public static void doSomething(){
        System.debug('in do something');
        futureMethod();
        System.debug('another things');
    }
    @future
    public static void futureMethod(){
        System.debug('the future method');
        System.debug('accounts '+getAllAccounts());
    }

##### Explanation 
>Here in logs both of those debugs gets print in console and whenever system gets time to execute futureMethod it runs that.

>This behaviour is similar to javascript's event loop processing

#### Static variables and Future calls
> Since Contexts are different and static variables lives only till execution context. Future running methods cant access changed values of static variables within another context 
> Below is an example of that.

##### Code / Solution :  
	 public static void doSomething(){
	     System.debug('in do something');
		 AccountController.identifier = 'here is changed one';     
	     futureMethod();
	     System.debug('another things '+
	     AccountController.identifier);
	 }
	 @future
	 public static void futureMethod(){
	     System.debug('the future method');
	     System.debug('accounts '+getAllAccounts());
	     System.debug('access statuc '+
	     AccountController.identifier);
	 }
##### Explanation 
> Assuming AccountController.identifier = 'test value' was initialized by someone on that class and we tried to change in doSomething() method.
> In future debug statement we will see original initialized value and not the newly changed one.

#### Data Caching [Level One]
> Using static variables if a value is stored in that static variable then through out the context this will behave as a cache. This should be implemented for reducing number of queries. 

##### Code / Solution :  
Controller Class 

	public class UserController {
	    public static boolean isUserSpecial = false;
	    public static boolean userSpecialChecked = false;
	    	    
		  public static boolean isUserSpecial(String userId){
		      System.debug('special called');
		      if(!userSpecialChecked){
		          System.debug('special first time');
		          User u = [select id , 
		          isUserSpecial__c from user where id = :userId];
		          isUserSpecial = u.isUserSpecial__c;
		          userSpecialChecked = true;
		      }
		      return isUserSpecial;
		  }
		      public static boolean isUserSpecialOld(String userId){    
		          System.debug('is special old called');
		          User u = [select id ,
		           isUserSpecial__c from user where id = :userId];
		          return u.isUserSpecial__c;                 
		  }
		}

Runner Class : 

    public static void checkForSpecialUserMethodNew(){
        System.debug('firstMethod'+
        UserController.isUserSpecial(UserInfo.getUserId()));
        System.debug('firstMethod called again'+
        UserController.isUserSpecial(UserInfo.getUserId()));
    }
        public static void checkForSpecialUserMethodOld(){
        System.debug('Second Method '+
        UserController.isUserSpecialOld((UserInfo.getUserId())));
        System.debug('Second Method called again '+
        UserController.isUserSpecialOld(UserInfo.getUserId()));
    }
Anonymous window : 

Running this in anon window for the will result in consuming 2 SOQL queries  
`RandomCodeClass.checkForSpecialUserMethodOld();`
Where as 
`RandomCodeClass.checkForSpecialUserMethodNew();`
Running above as many time you want will result in only one query , since we are checking if we have already computed the value. This can reduce load from query limits.

Implementation of this pattern can be used in most cases where value will be static and wont change frequently.

#### Trigger Scenario #1 
> *Question* : Consider a problem where you want to store first contact name and email on the account object.

##### Code / Solution  :  

ContactTriggerHandler class 

    public class ContactTriggerHandler {
        public static void populateFieldsOnAccount(List<Contact> contactList){
            // Get all Accounts from the list
            Set<String> accountIds = new Set<String>();
            Map<Id,Account> accountMap ;
            
            for(Contact c : contactList){
                if(c.AccountId != null){
                    accountIds.add(c.AccountId);
                }            
            }
            if(accountIds.isEmpty()){
                return;
            }
            // Query on accounts
            if(accountIds!= null && !accountIds.isEmpty()){
                accountMap = new Map<Id,Account>([Select id , name , 
                First_Contact_Email__c , First_Contact_Name__c from account 
                where id IN : accountIds]);            
            }
            if(accountMap.isEmpty()){
                return;
            }
            Map<Id,Account> filteredAccounts = new Map<Id,Account>();        
            // Check if it is already populated
            for(Account acc : accountMap.values()){
                if(acc.First_Contact_Email__c == '' 
                && acc.First_Contact_Name__c == ''){
                    filteredAccounts.put(acc.id,acc);
                }
            }        
            // Add values if not
            for(Contact c : contactList){
                Account acc = filteredAccounts.get(c.accountId);
                if(acc != null){
                    acc.First_Contact_Email__c = c.Email;
                    acc.First_Contact_Name__c = c.LastName;
                }            
            }
            // Update accounts
            try{
                update accountMap.values();            
            }catch(Exception e){
                System.debug(e);
            }
        }    
    }

Test class 

    @isTest
    public class ContactTriggerHandlerTest {
        @testSetup static void createTestData(){
            // Create Accounts 
            List<Account> accountList = new List<Account>();
            
            for(Integer i = 0 ; i < 200 ; i++){
                Account acc = new Account();
                acc.name = 'TestAccount'+i;
                accountList.add(acc);
            }
            insert accountList;
            
            List<Contact> contactList = new List<Contact>();        
            for(Integer i = 0 ; i < 200 ; i++ ){
                Contact con = new Contact();
                con.email = 'testEmail'+i+'@email.com';
                con.lastName = 'testLastName'+i;
                con.AccountId = accountList[i].id;   
                contactList.add(con);
            }
            insert contactList;
        }
        
        @isTest
        public static void testPopulateFieldsOnAccount(){
            List<Contact> contactList = [Select id , name , AccountId from 
            contact];
            List<Account> accountList = [Select id from account ];
            
            Test.startTest();
            System.debug('the list size '+contactList.size());                
            System.debug('the list size '+accountList.size());
            ContactTriggerHandler.populateFieldsOnAccount(contactList);
            Test.stopTest();
            
        }
    }

#### Unit Testing Raise Exceptions in tricky scenario
> This pattern makes user to be able to raise the exception whenever required from code.
> Private properties or method cant be accessed from another class. To make them visible in test class to raise coverage we use @testVisible annotation. This way no other developer will accidently use that variable. This when set true can be used in any code block to raise exception


>Below example is raising Mathematical Exception. Developers are free to raise exception they want in block of raise exception flag. 

##### Code / Solution  :  
>Posting snippets only full code not posted

	 public class ContactTriggerHandler {
	   @testvisible
	   private static boolean raiseException = false;
		        // Update accounts
        try{
            update accountMap.values();
            if(raiseException){
                integer x = 1/0;
            }            
        }catch(Exception e){
            System.debug(e);
        }
	   }

Test class Code : 

	    Test.startTest();
        ContactTriggerHandler.raiseException = true;

#### Trigger Static variables
> Static variables cannot be accessed by other classes or triggers . This is to be considered when designing variables and system

##### Code / Solution  :  

	trigger ContactTrigger on Contact (before insert) {   
	    public static string triggerStatic = 'someGarbage';

#### Trigger Data driven switch
> Triggers need to be data driven . There needs to be provision to switch trigger On / Off as per status.
> There are various ways to handle this . There needs to be design in place at project architecture level.
> Simple POC for this is below.

##### Code / Solution  :  

    Implementation #1 :Adding Custom settings for trigger. Considering there 
    is only one trigger per object we can add switch to restrict the 
    flow of the trigger.
    Implementation #2 : Another way to do so is using static variables in class. 
    Implementation #3 : Custom Metadata

Handler Code : 

    public class ContactTriggerHandler {
       @testvisible
       private static boolean raiseException = false;
       //Toggle below variable to disable and enable trigger on demand    	  
       public static boolean disableBeforeInsertContactTrigger = false;

Executable Code :

    List<Contact> contactList = new List<Contact>();        
    for(Integer i = 0 ; i < 200 ; i++ ){
        Contact con = new Contact();
        con.email = 'testEmail'+i+'@email.com';
        con.lastName = 'testLastName'+i;
        con.AccountId = accountList[i].id;   
        contactList.add(con);
    }
    // Making it true for some time to disable trigger.
    // Can be enabled any time later by making this false
    ContactTriggerHandler.disableBeforeInsertContactTrigger = true;
    insert contactList; 

#### Limits Understanding Test Classes
> We get 2 sets of limits in test classes. 
	> 1. Code outside Test.startTest() and Code inside Test.startTest()
	Below is POC for checking limits in both of those contexts  

##### Code / Solution  :  
    @isTest
    public static void testPopulateFieldsOnAccount(){
        List<Contact> contactList = [Select id , name ,email, AccountId,lastName 
        from contact order by AccountId];
        List<Account> accountList = [Select id ,First_Contact_Email__c,
        First_Contact_Name__c from account ]; 
        System.debug('Limits testing '+Limits.getQueries());
        System.debug('Limits testing Total'+Limits.getLimitQueries());
        Test.startTest();
        List<Contact> contactListAnother = [Select id , name ,email, AccountId,
        lastName from contact order by AccountId];
        System.debug('Limits testing inside'+Limits.getQueries());
        System.debug('Limits testing Total inside'+Limits.getLimitQueries());
        Test.stopTest();        
    }
Explanation : 
Below is log snippet for the following

    16:04:02:862 USER_DEBUG [30]|DEBUG|Limits testing 3
    16:04:02:862 USER_DEBUG [31]|DEBUG|Limits testing Total100
    16:04:02:864 USER_DEBUG [33]|DEBUG|Limits testing inside0
    16:04:02:864 USER_DEBUG [34]|DEBUG|Limits testing Total inside100

For testing purpose only query limits are printed. Please refer through Limits class salesforce for more limits.

#### Static variable and Test class
> Static variables lives within context. Whenever test method is started it starts new execution context But limits are persisted within different execution contexts of test class.
> In Below example a static variable is changed and printed in console. In test method the values are reset back to the original value.

##### Code / Solution  :  
    @isTest
    public class ContactTriggerHandlerTest {
        
        @testSetup static void createTestData(){                
    
    		System.debug('static '+ContactTriggerHandler.testStatic);
            ContactTriggerHandler.testStatic = 'Two';
            System.debug('static '+ContactTriggerHandler.testStatic);
        }
    
    @isTest
    public static void testPopulateFieldsOnAccount(){
        System.debug('static method '+ContactTriggerHandler.testStatic);

Below are debug logs

    16:53:53:219 USER_DEBUG [24]|DEBUG|static one
    16:53:53:219 USER_DEBUG [26]|DEBUG|static Two
    16:53:53:265 USER_DEBUG [31]|DEBUG|static method one

#### Trigger Scenario #2
> Consider a scenario where you want to query a set of contacts and as part of functionality make sure that if any of those contact belong to an account . The account has an annualRevenueForcast set.

Basic Implementation : 

    public static void queryAndSetAccountRevenue(){
        try{
            List<Contact> contactList = getContacts(10);
            Set<String> accountIds = new Set<String>();
            for(Contact con : contactList){
                if(con.AccountId != null){
                    accountIds.add(con.accountId);
                }
            }
            List<Account> accList = [Select id ,AnnualRevenue from account where 
            id IN :accountIds ];
            
            if(accList.size() > 0){
                accList = setAccountRevenueIfNotPresent(accList);                
            }
            //Update Acc
            update accList;
        }catch(Exception e){
            System.debug(e);
        }        
    }
    public static List<Account> setAccountRevenueIfNotPresent(List<Account> accList){
        for(Account acc : accList){
            if(acc.AnnualRevenue == null ){
                acc.AnnualRevenue = 500;
            }
        }
        return accList;
    } 

Here are stats for the code : 

    SOQL : 2 
    DML : 1 
    DML Rows  :  O(N)

##### Optimized for DML Rows
    public static void queryAndSetAccountRevenue(){
        try{
            List<Contact> contactList = getContacts(10);
            Set<String> accountIds = new Set<String>();
            for(Contact con : contactList){
                if(con.AccountId != null){
                    accountIds.add(con.accountId);
                }
            }
            // Adding a filter to add more selective query
            List<Account> accList = [Select id ,AnnualRevenue from account where 
            id IN :accountIds and AnnualRevenue = null];
            // Adding dml statement in to reduce DML in some cases
            if(accList.size() > 0){
                accList = setAccountRevenueIfNotPresent(accList); 
                //Update Acc
	            update accList;                
            }

        }catch(Exception e){
            System.debug(e);
        }        
    }
    public static List<Account> setAccountRevenueIfNotPresent(List<Account> 
    accList){
        for(Account acc : accList){
            if(acc.AnnualRevenue == null ){
                acc.AnnualRevenue = 500;
            }
        }
        return accList;
    } 

##### Reducing one SOQL query
> Above implementation works as well in larger code base where there is already existing query on contact object. we will just be extending that code for adding revenue feature
> In Below code implementation we will query on related account object with relationship fields and update the values late in single DML

    public static void queryAndSetAccountRevenue(){
        try{
            List<Account> accList = new List<Account>();
            List<Contact> contactList = getContacts(10000);
            Set<String> accountIds = new Set<String>();
            for(Contact con : contactList){
                if(con.AccountId != null && con.Account.AnnualRevenue == null){
                    System.debug(' account id '+
                    con.AccountId+ ' prev value '+con.account.annualRevenue);
                    // Change the reference here
                    con.account.annualRevenue = 500;
                    // Pass the object here 
					accList.add(con.account);
                }
            }

            if(accList.size() > 0 ){
                //Update Acc
                update accList;
            }
            
        }catch(Exception e){
            System.debug(e);
        }        
    }
Here in above example list is used to update values.
Consider a scenario where 10 contacts have same account we when using list we are adding duplicates in the list.

This can be solved by using Set or Maps
Below is map implementation 

    public static void queryAndSetAccountRevenue(){
        try{
            Map<String,Account> accMap = new Map<String,Account>();
            List<Contact> contactList = getContacts(10000);
            for(Contact con : contactList){
                if(con.AccountId != null && con.Account.AnnualRevenue == null){
                    System.debug(' account id '+con.AccountId+ ' prev value '
                    + con.account.annualRevenue);
                    // Change the reference here
                    con.account.annualRevenue = 500;
                    // Pass the object here 
    				accMap.put(con.AccountId,con.account);
                }
            }
            if(accMap.size() > 0 ){
                //Update Acc
                update accMap.values();
            }
            
        }catch(Exception e){
            System.debug(e);
        }        
    }

Code Stats  

    SOQL : 1
    DML : 1
    DML Rows : not O(N)

#### Benchmarking basics
> There should be a way to get performance of the written methods
> We can simply use Limits.getCPU time at start and end of methods to compute time.
> But performance can depend on various factors like input , number of iterations etc.
> To sum up we need to report avarage time taken for a method 

Please refer below Utility class for reporting benchmarkings 

Class : 

    public class BenchMarkingUtility {
        @testVisible
        private static Integer referenceStartTime ;
        @testVisible
        private static Integer referenceEndTime ;
        @testVisible
        private static Integer targetStartTime ;
        @testVisible    
        private static Integer targetEndTime ;
        @testVisible
        private static void markReferenceStart(){
            referenceStartTime = Limits.getCpuTime();
        }
        @testVisible
        private static void markReferenceEnd(){
            referenceEndTime = Limits.getCpuTime();
        }
        @testVisible
        private static void markTargetStart(){
            targetStartTime = Limits.getCpuTime();
        }
        @testVisible
        private static void markTargetEnd(){
            targetEndTime = Limits.getCpuTime();
        }
        @testVisible
        private static String reportResults(Integer loops){
            String result = '';
            Integer refDuration = referenceEndTime - referenceStartTime;
            Integer targetDuration = targetEndTime - targetStartTime;
            
            Integer slowerThanTarger = targetDuration - refDuration;
            
            Decimal eachItem = slowerThanTarger * 1000;
            eachItem /= loops;        
            eachItem.setScale(2);
            
        result = '#1 Reference Time : '+refDuration +'ms #2 Target Time : '+
        targetDuration +'ms #3 Slower Than Target : '+slowerThanTarger + ' ms or '
        +eachItem +' ms per operation ';
            return result;
        }
    }
Example : 
In below code we are computing benchmarking of incrementing an integer.
First loop doesnt have any operations to run. This is our ideal scenario [Fastest one]
In second Loop we are actually incrementing the variable.

We need time difference between those 2 to know how we are performing.

At the end method reports following.
`19:22:09:455 USER_DEBUG [61]|DEBUG|#1 Reference Time : 43ms #2 Target Time : 78ms #3 Slower Than Target : 35 ms or 3.5 ms per operation`

Reference loop runs for 43ms , Target Loop : 78 , 
Our loop is 35ms slower than the ideal

    @isTest
    public static void testBenchMarking(){
        Integer value = 0 ;
        BenchmarkingUtility.markReferenceStart();
        for(Integer i = 0 ; i < 10000 ; i++){
            
        }
        BenchMarkingUtility.markReferenceEnd();
        BenchMarkingUtility.markTargetStart();
        for(Integer i = 0 ; i < 10000 ; i++){
            value+=5;
        }
        BenchMarkingUtility.markTargetEnd();
        System.debug(BenchMarkingUtility.reportResults(10000));
    }

Logging Levels impact benchmarking results . Above benchmarking result is when Logging level is SFDC_DevConsole 

We need to change loging level to mimic how code runs in system.
Add a new level , with keeping every value as None only `Apex Code` property at `Error`

Results after changing debug level
`19:28:26:012 USER_DEBUG [39]|ERROR|#1 Reference Time : 3ms #2 Target Time : 6ms #3 Slower Than Target : 3 ms or 0.3 ms per operation ` 