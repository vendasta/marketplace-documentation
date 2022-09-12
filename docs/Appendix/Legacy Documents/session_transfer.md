# Session Transfer Introduction

_Our previous session transfer implementation has been deprecated in favor of [OAuth2](docs/Guides/oauth2.md). Please use OAuth2 going forward._

Seamless Session Transfer is one of the most important components of any Marketplace application. It ensures a seamless flow from the Vendasta Marketplace to the application and back again. Marketplace Applications are only permitted to use the user’s session to store the user’s ID and locale. Any other information must be available in the URL itself, which will produce a contextual url which can identify the target resource if a User bookmarks the url, or accesses it in an unauthenticated manner.

While _Seamless Session Transfer_ may sound complicated, in practice it is a simple token exchange that can be accomplished with two HTTPS endpoints and some session middleware. The next sections illustrate the required two HTTPS endpoints. *Note that non-SSL endpoints are not supported*.

## Session Middleware

Modification or addition to your application's session middleware will be required. The modifications required to support the Entry URL & Session URL should be fairly minor. Alernatively you could maintain a modified mirror application that is accessed on a seperate domain or path. See below for the default Session Transfer sequence.

![Session Transfer Diagram](https://storage.googleapis.com/wordpress-www-vendasta/developers/2019/Vendasta_Marketplace_SSO.png)

<h2 id="session-transfer-workflow">Session Transfer Workflow</h2>

1. A Vendasta Business Center user clicks the Vendor’s app icon. This results in a 302 redirect to the _Entry URL_ of the App. The accountId will be injected wherever the <accountId> placeholder exists in the URL registered in Vendor Center.
2. The app _Entry URL_ checks to see if there is a [session cookie](/vendors/session-middleware#session-cookie) in place, and if so checks server side for existing/valid Vendasta user session. This is checking against the session_id that was passed to the _Session URL_, and stored by the Vendor.
    * If a session cookie exists
        1. Vendor checks for a valid server side session for their product - if there isn't a valid session, Vendor redirects to _Session Transfer URL_
        2. Vendor checks that the User has access to the resource being requested. See the _'Check User Access to an Account'_ endpoint in the [User API](https://developers.vendasta.com/swaggerui).
        3. If a valid session exists for Vendor Application, and access checks pass: Vendor redirects to landing page, or nextUrl if present
        4. If the user authorization check fails, vendor displays an Error Page indicating user doesn't have access to requested resource
<!-- theme: warning -->
> Even for legacy sso flows, we no longer suggest using a session cookie. We suggest that you overwrite any existing sessions upon each entry, and thus skip step two. Note that if access is accomplished via `impersonation` that the first call to the Entry URL will now be skipped, and the first call the Vendor receives in the flow will be to the Session URL. Thus do not rely on caching anything from the first call to the Entry URL. Any caching of data for future use should be done in the second call to the Entry URL w/code.
3. If no Vendasta User session currently exists:
    * The app *Entry URL* uses the Marketplace API to fetch the _Session Transfer         URL_ for the Account via the _'Get Session Transfer URL'_ endpoint in the [Account API](https://developers.vendasta.com/swaggerui)
    * To make calls to the Marketplace API, the Vendor must first retrieve an authorization token via the OAuth endpoint in the [Marketplace API](https://developers.vendasta.com/swaggerui)  
4. The result of the call made in step 3 returns the Vendasta *Session Transfer URL*. The Vendor app redirects to this URL.
    * **Note: If you see a 500 lvl error in the UI after the redirect to the _Session Trasnfer URL_, this is generally caused by to your** _Session URL_ **being unavailable, timing out, or providing an invalid response to the Session Transfer URL's call, see Step 8.**
5. Vendasta's _Session Transfer URL_ ensures that there is a valid Business Center User session, and if not directs the user to log in. Once a Business Center session has been confirmed Vendasta calls the Application's _Session URL_ server to server, passing Session Data as claims in a JWT Token  - token is passed as raw text in the POST body, similar to the webhooks.
6. The App _Session URL_ retrieves and decodes the JWT claims and check if the **iss** (issuer) is _'Vendasta Marketplace'_. It could also be _'Vendasta Marketplace Test'_ if sent from the webhook testing tool.
7. If the issuer is Vendasta then the JWT is ingested by the Vendor and the signature validated with the Vendasta Public Key - located [here](/vendors/web-hooks).
8. The App *Session URL* stores the JWT claims and return a JSON response as follows:

    ``` json
    {
        "code": some-unique-hash-value
    }
    ```

9. Vendasta's _Session Transfer URL_ then 302 redirects to the _Entry URL_ of the App with the code the Vendor _Session URL_ provided as a query param, and the _nextUrl _if present.
10. The app _Entry URL_ checks the code against the JWT claims stored earlier and verifies if the code is valid. If the code is valid, the user session is established, and session cookie created. Application should error on invalid/expired code. The code should have a maximum life of 60 seconds.
11. Now that Session Transfer has succeeded, before redirecting to the requested resource, the Vendor must check if User has access to the resource being requested. See the _'Check User Access to an Account'_ endpoint in the [User API](https://developers.vendasta.com/swaggerui#/user).
12. If the user has access to the requested resource, the user is redirected to the App’s landing page **or the nextUrl location if provided.**

**Note:** Its a good idea to store the JWT claim with the code as the key of the claim entity. This comes in handy for checking the validity of the code when the Vendasta session transfer URL calls the app’s _Entry URL_ passing the code as a url param.

## Entry URL

When a User clicks 'Launch' on a Product banner in their 'My Products' page of the Business Center, they are redirected to the Product's *Entry URL*. This is a 302 client side redirect that will initiate a Transfer of the Vendasta User's Session to the Vendor Product.

![Business Center - My Products Page](https://storage.googleapis.com/wordpress-www-vendasta/developers/2019/VBC_MyProducts_Launch.jpg)

*Note that the Product icon only shows up for a User once your product has been provisioned for the Account.*

The *Entry URL* is the only URL in the session transfer process that accepts the *code* parameter, which is exchanged for a session cookie. Thus it must also support a *nextUrl* parameter to allow the User to log in, exchange a code, and then return to the page they were originally trying to view. 

## Configuration

The Entry URL is configured in the SSO URLs tab of the Integration page in Vendor Center
![](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/08/Vendor-Center.jpg)

## Session Cookie

The session cookie allows for instant identification of an existing session, so session transfer may be skipped to improve loading times. The presence of a session cookie prompts the Vendor to check for an existing session in their system matching the session_id of the cookie. If there is a valid session, the user may be redirected to the requested resource(if they are authorized to access it). If no session is found, or the session the cookie references is expired session transfer should be initiated by retreiving and redirecting to the _Session Transfer URL_. Once a session is established a new cookie should be generated with the value set to the session_id supplied in the JWT claims to the App's _Session URL_.

## Parameters

### **code** Url Parameter (optional)

The *Entry URL* being redirected to with the *code* as a url param indicates to the App that session transfer has already been initiated by the Vendasta authentication system. If it is present, then the client making the request is requesting a session cookie in exchange for the specified *code*. The *code* should be short lived - 60s. It is important that the *code* not be accepted beyond its expiry and that it be valid only for a single use.

**Note:** Looking forward to the role of the *Session URL*, It's a good idea to initially store the JWT claim provided to your *Session URL* with the `code` as the key of the claim entity. This comes in handy when the Vendasta *Session Transfer URL* redirects to the App’s *Entry URL* with the *code* as a url param. It allows the Vendor to check the validity of the `code`, and at the same time easily access the JWT claim fields that are to be used by the Entry URL. Once the code is verified this entry can then be destroyed, removing the code, and saving any claim parameters needed for future use.

### **nextUrl** - Url Parameter (optional)

Because only the *Entry URL* endpoint supports the exchange of codes, this parameter is necessary so the user can get to any page in the application through the *Entry URL*. The main purpose of the nextUrl is for deep linking into your product from the various Vendasta Reports. If the nextUrl is passed to your *Entry URL*, you must append it to the *Session Transfer URL* as a url param before redirecting to it. Once your *Entry URL* has confirmed a valid session, and that the User has access to the requested resource, it should redirect to the nextUrl if provided.

### **accountId** (required)

When a User is redirected to the *Entry URL*, the accountId will be injected into the placeholder in the *Entry URL* - this placeholder is configured when registering the url in Vendor Center. The *Entry URL* is templatized to accept this parameter as part of the url path, or as a url parameter. The accountId is required to be present on the url as it indicates what resource is being requested. The Vendor will maintain a mapping between the Marketplace accountId and the unique resource within the Vendor Product belonging to that Account.

**Sample Code**

```python
from flask import Flask, request, render_template, session, redirect
app = Flask(__name__)

def load_existing_session():
    session_data = None
    # Check client side
    session_id = request.cookies.get('session_id', None)
    # Check server side
    if session_id in session:
        session_data = session[session_id]
    return session_data

@app.route('/entry/<account_id>/')
def app_entry(account_id):
    new_cookie, code, next_url = None, None, None

    # Check for existing session
    existing_session = load_existing_session()
    if not existing_session:
            # Check for a code (good for a session exchange)
            code = request.args.get('code')
            if code and code in session:
                    new_session = session.pop(code)
                    user_id = new_session['user_id']
                    session_id = new_session['session_id']
                    # Ensure user has access to account being requested
                    if check_user_permissions(user_id, account_id):
                            new_cookie = session_id
                            session[user_id] = new_session
                    else:
                            return redirect('accessError.html')
            else:
                    # If there is no session, and no code 
                    # The Session Transfer URL should be called to force a login
                    return redirect(fetch_sessiontransfer_url(account_id))

    # a valid session, make sure to pass navbar JS URL, navbar data URL, account_id
    # and app_id to the context of your application
    session_data = new_session if new_session else existing_session
    context = {
            'product_navbar_data_url': session_data['vendasta.com/marketplace/product_navbar_data_url'],
            'app_id': get_marketplace_app_id(),
            'account_id': account_id
    }

    next_url = request.args.get('next_url')
    url = next_url if next_url else 'your_app_landing_page.html'
    response = render_template(url, **context) 

    if new_cookie: 
     response.set_cookie('session_id', new_cookie) 
    return response
```

## Session URL

The session URL is used by the Vendasta authentication framework for passing sessions along to applications. This is to be an HTTPS endpoint that accepts a raw JSON POST body. The JSON body follows the [JWT](http://jwt.io/) format. There are number of parameters specified in the claims section that provide details about the session to be created. The session URL must generate a unique single-use code for callers. The code is good for a one-time exchange for a session, the details of which are defined in the JWT claims. This endpoint is exclusively for server-to-server communication and uses public key cryptography for security.

**Note:** It's a good idea to initially store the JWT claim provided to your *Session URL* with the *code* as the key of the claim entity. This comes in handy when the Vendasta *Session Transfer URL* calls the App’s *Entry URL* with the *code* as a url param. You are then able to validate the code provided is the one that was issued for this session transfer.

**iss**

This is one of the standard claims in a JWT. For all requests from the vendasta marketplace use a issuer id of: "Vendasta Marketplace"

**iat**

This is the time that the JWT was issued. It is a UTC UNIX epoch timestamp in seconds. It is used for confirming that the exp value is reasonable.

**exp**

This is the time that this particular JWT expires (ie. should not be honoured anymore). If the current time is after the time in the exp, the request should be denied. Due to the servers involved not being synchronized with extreme precision, an exp value of 60s is used by the Vendasta system. 

**vendasta.com/marketplace/session_id**

This is a unique session identifier that is used by the Vendasta marketplace to reference the specified user session. It is imporant to keep track of this identifier on the user’s session because in the event that the user’s session is destroyed (expiry or logout), the Vendasta authentication system will send a webhook notifying the application and will pass this identifier.

**vendasta.com/marketplace/lang**

The ISO 639-2 code for the user’s language (ie. ‘eng’ for English). This parameter is included for all sessions, but will only be relevant if the application supports internationalization. If an application does not support the specified language, it is acceptable to default to English.

**vendasta.com/marketplace/user_id**

This is the user_id of the user for which a session-exchange code is being created. Note that user_ids are unique in the Vendasta system.

**vendasta.com/marketplace/partner_id**

This is the id for the Partner that the user for which a session-exchange code is being created belongs to.

**vendasta.com/marketplace/product_navbar_data_url**

This is the URL for retrieving vendasta navigation bar data. It’s required by the Vendasta NavBar to correctly display the list of accessible products to the user for whom the session is being created.

**Sample Code**

```python
from flask import Flask, request
app = Flask(__name__)

@app.route('/session/', methods=['POST'])
def app_session():

    # Extract the JWT from the POST body (don't validate it yet)
    data = request.get_data(as_text=True)
    claims = jwt.decode(data, verify=False)

    # Determine that it is indeed from Vendasta Marketplace
    issuer = claims['iss']
    if issuer != 'Vendasta Marketplace':
        return "Unknown issuer %s" % issuer

    # Confirm that it is signed correctly
    try:
        jwt.decode(data, verify=True, key=vendasta_public_key)
    except jwt.InvalidTokenError:
        return "Invalid Signature"

    # Create a random code and store the claim info with it
    code = uuid.uuid4().hex
    codes[code] = claims

    # Return the code
    return json.dumps({'code': code})
```

The data sent to your Session URL will be a raw JWT token in the body of the POST

Example JWT:

```text
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZW5kYXN0YS5jb20vbWFya2V0cGxhY2UvcGFydG5lcl9pZCI6IlZORFIiLCJ2ZW5kYXN0YS5jb20vbWFya2V0cGxhY2Uvc2Vzc2lvbl9pZCI6IjM3ZjE0Y2U1MzBlN2U4MGUyNDMxZjhlNTliZGJhY2IyM2I5NDQxODFmYjZjMGIyNmM5MjJhNDkwMzljZjJlNTgiLCJleHAiOjE1MTA3ODEyOTQsInZlbmRhc3RhLmNvbS9tYXJrZXRwbGFjZS9wcm9kdWN0X25hdmJhcl9kYXRhX3VybCI6Imh0dHBzOi8vbWFya2V0cGxhY2UtdmVuZG9ycy5zbWJsb2dpbi5jb20vYWpheC92MS9wcm9kdWN0cy1uYXZpZ2F0aW9uLWJhci1kYXRhL2dldC8iLCJpc3MiOiJWZW5kYXN0YSBNYXJrZXRwbGFjZSIsImlhdCI6MTUxMDc4MTIzNCwidmVuZGFzdGEuY29tL21hcmtldHBsYWNlL2xhbmciOiJlbmciLCJ2ZW5kYXN0YS5jb20vbWFya2V0cGxhY2UvdXNlcl9pZCI6IlVJRC1mMzE5ZWI4Yi1lYjQ3LTQ3ZmMtYWU1Yi0xMzZmNDlhM2RhMWEifQ.Q-cmKgi94XCEu54-23HxaZ1EkW0zyEZOVZIMC6FQKEvc5eF_TDwVtPVEWZcFsaV5wLFaT4VQ4DT_uFNU2OlTQo4VLL598Bdn2QJhqVL1oXrFLOzR-6i2LDgnrTM6AGfVf7JUOj53UHQL770LwmQS5qEZ7iNOqyrT1vGCIpTm8uM6j3XQlFSGkzKNjPfP5rtttp228sRms8Q7LEZRivOTWERNhaL-l1zMTUm00AcU10atiSxHsYM6Gym6ZYltY-7neIrtIOchC70UFTUWivxk8yq2UdYvscclM42afA1ZPBPZwGsS7ZQYposRI22SQorstROq5znceR22uhYKLPZYbg
```

Use tool on [jwt.io](https://jwt.io) homepage to test decode using Marketplace Public Key:

```text
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAqcJZvV3x8dC3q0Rpsu0M
Qby7z4k+iwKHpxV+rlSa7+jpr/xQknlH7ZrMmlcONCteaIZtk92g+QTOnchE9rIC
06TSoaLcpHpaoIpoeALZrgnIwfMMi/G0gRJ5xjuv6sc3QHsQVGf43ClOISPPoJ4t
91lPX/Gm+PvGHDonMpJZdFZyZRkCqBS/rHur+rwnbn1dcFI6iwzyFrQPMRpHx58W
vgFDy9VsCKypMmv5X6TF4KRMfZRmB+avBRWzp9tficzF9hQm7aekyYstUMm76jwD
uC4SYhRy1dsBQ0S8SgcNQR4hBsx5UxERE8uknBW1TtvoyaqMp/HNWZRCNnuDNcfH
/QIDAQAB
-----END PUBLIC KEY-----
```

## Navigation Bar

The Vendasta NavBar provides a seamless navigation experience for users to switch between your marketplace application, other applications, and their Business Center.  User logout will also be served by this bar, and hidden from your dashboard.

![](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/03/NavBar2-1024x160.jpg)

## Pre-requisites

To integrate the navigation bar into your marketplace application following pre-requisites must be met:

1. The Entry URL for the application is set.
2. The Session URL for the application is set.
3. A valid public key for the application is set.

## Workflow

The following highlights the steps from the [Session Transfer](/vendors/session-transfer) process that directly relate to implementing the Vendasta Navigation Bar. The *Entry URL* & *Session URL* both have a roll in providing data to the Navigation Bar:

1. **Entry URL:** A Vendasta User clicks the vendor’s app icon. Vendasta will redirect to the *Entry URL* of the app, inserting the accountId wherever the <accountId> placeholder exists in the *Entry URL* registered in Vendor Center.
2. *... Flow continues*
3. **Session URL:** Vendasta's *Session Transfer URL* calls the *Session URL* set by the vendor in the integration settings of the app, passing Session Data in the form of a JWT Token raw in the POST body.
4. **Session URL: Your application’s Session URL handler should make sure to store the [JWT claims](https://jwt.io/introduction/#payload) that you will require later. The important claim for the Navigation Bar is the **_vendasta.com/marketplace/product_navbar_data_url_.
5. **Entry URL:** Vendasta's *Session Transfer URL* then 302 redirects to the *Entry URL* of the app with the code the Vendor *Session URL* provided as a query param.
6. **Entry URL:** Your application validates if the code with the HTTP request is valid, if the code is valid then load your application, and pass along the following to your application context:
    * **app_id**: Your marketplace application id.
    * **account_id**: The account id provided in the contextualized Entry URL.
    * **product_navbar_js**: This is a static URL where the Javascript code for the Vendasta navigation bar lives. Make sure to use protocol-relative path.
    * **product_navbar_data_url**: This is the URL for accessing the data to populate the navigation bar available in JWT claims
    * **target-element-class(optional): **This field can help overcome css conflicts with the NavBar. It allows you to specify an element that has a **unique **class. If the element with this class exists on the page at the time the NavBar is called to render the bar, it will place the bar directly above the target element.

## Implementation

Include the script tag below before the closing body tag of your HTML to allow for rendering of the Navigation bar. **All** the data- attributes in the script tag are required to be passed as context when loading the application. The Navbar should load on every page of the application. The passing of the context is part of the logic your *Entry URL* will handle, so see the *Entry URL* [example code](/vendors/session-middleware#sample-code) for details.

### HTML TEMPLATE

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
