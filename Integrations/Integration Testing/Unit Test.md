# Unit Test

## How to write unit tests for `Apex Rest` methods ?
To mimic external system access behaviour we have to focus on setting `RestContext` ,
In test context , test methods use `RestContext` to continue on method execution.

Set any values which are required from external systems in the context manually.

For example : 
```Java
@RestResource(urlMapping='/v1/sayHello/*')
global class ApexRestPractice {

    @HttpGet
    global static void sayHello(){
        String finalString = 'Hi Mom ';
        RestRequest request = RestContext.request;
        RestResponse response = RestContext.response;
        String nameParam = request.params.get('name');
        if(String.isBlank(nameParam)){            
            response.statusCode = 400;
            response.responseBody = Blob.valueOf('What is this behaviour !? ');
            return;
        }
        finalString += 'from '+nameParam;
        response.responseBody = Blob.valueOf(finalString);                        
    }
}
```
Here method takes input from query parameter and executes further.

now to test this we need to set the query parameter in the `RestRequest` and then assign the `RestRequest` in the `RestContext`.

Similarly since we are also using `RestResponse` to send customized response , we will also be needing to assign `RestResponse` in the `RestContext`

Test method now should be looking like below ,
```Java
@istest
public class ApexRestPracticeTest {
    @istest
    public static void testGetSayHelloMethod(){
        RestRequest req = new RestRequest();
        RestResponse res = new RestResponse();
        
        req.httpMethod = 'GET';
        //Adds query parameter
        req.addParameter('name','shreyas');            
        
        RestContext.request = req;
        RestContext.response = res;
        Test.startTest();       
        ApexRestPractice.sayHello();
        Test.stopTest(); 
    }
}
```

Similarly for `POST` methods we can pass data directly to the method for mimicing request body data;

```Java
@HttpPost
global static void createAccount(String accName , String accDesc){
    System.debug(' the name '+accName);
    System.debug(' the desc '+accDesc);
    try {
        Account acc = new Account();
        acc.Name = accName;
        acc.Description = accDesc;
        insert acc;    
        
    }
    catch(Exception e){
        System.debug('THe exception '+e);            
    }
}
```
below is a test method for above function

```Java
@istest
public static void testPostCreateAccountMethod(){
    RestRequest req = new RestRequest();
    req.httpMethod = 'POST';                        
    RestContext.request = req;        
    
    Test.startTest();
    ApexRestPractice.createAccount('someName','someDesc');
    Test.stopTest(); 
}   
```
