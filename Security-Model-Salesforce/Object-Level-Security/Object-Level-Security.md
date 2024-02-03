# Object Level Security

## Questions

### What is Object Level Security ?
### How do we manage it ?
### Can we provide `read` on Object and `Edit` on fields ?
Yes , We can. Since object is read only even if we have edit on fields we wont be able to edit anything on object.
### What is FLS mechanism for formula field ? Can we change it ?
Since Formula fields are computed fields or expressions , We can only read those. FLS has to be set explicitly for providing access to user. 
### What is object security in case of `master-detail` objects ?
Refs : 
1. [User Permissions](https://help.salesforce.com/s/articleView?id=sf.permissions_about_users_access.htm&type=5)

### What is `View All and Modify All` ?
### What permissions does checking `Visible` provides on field ?
Refs: 
1. [Field Permissions](https://help.salesforce.com/s/articleView?id=sf.users_profiles_field_perms.htm&type=5)

### What happens if field is placed on `Page layout` and on `Lightening Record Page` but user doesn't have `Read` access to it ?

### Why use permission sets ?
### What is permission set group ? Why use them ?
### What is `session activation required` while creating permission set group ?
### How do we restrict Object and Field level access using permission set?
### What is muting permission set ? When to use ?
### How to create a muting permission set ?
### Does creation of it creates record in `PermissionSet` object ? 

### How to query all muting permission sets of an org ?

### What happens for this scenario ?

    There is a permission set named `permA` which has `industry` field with `edit` permission.

    There is a permission set group named `permGroupB` which has permission `permA` and `permC` as children in group.

    In muting permissions for `permGroupB` we have removed `industry` field's `edit` access , It is read only now.

    What happens if this permission set and permission set group is assigned to the user ? 

Muting only affects on the aggregations of the permissions of the group.

Permissions provided outside of the group remains as it is.

Here ,

`permA : edit`

`permGroupB : edit - edit = no edit`

In above case the user will still have `edit` on `industry` field via assigned permission set `permA` but not from `permGroupB`


### 2 users have same profile, but 1 user can `edit` a `field` on page layout, but another user can't. What could be the reason ?

### Does muting a permission in permission set group also mutes profile's permissions ?

No , It does not muting only affects the overall permission aggregation of the group. 

### What can `profiles` do over `permission set` ?
Refs : 
1. [User Permissions](https://help.salesforce.com/s/articleView?id=sf.permissions_about_users_access.htm&type=5)

### What are `Custom Permissions` ? Use case ?

### How to provide `Object and Field access` to guest users ? 

### How to query user's assigned permission sets ?

### How to query user's assigned permission set groups ?

### How to check user's object access programmatically ?

### How to check user's field access programmatically ?

### While creating fields can we set FLS on `permission sets` instead of `profiles` , If so how ?

### Can we assign `permission sets and groups` for specific time ? If so how ? 

## References
1. [Security Model Salesforce](https://developer.salesforce.com/blogs/developer-relations/2017/04/salesforce-data-security-model-explained-visually)
1. [Guidelines for Permission Sets](https://help.salesforce.com/s/articleView?id=sf.perm_sets_best_practices.htm&type=5)
1. [Data Access Main Thread - Recommended](https://help.salesforce.com/s/articleView?id=sf.security_data_access_mgmt.htm&type=5)
1. [User Permissions](https://help.salesforce.com/s/articleView?id=sf.permissions_about_users_access.htm&type=5)
1. [Field Permissions](https://help.salesforce.com/s/articleView?id=sf.users_profiles_field_perms.htm&type=5)
