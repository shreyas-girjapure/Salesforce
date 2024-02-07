# Object Level Security

# Questions

## What is Object Level Security ?
Object level security is base line security provided by salesforce to manage tables and their user access.
There are below configurations available for user to have access of an Object or Table.
1. Read
    1. Makes row accessible if row is shared / user has row level access.
1. Edit
    1. Edit makes row editable , if user has row level access.
    1. Enabling `Edit` auto enables `Read`.
1. Create
    1. Does it only helps in new record creation ??
    1. Enabling `Create` auto enables `Read`.
1. Delete
    1. Provides delete access on rows of objects only if user has row level access.
1. View All
    1. Overrides sharing rules for the object. Marking this `true` makes all rows of this object available to be `read`. 
1. Modify All
    1. Overrides sharing rules for the object. Marking this `true` makes all rows of this object `editable`.

Profiles and Permission set are the only way to manage this setting from.
## How do we manage it ?
We can only manage this from profiles and permission sets.
## Can we provide `read` on Object and `Edit` on fields ?
Yes , We can. Since object is read only even if we have `edit` on fields we wont be able to edit anything on object.
## What is FLS mechanism for formula field ? Can we change it ?
Since Formula fields are computed fields or expressions , We can only read those. FLS has to be set explicitly for providing access to user.
## What is object security in case of `master-detail` objects ?

1. You can control CRUD on detail and master object and its fields via profile and Permission Sets
 
Refs : 
1. [User Permissions](https://help.salesforce.com/s/articleView?id=sf.permissions_about_users_access.htm&type=5)

## What is `View All and Modify All` ?
1. `View All` setting on object overrides sharing settings for that object and provides read access to all users who has this permission. 
1. `Modify All` setting on object overrides sharing settings for that object and provides edit access to all users who has this permission.
## What permissions does checking `Visible` provides on field ?
While creating fields users get asked for `Visible` and `Read Only` Checkbox for permissions.
Checking on `Visible` provides `read and Edit` access on the field in the mentioned profile

Refs: 
1. [Field Permissions](https://help.salesforce.com/s/articleView?id=sf.users_profiles_field_perms.htm&type=5)

## What happens if field is placed on `Page layout` and on `Lightening Record Page` but user doesn't have `Read` access to it ?
If user doesn't have read access to the field but record detail page has the field. Then field is not displayed and empty space is displayed.

## Why use permission sets ?
permission set provides granular and reusable set of permissions.
1. They are more flexible.
1. They are package-able.
1. They are upgradeable too.
1. User can have multiple permission sets.

having such building block can help you establish security model you want for your business case.
## What is permission set group ? Why use them ?
1. Permission set group provides grouping of modular permission sets.
1. It makes assignment and assignment management easy.
1. Muting is also available while using groups.

## What is `session activation required` while creating permission set group ?
1. Need help for use case and more characteristics ?
1. This is for providing control of permissions over limited session login.
1. this is different from duration based permission set and Permission set group assignments.
## How do we restrict Object and Field level access using permission set?
Using muting permissions in permission set group.
## What is muting permission set ? When to use ?
1. Muting permissions is salesforce feature which can be used to restrict some of the permissions from permission set group.
1. This is limited to permission set group only and has effect only on cumulative permissions of the group.

## Does creation of it creates record in `PermissionSet` object ?
1. Muting permission sets created in groups are not visible as separate permissions in salesforce.
1. There is separate `MutingPermissionSet` object where they are managed.

## How to query all muting permission sets of an org ?
1. `select id , DeveloperName from MutingPermissionSet` 
## What happens for this scenario ?

>    There is a permission set named `permA` which has `industry` field with `edit` permission.

>There is a permission set group named `permGroupB` which has permission `permA` and `permC` as children in group.

>In muting permissions for `permGroupB` we have removed `industry` field's `edit` access , It is read only now.

>What happens if this permission set and permission set group is assigned to the user ? 

Muting only has effects on the aggregations of the permissions of the group.
Permissions provided outside of the group remains as it is.

Here ,

`permA : edit`

`permGroupB : edit - edit = no edit`

In above case the user will still have `edit` on `industry` field via assigned permission set `permA` but not from `permGroupB`

## 2 users have same profile, but 1 user can `edit` a `field` on page layout, but another user can't. What could be the reason ?
1. This definitely has to do with object level security
1. The user which has access must be getting extra field permission from either permission set or permission set group. 
## Does muting a permission in permission set group also mutes profile's permissions ?

1. No , It does not muting only has effects on  the overall permission aggregation of the group. 

## What can `profiles` do over `permission set` ?
1. Login IP ranges
1. Page layout assignments
1. Login hours

Refs : 
1. [User Permissions](https://help.salesforce.com/s/articleView?id=sf.permissions_about_users_access.htm&type=5)

## What are `Custom Permissions` ? Use case ?

Refs : 
1.  [Custom Permissions](https://help.salesforce.com/s/articleView?id=sf.custom_perms_overview.htm&type=5)

## How to provide `Object and Field access` to guest users ?
Refs : 
1. [Steps](https://help.salesforce.com/s/articleView?id=sf.rss_config_guest_user_profile.htm&type=5)

## How to query user's assigned permission sets ?
1. `select id , AssigneeId , PermissionSetId from PermissionSetAssignment`

## How to query user's assigned permission set groups ?
1. `select id , AssigneeId , PermissionSetGroupId from PermissionSetAssignment `
## How to check user's object access programmatically ?

1. `select id ,EntityDefinition.DeveloperName,IsReadable, IsCreatable, IsEditable, IsDeletable,  UserId , DurableId  from UserEntityAccess where UserId = '0052x000002fJUZAA2' and EntityDefinition.DeveloperName = 'Account' limit 1000 `
1. Or use `SObject.isEditable()` clauses in apex program.
1. Or use `ObjectPermission` and `PermissionSetAssignment` object to programmatically get permissions

## How to check user's field access programmatically ?
1. `select id ,EntityDefinition.DeveloperName,IsReadable, IsCreatable, IsEditable, IsDeletable,  UserId , DurableId  from UserFieldAccess where UserId = '0052x000002fJUZAA2' limit 1000`
1. Or use `ObjectPermission` and `PermissionSetAssignment` object to programmatically get permissions
1. Or Use `Schema.sObjectType.ObjectAPI.fields.FieldAPI.isUpdateable()` etc 

## While creating fields can we set FLS on `permission sets` instead of `profiles` , If so how ?
1. Yes, There is setting in user management setting which allowes us to do so.

## Can we assign `permission sets and groups` for specific time ? If so how ?

1. Yes , There is provision in permission set and group access management to provide periodic access.
## What is deployment status `In Development` and `Deployed` on object setting ?
1. Objects with `In Development` can only be seen by users with `Customize Application Permission`.
1. Objects with `Deployed` status are available for all users with read access.

Refs : 
1. [Deployment Status Object](https://help.salesforce.com/s/articleView?id=sf.deploying_custom_objects.htm&type=5)

## References
1. [Security Model Salesforce](https://developer.salesforce.com/blogs/developer-relations/2017/04/salesforce-data-security-model-explained-visually)
1. [Guidelines for Permission Sets](https://help.salesforce.com/s/articleView?id=sf.perm_sets_best_practices.htm&type=5)
1. [Data Access Main Thread - Recommended](https://help.salesforce.com/s/articleView?id=sf.security_data_access_mgmt.htm&type=5)
1. [User Permissions](https://help.salesforce.com/s/articleView?id=sf.permissions_about_users_access.htm&type=5)
1. [Field Permissions](https://help.salesforce.com/s/articleView?id=sf.users_profiles_field_perms.htm&type=5)
1. [Custom Permissions](https://help.salesforce.com/s/articleView?id=sf.custom_perms_overview.htm&type=5)
1. [Deployment Status Object](https://help.salesforce.com/s/articleView?id=sf.deploying_custom_objects.htm&type=5)
