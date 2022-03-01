# Vendasta Identity access via OAuth2 3-Legged Flow

## Overview

This guide aims to help you configure your application to use Vendasta’s Identity Gateway as an Identity Provider for your application. This will allow you to identify users entering your application from the Vendasta platform and will in future API iterations allow you to call Vendasta APIs on their behalf.

Vendasta implements the [OpenID Connect Core specification](https://openid.net/specs/openid-connect-core-1_0.html), which includes a complete [OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749) integration.

This guide is for implementing a **3-legged OAuth** flow. The distinction between 2-legged and 3-legged flows is as follows:

|                                             | **Access Level Provided**  | **Setup Required** |
|---------------------------------------------|----------------------------|--------------------|
| **2-legged flow (Partner Service Account)** | Access to high-level Vendasta workflow APIs, but no access to **user data** or **business data**. | Allocating a Partner Service Account. Configuring an OAuth2 2-legged client.             
| **3-legged flow (this guide)**              | Access to **user data** and **business data** associated with the user performing the flow.       | Follow this guide. Requires an end user to initiate the flow to provide access to the app. |

## Step 1: Choosing the type of implementation for your App

The first decision you will need to make is where you will implement your authorization flow:

1. **(Recommended)** Backend workflow: implement the workflow within your secure webserver
2. Frontend workflow: implement the workflow using Javascript within a Single Page Application

**We recommend implementing your authorization flow on the backend in most cases as it is easier to setup, secure, and use. However, Single Page Applications would likely prefer a Frontend Workflow so that tokens can be more easily refreshed.**

Both OpenID Connect and OAuth2 are standardized workflows, which means most programming languages have at least one library available to aid your implementation whether you choose to implement the workflow on the frontend or on your backend webserver.

<!-- theme: info -->
>You can find a list of compliant OAuth2 libraries [here](https://oauth.net/code/). Please select a **Client Library** in your language of choice.
>
>Whichever client library you select, please consult the included documentation for the library of your choice to supplement this guide.

### Backend Clients:

Clients running on a backend web-server will use their **client secret** for verification.

### Frontend Clients:

* Frontend clients should use the [PKCE flow](https://developer.okta.com/blog/2019/08/22/okta-authjs-pkce/?utm_campaign=text_website_all_multiple_dev_dev_oauth-pkce_null&utm_source=oauthio&utm_medium=cpc), it is recommended that you find an OAuth2 library which supports this feature.
* The **implicit grant flow** is considered insecure and should be avoided. 

## Step 2: Configuring your Client & Library

### Configuring your integration in Vendor Center

Your integration is configured in Vendor Center, under Integration > SSO Settings, including the Entry URL, Logout URL, and OAuth2 configuration which will provide the client ID and client secret.

![Session Settings Diagram](../../assets/images/guides/sso/sso_marketplace_settings.png)

|||
|-|-|
| **Entry URL**  | User is redirected to this url when they click on the Product icon in the End User dashboard. The account_id is injected into the placeholder on the url, and should be passed on to the Authorization URL to trigger the start of SSO. 
| **Logout URL** | A webhook for back channel logout of service provider session. 






### Client Configuration

Follow your client’s installation guide, entering the following information when prompted:

|                                  |                                                                              |
|----------------------------------|------------------------------------------------------------------------------|
| **Issuer**                       | `https://iam-prod.apigateway.co`                                             |
| **Authorization URL**            | `https://sso-api-prod.apigateway.co/oauth2/auth?account_id=<account_id>` (see Contextualizing your Auth URL below)                                                                              |
| **Token URL**                    | `https://sso-api-prod.apigateway.co/oauth2/token`                            |
| **Logout/End Session URL**       | `https://sso-api-prod.apigateway.co/oauth2/logout`                           |
| **JWKS URL (optional)**          | `https://iam-prod.apigateway.co/oauth2/v1/certs`                             |
| **User Info URL (optional)**     | `https://sso-api-prod.apigateway.co/oauth2/user-info`                        |
| **Client ID**                    | Obtained when creating your OAuth2 configuration within the Vendasta Platform|
| **Client Secret (backend only)** | Obtained when creating your OAuth2 configuration within the Vendasta Platform|


## Step 3: Session Transfer Workflow

## Authorization URL Configuration
The library that you chose to use likely had a function for generating the OAuth Authorization URL.



### Contextualizing your Auth URL

Vendasta users may belong to any one of our many partners, and each partner may select one of many different login methods. Additionally user access is white-labeled. The branding to be displayed to them can be by `market_id`.
Thus in order to direct the user to the login screen that is specific to their account, we need to know which account they are attempting to access.
Most OAuth2 libraries will allow you to add additional context to your Authorization URL with query parameters, before initiating an OAuth2 flow, provide options to your OAuth2 library to set the account ID to the `account_id` query parameter.

### Prompts

Our Authorization URL supports each of the following values for the `prompt` query parameter

|||
|-------------|--------------------------|
| **none**    | Do not prompt the user for consent or to log in. Will result in an error if the user does not have an active session or has not previously provided consent. This is useful for acquiring fresh tokens for a user who has logged in recently and likely has an active session. |
| **login**   | Prompt the user to log in, ignoring an existing session if one exists.  |
| **consent** | Prompt the user to provide consent for accessing the requested scopes. `prompt=consent` is required when requesting the `offline_access` scope. |





### Scopes

In order to call APIs on behalf of a user, you must specify which **scopes** your app needs access to. Consult the APIs you wish to call to determine which scopes you will require, and include these scopes in your OAuth2 client’s configuration step.

### Special Scopes

Certain scopes have a special meaning, or are distinct from those used for API access.

|                 |                                                                                                 |
|-----------------|-------------------------------------------------------------------------------------------------|
| `openid`        | **Required:** this scope MUST be included when performing an OpenID connect flow.               |
| `offline_access`| This scope requests that a **refresh token** be returned to your application alongside your **access token**. See the **Refresh Token** section for more information.                                           |
| `profile`       | Grants access to view a user’s name, locale, and Vendasta roles using the **user-info** endpoint. See the **User Info Endpoint** section for more information.                                                        |

### Access Tokens

Access tokens expire **30 minutes** from the time of issue. They will need to be refreshed regularly using one of the following techniques:

- **Redirect the user back through the authorization process (backend)**. If the user’s token has expired, or is close to expiry, you can fetch a new one by re-initiating the OAuth2 authorization flow. You may pass the `prompt=none` query parameter for users who have already given consent to avoid displaying the consent dialog again, which should allow a seamless redirection back into your application.

- **Silent Refresh (Frontend):** This technique redirects the user through the authorization process just as the previous technique, however it is initiated from Javascript by opening a hidden browser window for the authorization flow, using prompt=none to complete the flow without user interaction. Thiss refreshes the user’s token without any interruption.

- **Use a refresh token (Backend):** Refresh tokens allow applications to refresh access tokens at any time, even after the user has left their application. This is useful, but requires explicit consent from the end-user and is not recommended unless your application needs to perform background work on behalf of the end-user. Refresh tokens are sensitive and must be kept secure.

**All access token claims are subject to change without notice, and should only be inspected for debugging purposes.**

### ID Tokens

An identity token will be provided in the response from the token endpoint. Identity tokens are compliant with the [OpenID Core ID Token specification](https://openid.net/specs/openid-connect-core-1_0.html#IDToken). 

In addition to the core ID token claims, they will also include requested identity data claims such as the [OpenID Standard Identity Claims](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims). The ID Token will also be given any requested identity scopes (such as `email`, which provide `email` and `email_verified` identity claims), so it can be used via the `Authorization` header when calling the user-info endpoint.

ID tokens are valid for 30 days.

Per the OpenID specification, ID tokens are [**JWT**](https://jwt.io/introduction/) encoded. When troubleshooting your integration you can use [https://jwt.io/](https://jwt.io/) tool to inspect the contents.

Here is an example payload of an ID token after decoding, note that fields may be absent if unavailable:

```
{
  "iss": "https://iam-prod.apigateway.co",
  "sub": "U-e7851c53-8a49-4c89-90fd-7a8140888c02",
  "aud": "<my_client_id>",
  "exp": 1311281970,
  "iat": 1311280970,
  "name": "Jane Doe",
  "given_name": "Jane",
  "family_name": "Doe",
  "gender": "female",
  "birthdate": "1990-10-31",
  "email": "janedoe@example.com",
  "email_verified": false,
  "phone_number": "+1 (425) 555-1212",
  "phone_number_verified": false,
  "locale": "en-US",
  "updated_at": 1311220970
}
```

### Refresh Tokens

Refresh tokens allow an application to acquire fresh **Access Tokens** as-needed, even when the end-user is no longer actively engaged with their application. This allows applications to perform **background processing** on behalf of the user.

Refresh Tokens are **not granted** by default and must be explicitly requested by the application by meeting the following requirements:

- Request the `offline_access` scope in your OAuth2 Auth requests
- Set the `prompt=consent` query parameter on your Auth requests (most clients provide this option, or will do so automatically if they are sufficiently OpenID connect aware)

Once a refresh token is obtained you may store it in your database for later use. **Do not store refresh tokens in the browser via local-storage as this is vulnerable to XSS attacks.** Consult your OAuth2 library to learn how to acquire, store, and load refresh tokens as required.

### User Info Endpoint

The User Info Endpoint is available to any application which has acquired an Access Token from an OAuth2 flow. However, the information it provides is scaled according to the **scopes** on your access token.

Here is the User Info URL:

    https://sso-api-prod.apigateway.co/oauth2/user-info

You may make a `GET` or `POST` request to this endpoint. Include your Identity Token in the **AUTHORIZATION** header like so, replace the token with your own:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL2lhbS1wcm9kLmFwaWdhdGV3YXkuY28iLCJleHAiOjE2MTExNjEyMDQsImlhdCI6MTYxMTE1OTQwNCwiaXNzIjoiaHR0cHM6Ly9pYW0tcHJvZC5hcGlnYXRld2F5LmNvIiwic3ViIjoiVS1lNzg1MWM1My04YTQ5LTRjODktOTBmZC03YTgxNDA4ODhjMDIiLCJmZWRlcmF0ZWRfaWRlbnRpdHlfcHJvdmlkZXIiOiJvYXV0aCIsInVzZXJfaWQiOiJVLWU3ODUxYzUzLThhNDktNGM4OS05MGZkLTdhODE0MDg4OGMwMiIsImtpbmQiOiJhY2Nlc3MiLCJzY29wZSI6InByb2ZpbGUgZW1haWwgb3BlbmlkIiwia2lkIjoiOWZjMGEyYWNlMTNjZjdjOTBiM2ZmZjcxODkzZGJjODAiLCJlbWFpbF92ZXJpZmllZCI6dHJ1ZX0.s12fKo19qKBR4CjMlXqkV0KxksieY7jlgT7Ryg9PH68
```

The amount of information returned is determined by the scopes which your application requested during its authorization flow as follows:

|           |                                                                                                      |
|-----------|------------------------------------------------------------------------------------------------------|
| `email`   | **Email is currently excluded from this implementation.** Please use the `sub` field returned by the user-info endpoint to uniquely identify the user.                                                                  |
| `profile` | Grants access to view a user’s name, locale, and Vendasta roles using the user-info endpoint. See the User Info Endpoint section for more information.                                                                   |
| `openid`  | Include the User ID (a.k.a. `sub`), and namespace.                                                   |

**All user info claims aside from those specified in the [OpenID Core specification](https://openid.net/specs/openid-connect-core-1_0.html#UserInfoResponse) are subject to change without notice and should only be used for debugging purposes.**

```
{
    "sub": "U-b10e6de6-ee43-429c-ac26-b3cb81ef6f5f",
    "email": "mary@example.com",
    "email_verified": true,
    "namespace": "ABC",
    "name": "Mary Smith",
    "given_name": "Mary",
    "family_name": "Smith",
    "locale": "en",
    "updated_at": 1611083679,
    "namespace": "ABC",
    "roles": [
        "partner",
        "sales_person",
        "digital_agent",
        "smb"
    ],
    "legacy_user_id": "UID-1234",
    "created_at": 1566245652,
    "product_navbar_data_url": "https://www.smblogin.com/api/user/U-b10e6de6-ee43-429c-ac26-b3cb81ef6f5f/products-navigation-bar-data/"
}
```
<div class="background-accent remember">

**Security note**

From OIDC spec point [5.3.2](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) - "NOTE: Due to the possibility of token substitution attacks (see [Section 16.11](https://openid.net/specs/openid-connect-core-1_0.html#TokenSubstitution)), the UserInfo Response is not guaranteed to be about the End-User identified by the sub (subject) element of the ID Token. The sub Claim in the UserInfo Response MUST be verified to exactly match the sub Claim in the ID Token; if they do not match, the UserInfo Response values MUST NOT be used."

</div>


## Step X - Dashboard Modifications

### Navigation Bar

The Vendasta NavBar provides a seamless navigation experience for users to switch between your marketplace application, other applications, and their Business Center.  User logout will also be served by this bar, and hidden from your dashboard. **Implementation of the Navigation Bar is required if your integration includes SSO.**

![](../../assets/images/guides/sso/navbar.png)

Include the script tag below before the closing body tag of your HTML to allow for rendering of the Navigation bar. **All** the data- attributes in the script tag are required to be passed as when loading the application. The Navbar should load on every page of the application.

It is recommended that you build the entire script tag before injecting it into the page rather than injecting the custom params individually. This will avoid a race condition where the script runs before the parameters are present, resulting in an empty navbar frame.

**HTML TEMPLATE**

```html
<!-- include the following script tag before the closing body tag to render the navigation bar in your app -->
<script src="//www.cdnstyles.com/static/product_navbar/v1/product_navbar.js"
        data-url="{{ product_navbar_data_url }}"
        data-account-id="{{ account_id }}"
        data-app-id="{{ app_id }}">
</script>

OR if including target-element-class

<!-- include the following script tag before the closing body tag to render the navigation bar in your app -->
<script src="//www.cdnstyles.com/static/product_navbar/v1/product_navbar.js"
        data-url="{{ product_navbar_data_url }}"
        data-account-id="{{ account_id }}"
        data-app-id="{{ app_id }}"
        target-element-class="{{target_element_class}}">
</script>
```

Script parameter details:

|||
|-|-|
| `vendasta.com/marketplace/product_navbar_data_url`| This is the endpoint the product navbar script will use to retrieve the data to populate the Vendasta navigation bar. To be retrived via the OIDC User-Info endpoint.
| `data-account-id`                                 | The unique ID for a Vendasta Account. This is passed to the Service Provider via the Entry URL. 
| `data-app-id`                                     | The unique ID for a Vendor Product. Found in the Vendor Center URL when in the context of a Product.
| `target-element-class(optional)`                  | This field can help overcome css conflicts with the NavBar. It allows you to specify an element that has a **unique** class. If the element with this class exists on the page at the time the NavBar is called to render the bar, it will place the bar directly above the target element|

## Testing