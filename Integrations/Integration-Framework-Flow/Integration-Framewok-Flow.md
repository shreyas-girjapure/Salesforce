## Integration Framework

The integration framework is used for managing outbound connections for SF org.

General idea of integration framework is to provide
1. Ease of new service onboarding.
1. Ease of monitoring.
1. Separation of concerns in aspect of only generating request and parsing the response.


### Implemented Structure

1. Object Structure
1. Trigger Framework
1. Handling Outbound calls

### Object Structure
1. Integration_Message__C - Object which manages rows of Integration logs
    1. Request__c - textarea (131072)
        1. Request body that is supposed to be sent in outbound call
    1. Response __c - textarea (131072)
        1. Response body that is supposed to be sent in outbound call
    1. Status__c - picklist (255)
        1. Based on status changes automations are triggered, example on `New` status Request Generators are called and request body is constructed.
    1. Outbound_API__c - Boolean
        1. Outbound APIs are used when SOAP apis are to be used. Mule has exposed a hook where fields of Integration Message are posted. 
    1. Document_API__c - Boolean
        1. Document API check is used when rest callouts are to be made.
    1. SVCNAME__c - picklist (255)
        1. Service or which is in question , example : AADHAR_NAME_MATCH , WEATHER_API etc
1. Integration Callout custom setting
    1. Custom Setting Records are used to mark Integration Callout Records , Which has following mapping
        1. Service_Name__c
        1. Request_Generator_Class__c
        1. Response_Processor_Class__c
    1. The Request generators and response processors are used to parse salesforce data into JSON and from JSON to apex wrappers

### Trigger Framework

1. Invocation of a service
Rows/Instances of Integration_Message__c are inserted into DB , Primary required fields are 
    1. Integration_Message__c message = new Integration_Message__c(
            Status__c='New',
            SVCNAME__c='DMS',
            Document_API__c = true
        );
    1. Upon insertion on such records , before insert is triggered and Request generation processes is kicked in.
    1. Before insert trigger is used to generate JSON structure to be sent as body.
    1. After request generation is completed the status is changed to `In-Progress`.
    1. Once the status is changed to `In-Progress` it is then picked up for outbound callouts. 
1. Integration messages are sent to external system using `queue` based system.
1. `In-Progress` Messages are sent to queue using `System.Enqueue()`
1. `HTTPRequest` and `HTTPResponse` classes are used along with named credentials of `mulesoft`
1. Once response is received it is parsed using the custom setting's  `Response_Processor_Class__c`
