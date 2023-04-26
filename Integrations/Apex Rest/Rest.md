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
            response.responseBody = Blob.valueOf('What is this behaviour !? ');
            return;
        }
        finalString += 'from '+nameParam;
        response.responseBody = Blob.valueOf(finalString);                        
    }

}
```
## How to post data to an endpoint ?

## What is difference between `post` , `put` and `patch` ?
## How to send XML as Response and Request ?
## Use Wrapper class in apex request for posting complex data 
## How to write unit tests for apex rest methods ?

## References