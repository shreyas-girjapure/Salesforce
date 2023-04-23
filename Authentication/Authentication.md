# Authentication and Authorization

## What is `Authentication` ?
Authentication is the process of confirming the identity of a user, device, or system. It is a security mechanism used to ensure that the entity trying to access a resource or service is who or what it claims to be. Authentication is important for protecting sensitive information and preventing unauthorized access to systems and networks. It can be achieved through various methods, such as passwords, biometrics, smart cards, tokens, and multi-factor authentication. The ultimate goal of authentication is to establish trust and ensure that only authorized individuals or systems are granted access to the resources they need.

## What are types of `Authentication` in salesforce ?
Salesforce supports various types of authentication methods, including:

1. `Username and Password`: This is the most common method of authentication in Salesforce. Users enter their username and password to log in.

2. `Single Sign-On (SSO)`: SSO allows users to access multiple applications with a single set of login credentials. Salesforce supports SSO through several identity providers, including Active Directory, Okta, and Ping Identity.

3. `Social Sign-On`: Social Sign-On allows users to log in using their social media accounts, such as Facebook, LinkedIn, and Twitter.

4. `Certificate-Based Authentication`: Certificate-based authentication uses digital certificates to authenticate users. It is commonly used in enterprise environments.

5. `OAuth`: OAuth is an open standard for authentication and authorization. It allows users to grant third-party applications access to their Salesforce data without sharing their login credentials.

6. `Two-Factor Authentication (2FA)`: 2FA adds an extra layer of security to the authentication process by requiring users to provide a second form of identification, such as a code sent to their mobile device.

Salesforce also offers additional authentication options for mobile devices, such as Mobile Identity, which enables users to log in using a fingerprint or a PIN code.w

## What is `Authorization` ?
Authorization is the process of determining whether a user or client application has the necessary permissions to access a specific resource or perform a specific action in a system. In Salesforce, authorization is implemented using access control mechanisms such as permissions, roles, profiles, and sharing rules.

The following are the different types of authorization in Salesforce:

`Object Level Authorization`: This type of authorization is based on the user's access to individual records. It is controlled through record-level sharing settings and can be managed using the role hierarchy, sharing rules, and manual sharing.

`Field Level Authorization`: This type of authorization is based on the user's access to specific fields on a record. It is controlled through field-level security settings and can be managed using profiles and permission sets.

`System Level Authorization`: This type of authorization is based on the user's access to system-level features such as reports, dashboards, and setup. It is controlled through system permissions and can be managed using profiles and permission sets.

`Apex Authorization`: This type of authorization is based on the user's access to custom Apex classes, triggers, and Visualforce pages. It is controlled through Apex sharing settings and can be managed using profiles and permission sets.

`API Authorization`: This type of authorization is based on the user's access to Salesforce's REST and SOAP APIs. It is controlled through connected app settings and can be managed using profiles and permission sets.

## What is `OAuth` and What is `OAuth 2.0` Flow ?
OAuth 2.0 is an authorization framework that allows third-party applications to access protected resources on behalf of a resource owner (usually a user) without the need for the resource owner to share their credentials with the third-party application. The OAuth 2.0 flow is a series of steps that an application must go through to obtain an access token from a server that provides OAuth 2.0 authorization services.

There are several types of OAuth 2.0 flows, but the most common is the Authorization Code flow. The steps involved in this flow are as follows:

1. The user initiates the flow by clicking on a button or link that redirects them to the authorization server.
2. The application sends an authorization request to the authorization server, which includes the client ID, redirect URI, and scope.
3. The user logs in to the authorization server (if they haven't already) and grants permission to the application to access their resources.
4. The authorization server sends an authorization code to the application's redirect URI.
5. The application sends a token request to the authorization server, which includes the authorization code, client ID, client secret, and redirect URI.
6. The authorization server validates the token request and sends an access token and optional refresh token back to the application.
7. The application can now use the access token to access protected resources on behalf of the user.

There are variations of this flow, such as the Implicit flow, the Resource Owner Password Credentials flow, and the Client Credentials flow, which are used in different scenarios based on the requirements of the application and the resource owner.

## What is a `Connected app` ? Why do we use it ?
A Connected App is a framework in Salesforce that allows third-party applications to integrate with Salesforce securely. It provides a way for external applications to authenticate with Salesforce using OAuth 2.0 and access data in Salesforce on behalf of the authenticated user. 

Connected apps can be used for a variety of purposes, such as:

1. `Integration with external systems`: Connected apps can be used to integrate Salesforce with external systems such as marketing automation tools, e-commerce platforms, and other business applications.

2. `Custom user interfaces`: Connected apps can be used to create custom user interfaces that interact with Salesforce data and functionality, such as a mobile app or a custom portal.

3. `Single Sign-On (SSO)`: Connected apps can be used to implement SSO across multiple systems, allowing users to log in to Salesforce and other systems using a single set of credentials.

4. `API access`: Connected apps can be used to access Salesforce's REST and SOAP APIs, allowing external applications to read and write data in Salesforce.

When you create a Connected App in Salesforce, you define a set of OAuth policies that control how the app can access Salesforce data and what permissions it has. You also specify a callback URL that the authentication server redirects to after the user authorizes the app.

Once a Connected App is set up, external applications can use OAuth 2.0 to authenticate with Salesforce and obtain an access token, which can be used to access Salesforce data on behalf of the authenticated user.

## What are different grant types available in salesforce ?
Salesforce supports several grant types for obtaining an access token through OAuth 2.0 authentication. Here are the most common grant types available in Salesforce:

1. `Authorization Code Grant`: This is the most commonly used grant type in Salesforce. It involves the user being redirected to the Salesforce login page, where they authenticate and authorize the connected app to access their Salesforce data. After successful authentication and authorization, the connected app receives an authorization code, which it then exchanges for an access token.

2. `Implicit Grant`: This grant type is used when the connected app needs to obtain an access token without a client secret, such as when the app is a JavaScript application running in a browser. With the Implicit Grant, the access token is returned directly to the client application after successful authentication and authorization.

3. `Username-Password Grant`: This grant type allows the connected app to obtain an access token by exchanging the user's Salesforce username and password. This grant type is typically used for server-to-server integrations or in cases where a user interface is not available.

4. `Client Credentials Grant`: This grant type is used by connected apps that need to access resources in Salesforce without a user context, such as when accessing metadata or static resources. With the Client Credentials Grant, the connected app obtains an access token using its own client ID and secret.

5. `JWT Bearer Token Grant`: This grant type is used when the connected app needs to access Salesforce data on behalf of a user without the user having to explicitly authorize the app. With the JWT Bearer Token Grant, the connected app creates a JSON Web Token (JWT) and exchanges it for an access token.

These are some of the most common grant types available in Salesforce. The specific grant type used depends on the type of application and the specific requirements of the use case.

## How to get access token using Authorization code grant type ?

1. Identify type of sandbox you want to log in to. For Developer instances `https://login.salesforce.com` and for sandboxes `https://test.salesforce.com`
1. Create a connected app 
    1. Get `client id / client key` 
        1. `3MVG9n_HvETGhr3CP0CGtYT.i.ixvsTVAJT.xgI7Scuo5qn46te0ElKck4E3pFVGVs5Gy1vGvDA==`
    1. Get `redirect url` , this is `callback url` of your connect app.
        1. `https://www.google.co.in/`
1. `https://login.salesforce.com/services/oauth2/authorize?response_type=code&client_id=3MVG9n_HvETGhr3CP0CGtYT.i.ixvsTVAJT.xgI7Scuo5qn46te0ElKck4E3pFVGVs5Gy1vGvDA==&redirect_uri=https://www.google.co.in/
`
1. This URL asks for authentication / login. Once logged in auth code is sent to the redirect url or callback url.
1.  We got this URL back in response `https://www.google.co.in/?code=aPrxUHjncL9lYc6doDJGt3VZjXHDUwq6IPUl7Qkv.9LOQ3No_qIbe3gny3g3S39SSSbHx9wuKQ%3D%3D`
1. Copy the code `aPrxUHjncL9lYc6doDJGt3VZjXHDUwq6IPUl7Qkv.9LOQ3No_qIbe3gny3g3S39SSSbHx9wuKQ%3D%3D`
1. Now this auth code can be used to get access token.
1. Token is requested from different url `https://login.salesforce.com/services/oauth2/token
`
1. This endpoint needs a post body in following format.
`grant_type=authorization_code&client_id=<your_client_id>&client_secret=<your_client_secret>&redirect_uri=<your_redirect_uri>&code=<your_authorization_code>
`
1. There are 2 ways to hit the requested body
    1. From API client with body in post message
    1. From URL headers
1. Final URL to get token `https://login.salesforce.com/services/oauth2/token?grant_type=authorization_code&code=aPrxUHjncL9lYc6doDJGt3VZjXHDUwq6IPUl7Qkv.9LOQ3No_qIbe3gny3g3S39SSSbHx9wuKQ%3D%3D&client_id=3MVG9n_HvETGhr3CP0CGtYT.i.ixvsTVAJT.xgI7Scuo5qn46te0ElKck4E3pFVGVs5Gy1vGvDA==&client_secret=9F5BD54997AB1BC09AD98D0F991B1A2A73B8F244AD97D517C737C282FBFD9494&redirect_uri=https://www.google.co.in/`
1. In the end we get token and other relevant information in the response.
1. With token we can access data from salesforce with `Authorization: Bearer <access_token>
` header inputs

## How to get access token using Username and Password grant type ?
1. Identify type of sandbox you want to log in to. For Developer instances `https://login.salesforce.com` and for sandboxes `https://test.salesforce.com`
1. Create a connected app 
    1. Get `client id / client key` 
        1. `3MVG9n_HvETGhr3CP0CGtYT.i.ixvsTVAJT.xgI7Scuo5qn46te0ElKck4E3pFVGVs5Gy1vGvDA==`
    1. Get `Client Secret` 
        1. `9F5BD54997AB1BC09AD98D0F991B1A2A73B8F244AD97D517C737C282FBFD9494`
    1. Get `redirect url` , this is `callback url` of your connect app.
        1. `https://www.google.co.in/`
1. `https://login.salesforce.com/services/oauth2/token?grant_type=password&client_id=3MVG9n_HvETGhr3CP0CGtYT.i.ixvsTVAJT.xgI7Scuo5qn46te0ElKck4E3pFVGVs5Gy1vGvDA==&client_secret=9F5BD54997AB1BC09AD98D0F991B1A2A73B8F244AD97D517C737C282FBFD9494&username=shreyasneworg@salesforce.com&password=<yourPassword>`
1. Use postman to get access token from this method and store in for further requests.