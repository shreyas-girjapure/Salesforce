# Row Level Security

# Questions

## What is row level sharing ?
## What are various ways to share records from strict to loose ?
## OWD Sharing ?
The first step in record-level security is to determine the organization-wide sharing settings for each object. Organization-wide sharing settings specify the default level of access that users have to each others’ records.

You use organization-wide sharing settings to lock your data to the most restrictive level. Use the other record-level security and sharing tools to selectively give access to other users. For example, users have object-level permissions to read and edit opportunities, and the organization-wide sharing setting is Read-Only. By default, those users can read all opportunity records, but can’t edit any unless they own the record or are granted other permissions.

Refs : 
1. [Control Who Sees What - Recommended](https://help.salesforce.com/s/articleView?id=sf.security_data_access.htm&type=5)
## What is `public/read/write/transfer` ?
1. 	All users can view, edit, transfer, and report on all records. Only available for cases or leads.
1. For example, if Alice is the owner of ACME case number 100, all other users can view, edit, transfer ownership, and report on that case. But only Alice can delete or change the sharing on case 100.

Refs : 
1. [All OWD Levels ](https://help.salesforce.com/s/articleView?id=sf.sharing_model_fields.htm&type=5)

## What is `Public Full Access` ?
1. All users can view, edit, transfer, delete, and report on all records. Only available for campaigns.
1. For example, if Ben is the owner of a campaign, all other users can view, edit, transfer, or delete that campaign.

Refs : 
1. [All OWD Levels ](https://help.salesforce.com/s/articleView?id=sf.sharing_model_fields.htm&type=5)

## Role Hierarchy ?
## Sharing Rules ?
1. Owner-Based Sharing Rules
    1. An owner-based sharing rule opens access to records owned by certain users. For example, a company’s sales managers need to see opportunities owned by sales managers in a different region. The U.S. sales manager could give the APAC sales manager access to the opportunities owned by the U.S. team using owner-based sharing.
1. Criteria-Based Sharing Rules
    1. A criteria-based sharing rule determines with whom to share records based on field values. For example, you have a custom object for job applications, with a custom picklist field named “Department.” A criteria-based sharing rule could share all job applications in which the Department field is set to “IT” with all IT managers in your organization.

Refs : 
1. [Sharing Types](https://help.salesforce.com/s/articleView?id=sf.security_sharing_rule_types.htm&type=5)
## Manual Sharing ?
## Apex Sharing ? Is manual sharing and apex different ?
1. The organization-wide default settings can’t be changed from private to public for a custom object if Apex code uses the sharing entries associated with that object. For example, if Apex code retrieves the users and groups who have sharing access on a custom object Invoice__c (represented as Invoice__share in the code), you can’t change the object’s organization-wide sharing setting from private to public.
## How to restrict records ? What are Restriction Rules ?
## What are scoping rules ?
## Teams Sharing ?
## What is Guest User Sharing ?
A guest user sharing rule is a special type of criteria-based sharing rule and the only way to grant record access to unauthenticated guest users.

## Public Groups and Queues ?
## Does `View All` and `Modify All` overrides sharing settings ?
## How to share records with guest users ?
## How to Remove shared access ?
## What kind of access do roles up in hierarchy get ? Can they edit and delete if object access is provided ?
1. Users above a record owner in the role hierarchy can only view or edit the record owner’s records if they have the “Read” or “Edit” object permission for the type of record.
1. Yes , The roles up in the hierarchy can delete if object permissions are in place. 
## Can owner delete their records even if he doesn't have object level permission of `delete`? 
## What happens to manually shared and apex shared records when owner is changed ?
## How to Prevent Manual sharing ?
## Can we programmatically create criteria based and owner based sharing rules ?


## References
1. [Security Model Salesforce](https://developer.salesforce.com/blogs/developer-relations/2017/04/salesforce-data-security-model-explained-visually)
1. [Control Who Sees What - Recommended](https://help.salesforce.com/s/articleView?id=sf.security_data_access.htm&type=5)
1. [Sharing Settings Main thread - Recommended](https://help.salesforce.com/s/articleView?id=sf.managing_the_sharing_model.htm&language=en_US&type=5)
1. [All OWD Levels ](https://help.salesforce.com/s/articleView?id=sf.sharing_model_fields.htm&type=5)
1. [Sharing Types](https://help.salesforce.com/s/articleView?id=sf.security_sharing_rule_types.htm&type=5)
1. [Sharing Architecture Guide](https://architect.salesforce.com/fundamentals/platform-sharing-architecture)
1. [Record Level Access Under the Hood - Recommended](https://developer.salesforce.com/docs/atlas.en-us.salesforce_record_access_under_the_hood.meta/salesforce_record_access_under_the_hood/uth_preface.htm)
1. [Restriction Rules](https://help.salesforce.com/s/articleView?id=sf.security_restriction_rule.htm&type=5)
1. [Scoping Rules](https://help.salesforce.com/s/articleView?id=sf.security_scoping_rule.htm&type=5)
1. [Apex Security And Sharing](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_sharing_chapter.htm)
1. [Insufficient Privileges Error](https://help.salesforce.com/s/articleView?id=sf.admin_insufficient_privileges_flowchart.htm&type=5)
