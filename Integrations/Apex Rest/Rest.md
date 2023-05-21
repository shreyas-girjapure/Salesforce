# Representational State Transfer (REST)

## What is REST ?

REST API stands for Representational State Transfer Application Programming Interface. It is an architectural style that allows two computer systems to communicate with each other over the internet.

A REST API uses HTTP requests to `GET`, `POST`, `PUT`, and `DELETE` data to and from a server. Each request is considered as an independent operation, which means that the server does not store any information about the previous requests made by a client.

## How to expose an end point in salesforce to say `Hi Mom!` ?

You can expose apex methods and salesforce data as a rest service

1. Create a class declared as `global`
1. Declare the class as `RestResource` with `urlMapping`.
   1. The URL mapping will be the actual endpoint, where your service can be accessed publicaly.
1. Declare method which you want to expose with `global` and `static` keywords.
1. Use `HttpGet` or relevant annotations per your need.
1. Now yoour end point is publicaly available at
   `YOUR_ORG_DOMAIN/services/apexrest/v1/sayHello`
1. Below is a sample snippet of a class
1. Sample Code

```Java
@RestResource(urlMapping='/v1/sayHello/*')
global class ApexRestPractice {

    @HttpGet
    global static String sayHello(){
        return 'Hi MOM!';
    }

}
```

Considerations :

1. A Class or URL mapping can only have one GET or any other method of its type.
1. Methods and class must be declared as global to be accessed from public network.

## How to access query parameters of apex rest endpoint ?

`RestContext` Class provides various properties and respective methods to cover more scenarios with request and response structures.

One of the Property is `RestRequest`.
This class provides request related infromation and provides useful methods for manipulating them.

Now lets change `sayHello` endpoint to pass any name from query parameter

```Java
@RestResource(urlMapping='/v1/sayHello/*')
global class ApexRestPractice {

    @HttpGet
    global static String sayHello(){
        String finalString = 'Hi Mom ';
        RestRequest request = RestContext.request;
        String nameParam = request.params.get('name');
        if(!String.isBlank(nameParam)){
            finalString += 'from '+nameParam;
        }
        return finalString;
    }

}
```

## How to send status code and error message in response from apex rest ?

`RestContext` has `RestResponse` which has status codes and response body. This can be used to send custom body as output and status code.

Different status code ranges indicates various responses. Custom status codes can be used to make endpoints more robust.

Now lets make sure user is sending query parameter mandatorily in the request.
If user doesn't send query parameter then we will throw error and a custom error message to let user know query parameter is mandatory.

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
            response.responseBody = Blob.valueOf('What is this behaviour !?');
            return;
        }
        finalString += 'from '+nameParam;
        response.responseBody = Blob.valueOf(finalString);
    }

}
```

## How to use `HttpPost` ?
We can also create `POST` endpoint similarly to add some external data into our web service.
We can manipulate posted data however desired.
`Example` : Save into record , Transform and perform some actions on it.

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
            return acc;
        }
        catch(Exception e){
            System.debug('THe exception '+e);
        }
    }
```
In above example we are taking 2 parameters , namely `accName` and `accDesc` from body of the request.
The HTTP request body must have JSON key names similar to methods parameter names.

Sample HTTP Request : 

```http
POST /https://d2w00000kn3veeat-dev-ed.develop.my.salesforce.com/services/apexrest/v1/sayHello HTTP/1.1
Host: example.com
Content-Type: application/json

{
"accName" :"newAcc",
"accDesc" : "acc Desc"
}
```
This returns HTTP Response code `200` indicating transaction was successful.  
## Use Wrapper class in apex request for posting complex data
We can send data in the `@HttpPost` with as many variables as want as input parameters.

Passing many variables into method is not considered as best partice.

Instead we can use custom wrapper class to pass desired data into single parameter.

```Java
@RestResource(urlMapping='/v1/postHere')
global class ApexRestPost {

    @HttpPost
    global static String saveData(AccountInformation accInformation){
        String finalResponse = '';
        try{
            Account acc = accInformation.accountRecord;       
            insert acc;
            finalResponse = acc.id;
            return finalResponse;
        }catch(Exception e){
            System.debug('Exception '+e);
        }
        return finalResponse;
    }
}
```
In above method we have created class named `AccountInformation` , This class needs to be `global` marked to make it visible to external systems.

We can declare the class in same `ApexRestPost` class or we can create a new file named `AccountInformation`
```Java
global class AccountInformation {
    Account accountRecord;
    String sayHello;
}    
```
The class externally represented as following JSON structure

```JSON
{
    "accInformation": {
        "accountRecord": {
            "name": "firstAccoiiint",
            "Description": "desccc"
        }
    },
}
```
in above example we have not passed `sayHello` parameter in JSON , experimenting that not all parameters of the class needs to be mandatorily passed from JSON.

There are other ways to handle external body data dynamically , using `JSON` class's `serialize` and `deserialize` methods.

`Integration Frameworks` are designed with one of the above methods to keep code clean and readable

## What is difference between `post` , `put` and `patch` ?

In the context of HTTP methods, `POST`, `PUT`, and `PATCH` are used for different purposes when interacting with a server. Here's an explanation of each method along with an example:

1. POST (Create):

   1. The `POST` method is used to submit data to the server to create a new resource.
   1. It is non-idempotent, meaning multiple identical requests may lead to different outcomes.
   1. The server typically generates a new resource identifier and returns it in the response.
   1. Example: Creating a new user by submitting user details to the server.

    ```http
    POST /api/users HTTP/1.1
    Host: example.com
    Content-Type: application/json

    {
    "name": "John Doe",
    "email": "john.doe@example.com"
    }
    ```

2. PUT (Replace):

   1. The `PUT` method is used to replace the entire resource at the specified URL with the payload sent in the request.
   1. It is idempotent, meaning multiple identical requests will have the same outcome.
   1. If the resource doesn't exist, it may be created. If it exists, it will be completely replaced.
   1. Example: Updating a user's details by replacing the existing user resource.

   ```http
   PUT /api/users/123 HTTP/1.1
   Host: example.com
   Content-Type: application/json

   {
     "name": "Jane Smith",
     "email": "jane.smith@example.com"
   }
   ```

3. PATCH (Modify):

   1. The `PATCH` method is used to apply partial modifications to an existing resource.
   1. It is generally used when you want to update specific fields or properties of a resource.
   1. The payload typically contains only the changes that need to be applied.
   1. Example: Updating only the email address of a user without modifying other properties.

   ```http
   PATCH /api/users/123 HTTP/1.1
   Host: example.com
   Content-Type: application/json

   {
     "email": "updated.email@example.com"
   }
   ```

Note : the specific implementation and behavior of these methods can vary depending on the server-side framework or API you are working with.

## How to send XML as Response and Request ?
Similar to JSON format , we can send XML formatted data over HTTP for further manipulation.
To do so we need to change header `content-type` for the request.

`Content-Type : application/xml`
This will let server know incoming data is xml formatted.

Now , request body should something like this.
```xml
<request>
    <accName>someAccountName</accName>
    <accDesc>some Account Description</accDesc>
</request>
```
## How to write unit tests for apex rest methods ?
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

## References
- [Apex Rest](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_rest.htm)
- [Rest Context](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex_methods_system_restcontext.htm)
- [Rest Request](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex_methods_system_restrequest.htm)
- [Rest Response](https://developer.salesforce.com/docs/atlas.en-us.apexref.meta/apexref/apex_methods_system_restresponse.htm)
- [Chat-GPT](https://chat.openai.com/)
