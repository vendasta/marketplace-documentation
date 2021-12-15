# Webhooks

All of the webhooks used in the Vendasta marketplace take security and privacy as primary concerns. Thus Marketplace webhooks are sent over HTTPS, and payloads are signed by the Vendasta Marketplace so applications can verify the authenticity of all payloads. In order to make this process secure, extensible, and simple for developers we have chosen to use the [JWT Format](https://jwt.io/introduction/) signed by an RSA key for the Marketplace Webhooks.

## Registering Webhooks

Webhooks are registered by placing your url in the Integration page of your App in Vendor Center. API registration is not available.

![vendor-center-webhooks](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/03/VDC_Webhooks-1024x461.jpg)

## Receiving Webhooks

**Payload Details**

``` text
content type: text/plain
body: JWT signed base64 string sent as plaintext raw in the POST body
```
Auth0 has libraries for most popular languages to help speed up integrations that interact with JWTs: [https://jwt.io/#libraries-io](https://jwt.io/#libraries-io)


**Validation**

You should always verify that the JWT issuer is 'Vendasta Marketplace', or 'Vendasta Marketplace Test' for webhooks sent using the Vendor Center testing tool. For verifying any JWT's sent to your endpoints by Vendasta, the public key for the Vendasta Marketplace is:

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

You can quickly test whether a token's signature is valid using the [jwt.io debugger](https://jwt.io) used in Step 1. Enter the Marketplace Public key into the public key box on the right, and then re-paste the token into the Encode text area. You will see the graphic below change to 'Signature Verified'.


**Testing**

The best way to initially test receiving a purchase payload from Vendasta is to use the Testing Page in Vendor Center. The Testing Page will load the Purchase Webhook URLs you have entered in the Integration Page by default, but you can set them to whatever url you would like.

For your first test, try a service like [Webhook.Site](https://webhook.site) or [RequestBin by Pipedream](https://requestbin.com/). Copy the unique url they give, and enter it into the on the testing page, and select "SEND TEST APP WEBHOOK":

![](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/10/webhooktesting.jpg)

Review the results on the Webhook Tester page. It will have received a dummy payload. To see the contents, place the JWT token into the Encoded text area of the [jwt.io debugger](https://jwt.io). You will see that it indicates that you have an Invalid Signature since no Public Key is in place yet.

## Webhook Timing
If your App has Add-ons configured, and the App is activated at the same time as one or more Add-ons, the webhook for the parent App will always arrive first. Payloads to your configured endpoint of any subsequent Add-ons on the order will arrive as little as 500ms apart. This is regardless of whether this was multiple instances of a multi-purchasable Add-on, or activations of seperate Add-ons.

### Message Delivery Failure

**Timeout OR 500 level response status**

|Webhook|Timeout |Retry
|:---|:---|:---
|*Purchase Webhooks* | 30s | Backoff time doubles from 30s to a  maximum spacing of one hour(aprox*)
| *All other Webhooks*| 10s | Backoff time doubles from 60s to a  maximum spacing of one hour(aprox*)

If your system is down, the retry mechanism will allow for you to receive the message once it is back up. Otherwise, it will serve to alert you that a problem exists without inundating your endpoint with traffic.

**Purchase Webhooks: 300-400 level response status**

There will be no retry for the purchase webhooks when called with the 'action' value of 'provisioned'. The Partner will be alerted that the activation attempt failed. If your App Activation may be rejected for expected reasons please manage this through the Pending Activation Workflow rather than via webhook response.

![activation-rejection](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/09/Activation_Rejection.jpg)

If the body of the response of a 300-400 level status is formatted correctly we will provide the partner with the human readable message that you specify. A good message here can provide the partner with the information they require to fix the error, making it more likely for them to try the activation again. 

```json
{
    "error_code": "This is a code that has meaning to you so if we need to ask you questions about a rejection it's easier for you to debug",
    "message": "A message for the developers of Vendasta to help debug what went wrong",
    "human_readable_message": "The text here will be displayed directly to your customer beside the reject date."
}
```

## Product Purchase Hook

The purchase webhook is called every time billing information about an account changes in regards to your product. The relevant changes are the **provisioned**, **change-edition**, and **de-provisioned** events, which are specified in the 'action' field. Note that the order_form section will be null if no order form is specified for your product.

Details:

- **webhook_id** _An identifier for the webhook describing its function._
- **action** _A description of the action that triggered the webhook being sent._
- **account** _A nested structure representing the business the product has been purchased for including identification, location, and contact information._
- **order_form** _A collection of information added to an order form filled out while purchasing the product. Will be null if the product has no order form specified._
- **partner_id** _A unique id specific to the partner selling to the business account._
- **market_id** _An identifier indicating the area that a partner is marketing to. Usually used to specify different geographic regions._
- **activation_id:** _Unique id specific to each instance of an activated product._
- **order_form_submission_id** _A unique identifier specific to the order form that was filled out. Will be empty if there is no order form.
- **vendor_order_id** _A unique identifier for the order the product was purchased in._
- **custom_price** _A nested structure representing what is being spent on the product at activation. The structure contains values for currency, frequency of billing and the spending amount in cents._
- **edition_id** _A unique identifier for the edition of the product being purchased. Will be empty if product does not have separate editions._
- **app_id** _A unique identifier for the product._

SAMPLE DECODED PAYLOAD - Provisioned:

```json
{
   "iss":"Vendasta Marketplace",
   "iat":1457560237,
   "exp":1457560297,
   "vendasta.com/marketplace/webhook":{
      "webhook_id":"purchase",
      "action":"provisioned",
      "account":{
         "call_tracking_number":[
            "18005555555"
         ],
         "deleted_flag":false,
         "contact_first_name":null,
         "common_name":[
            "KingMe"
         ],
         "timezone":"America/Regina",
         "work_number":[
            "+1 306-652-5464"
         ],
         "partner_id":"VNDR",
         "id":"AG-M3PB8CJP2J",
         "city":"Saskatoon",
         "zip":"S7M 0X6",
         "market_id":"default",
         "deleted":null,
         "state":"SK",
         "company_name":"King Me Boardgamery and Cafe",
         "latitude":52.1259659,
         "foursquare_url":"https://www.foursquare.com/kingme",
         "service_area_business_flag":false,
         "facebook_url":"https://www.facebook.com/kingmeboardgamery/",
         "tax_ids":[
            "food",
            "restaurants:cafes"
         ],
         "website":"https://www.kingmeboardgamery.com/",
         "rss_url":"http://iamawesome.com",
         "updated":"Mon, 29 Mar 2021 23:25:25 -0000",
         "twitter_url":"https://www.twitter.com/kingme",
         "address2":"42",
         "fax_number":"1800343433258298",
         "contact_email":null,
         "address":"527 20th Street West",
         "youtube_url":"https://www.youtube.com/kingmeboardgamery",
         "instagram_url":"https://www.instagram.com/kingme",
         "key_person":[
            
         ],
         "pinterest_url":"https://www.pinterest.com/kingme",
         "contact_last_name":null,
         "cell_number":"3065555555",
         "customer_identifier":"kingme-test",
         "created":"Fri, 17 Jan 2020 17:59:47 -0000",
         "linkedin_url":"https://www.linkedin.com/kingme",
         "longitude":-106.6776856,
         "sales_person_id":"UID-d1985535-f7f2-4529-ab47-2932110c46fe",
         "googleplus_url":null,
         "country":"CA"
      },
      "order_form":{
         "contact_phone_number":"",
         "business_phone_number":"(999) 999-5555",
         "salesperson_name":"Jane Doe",
         "business_account_group_id":"AG-XXXXXXXX",
         "checbox_question":true,
         "contact_email":"",
         "salesperson_phone_number":[
            
         ],
         "salesperson_email":"person@vendasta.com",
         "business_name":"Name",
         "contact_name":"",
         "business_address":"123 some st, San Francisco, CA, US, 94111",
         "question_number_two":"2",
         "files_for_upload":[
            {
               "name":"uploadedfilename.ext",
               "url":"https://example.com/A%20file%20with%20spaces.ext"
            },
            {
               "name":"uploadedpicturename.jpg",
               "url":"https://example.com/pathtofile/"
            }
         ],
         "drop_down_question":{
            "value":"Yes",
            "label":"Yes"
         },
         "end_user_field_type":{
            "value":"{\"firstName\":\"Rand\",\"lastName\":\"al'Thor\",   \"email\":\"dragon.reborn@gmail.com\",\"id\":\"UID-372e0708-ab65-493f-8d47-665cbca4b65a\"}",
            "label":"Rand al'Thor (dragon.reborn@gmail.com)"
         }
      },
      "partner_id":"ABC",
      "market_id":"Westlands",
      "activation_id":"02e9929d-35f2-4c70-988f-5650b183ef9f",
      "order_form_submission_id":"OFS-9f2e1a82-51d6-4a79-ba4b-a8347de14e53",
      "vendor_order_id":"ORD-XXXXXXXXXX",
      "custom_price":{
         "currency":"USD",
         "value":50000,
         "frequency":"MONTHLY"
      },
      "edition_id":"EDITION-123",
      "app_id":"MP-123"
   }
}
```
<div class="background-accent remember">

_Please Note:_
* If you need extended Account Data it can be retrieved by adding /richdata to the end of the [Get Account](https://developers.vendasta.com/swaggerui#/account/get_account__account_id_) path
</div>

SAMPLE DECODED PAYLOAD - Edition Changed:

```json
{
    "iss": "Vendasta Marketplace",
    "iat": 1457560237,
    "exp": 1457560297,
    "vendasta.com/marketplace/webhook": {
        "webhook_id": "purchase",
        "action": "change-edition",
        "account": {
            "id": "AG-XXXXXXXX",
            ...
        },
       "order_form": {
          ...
        },
        "partner_id": "ABC",
        "market_id": "Saskatoon",
        "activation_id": "02e9929d-35f2-4c70-988f-5650b183ef9f",
        "order_form_submission_id": "OFS-9f2e1a82-51d6-4a79-ba4b-a8347de14e53",
        "edition_id": "newEditionId",
        "previous_edition_id": "formerEditionId",
        "app_id": "MP-123"
}
```

SAMPLE DECODED PAYLOAD - De-Provisioned:

```json
{
    "iss": "Vendasta Marketplace",
    "iat": 1457560237,
    "exp": 1457560297,
    "vendasta.com/marketplace/webhook": {
        "webhook_id": "purchase",
        "action": "de-provisioned",
        "account": {
            "id": "AG-XXXXXXXX",
            ...
        },
        "order_form": null,
        "partner_id": "ABC",
        "market_id": "Saskatoon",
        "activation_id": "02e9929d-35f2-4c70-988f-5650b183ef9f",
        "order_form_submission_id": "OFS-9f2e1a82-51d6-4a79-ba4b-a8347de14e53",
        "edition_id": "EDITION-123",
        "app_id": "MP-123"
    }
}
```

SAMPLE DECODED PAYLOAD - Trial:

```json
{
  "iss": "Vendasta Marketplace",
  "iat": 1617061008,
  "exp": 1617061068,
  "vendasta.com/marketplace/webhook": {
    "account": {
        "id": "AG-XXXXXXXX",
        ...
    },
    "order_form_submission_id": "",
    "vendor_order_id": "O-8447fb46-3d10-496a-bd8b-28041372da55",
    "order_form": null,
    "market_id": "default",
    "previous_edition_id": "",
    "app_id": "MP-c316837acf404de681cd4883331c43ae",
    "edition_id": "",
    "activation_id": "f13da5ec-b89c-4a84-a05a-e47db1b88eda",
    "deactivation_time": null,
    "webhook_id": "purchase",
    "action": "provisioned-trial",
    "activation_time": "2021-03-29T23:36:46.812540727Z",
    "partner_id": "VNDR",
    "addon_id": null
  }
}
```


## Add-Ons Purchase Hook

The Add-ons purchase webhook is called every time billing information about an account changes in regards to the Add-ons for your product. The relevant changes are the **provisioned**, and **de-provisioned** events, which are specified in the 'action' field. Note that the order_form section will be null if no order form is specified for your product.

Details:

- **account** _A nested structure representing the business the product has been purchased for including identification, location, and contact information._
- **order_form** _A collection of information added to an order form filled out while purchasing the Add-on. Will be null if the product has no order form specified._
- **market_id** _An identifier indicating the area that a partner is marketing to. Usually used to specify different geographic regions._
- **addon_id:** _Unique id, specific to the Add-on being activated._
- **activation_id:** _Unique id specific to each instance of an activated Add-on._
- **order_form_submission_id** _A unique identifier specific to the order form that was filled out. Will be empty if there is no order form.
- **custom_price** _A nested structure representing what is being spent on the product at activation. The structure contains values for currency, frequency of billing and the spending amount in cents._
- **webhook_id** _An identifier for the webhook describing its function._
- **action** _A description of the action that triggered the webhook being sent._
- **activation_time:** _The time that an Add-on was or is to be activated. This is to allow for delayed activation._
- **deactivation_time:** _The time that an Add-on should be deactivated in the future. This must be observed and acted on come the given time._
- **partner_id** _A unique id specific to the partner selling to the business account._
- **app_id** _A unique identifier for the product._

SAMPLE DECODED PAYLOAD - Provisioned:

```json
{  
   "iss":"Vendasta Marketplace",
   "iat":1500498639,
   "exp":1500498699,
   "vendasta.com/marketplace/webhook":{  
   "account":{  
      "partner_id":"VNDR",
      "id":"AG-XXXXXXXX",
      "market_id":"testmarket",
      "created":"Thu, 06 Jul 2017 21:31:35 -0000",
      ...
   },
   "order_form":{  
      "contact_phone_number":"",
      "business_phone_number":"(999) 999-5555",
      "salesperson_name":"Jane Doe",
      "business_account_group_id":"AG-XXXXXXXX",
      "checbox_question":true,
      "contact_email":"",
      "salesperson_phone_number":[  

      ],
      "salesperson_email":"person@vendasta.com",
      "business_name":"Name",
      "contact_name":"",
      "business_address":"123 some st, San Francisco, CA, US, 94111",
      "question_number_two":"2",
      "files_for_upload":[  
         {  
            "name":"uploadedfilename.ext",
            "url":"https://example.com/A%20file%20with%20spaces.ext"
         },
         {  
            "name":"uploadedpicturename.jpg",
            "url":"https://example.com/pathtofile/"
         }
      ],
      "drop_down_question":{  
         "value":"Yes",
         "label":"Yes"
      }
   },
   "market_id":"testmarket",
   "addon_id":"A-604152205",
   "activation_id": "02e9929d-35f2-4c70-988f-5650b183ef9d",
   "order_form_submission_id": "OFS-9f2e1a82-51d6-4a79-ba4b-a8347de14c53",
   "custom_price": {
     "currency":"USD",
     "value": 50000,
     "frequency":"MONTHLY"
   },
   "webhook_id":"purchase",
   "action":"provisioned",
   "activation_time":"2017-07-24T14:39:55.117426664Z",
   "deactivation_time":null,
   "partner_id":"VNDR",
   "app_id": "MP-123"
}
```

SAMPLE DECODED PAYLOAD - De-Provisioned:

Details:

- **account** _A nested structure representing the business the product has been purchased for including identification, location, and contact information._
- **order_form** _A collection of information added to an order form filled out while purchasing the Add-on. Will be null for de-provisioning webhooks._
- **market_id** _An identifier indicating the area that a partner is marketing to. Usually used to specify different geographic regions._
- **addon_id:** _Unique id, specific to the Add-on being activated._
- **activation_id:** _Unique id specific to each instance of an activated Add-on._
- **order_form_submission_id** _A unique identifier specific to the order form that was filled out. Will be empty if there is no order form.
- **webhook_id** _An identifier for the webhook describing its function._
- **action** _A description of the action that triggered the webhook being sent._
- **activation_time:** _The time that an Add-on was or is to be activated. This is to allow for delayed activation._
- **deactivation_time:** _The time that an Add-on should be deactivated in the future. This must be observed and acted on come the given time._
- **partner_id** _A unique id specific to the partner selling to the business account._
- **app_id** _A unique identifier for the product._

```json
{  
   "iss":"Vendasta Marketplace",
   "iat":1500498639,
   "exp":1500498699,
   "vendasta.com/marketplace/webhook":{  
      "account":{  
         "partner_id":"VNDR",
         "id":"AG-XXXXXXXX",
         "market_id":"testmarket",
         "created":"Thu, 06 Jul 2017 21:31:35 -0000",
         ...
      },
      "order_form":null,
      "market_id":"testmarket",
      "addon_id":"A-604152205",
      "activation_id": "02e9929d-35f2-4c70-988f-5650b183ef9d",
      "order_form_submission_id": "OFS-9f2e1a82-51d6-4a79-ba4b-a8347de14e53",
      "webhook_id":"purchase",
      "action":"de-provisioned",
      "activation_time":"2017-07-24T14:39:55.117426664Z",
      "deactivation_time":"2017-07-27T15:49:56.117426675Z",
      "partner_id":"VNDR",
      "app_id": "MP-123"
   }
}
```

## Cancelation Webhook

The Cancelation webhook is called when a product gets cancelled. The relevant changes are the **cancel**, and **undo-cancel** events, which are specified in the 'action' field. Note that the original activation time, time of the action, and time of deactivation or renewal will be included.

Details:

- **account_group_id:** _Unique id, specific to the account that is cancelling a product._
- **activation_id:** _Unique id specific to each instance of an activated product._
- **activation_time:** _The time that the product was activated._
- **deactivation_time:** _The time that the product should be deactivated in the future. This must be observed and acted on come the given time._
- **cancellation_choices:** _List of preset reasons the user can select during the cancellation. (Product is too expensive, Customer reached end of contract, etc)_
- **cancellation_comment:** _User submitted reason for the cancellation._

SAMPLE DECODED PAYLOAD - Cancel:

```json
{
  "iss": "Vendasta Marketplace",
  "iat": 1500498639,
  "exp": 1500498699,
  "vendasta.com/marketplace/webhook": {
    "account_group_id": "AG-XXXXXXXX",
    "addon_id": "A-604152205",
    "edition_id": "",
    "activation_id": "02e9929d-35f2-4c70-988f-5650b183ef9d",
    "webhook_id": "Cancel-Product",
    "action": "cancel",
    "activation_time": "2017-07-24T14:39:55.117426664Z",
    "cancellation_time": "2017-07-31T5:47:52.114326789Z",
    "deactivation_time": "2017-08-23T14:39:55.117426664Z",
    "partner_id": "VNDR",
    "app_id": "MP-123",
    "vendor_order_id": "ORD-123",
    "cancellation_choices": ["Product is too expensive"],
    "cancellation_comment": "This is why we cancelled the product"
  }
}
```

SAMPLE DECODED PAYLOAD - Undo-Cancel:

Details:

- **account_group_id:** _Unique id, specific to the account that is cancelling a product._
- **activation_id:** _Unique id specific to each instance of an activated product._
- **activation_time:** _The time that the product was activated._
- **renewal_time:** _The time that the product should be renewed in the future._

```json
{
  "iss": "Vendasta Marketplace",
  "iat": 1500498639,
  "exp": 1500498699,
  "vendasta.com/marketplace/webhook": {
    "account_group_id": "AG-XXXXXXXX",
    "addon_id": "A-604152205",
    "edition_id": "",
    "activation_id": "02e9929d-35f2-4c70-988f-5650b183ef9d",
    "webhook_id": "Undo-Cancel-Product",
    "action": "undo-cancel",
    "activation_time": "2017-07-24T14:39:55.117426664Z",
    "undo_cancellation_time": "2017-08-1T6:34:24.8234173950Z",
    "renewal_time": "2017-08-23T14:39:55.117426664Z",
    "partner_id": "VNDR",
    "app_id": "MP-123",
    "vendor_order_id": "ORD-123"
  }
}
```


## Logout Webhook

The logout webhook is called every time a user’s session is invalidated by timeout or from a user taking an actual logout action. This webhook instructs the application to remove the specified session from it’s session store. The session_id provided matches the id provided to the [Session URL](/vendors/session-middleware#session-url) when the session was first transferred. For security reasons this is a one-way hash of the user’s actual Vendasta session ID.

SAMPLE DECODED PAYLOAD:

```json
{
    "iss": "Vendasta Marketplace",
    "iat": 1457560237,
    "exp": 1457560297,
    "vendasta.com/marketplace/webhook": {
        "webhook_id": "logout",
        "session_id": "195713298abef3",
        "user_id": "UID-684f886a-9c00-4aaf-a8ee-76a1dc43xxxx",
        "partner_id": "ABC",
        "market_id": "Saskatoon"
    }
}
```

*note that user_id can be different lengths

## Account Webhook

The account webhook is called every time account information changes. This can include changes to the address, phone number, etc. that we track for the account. This endpoint is useful for applications to keep a local cache of accounts. This can be important for making responsive applications.

SAMPLE DECODED PAYLOAD:

```json
{
    "iss": "Vendasta Marketplace",
    "iat": 1457560237,
    "exp": 1457560297,
    "vendasta.com/marketplace/webhook": {
        "webhook_id": "account",
        "action": "update",
        "account": {
            "id": "AG-XXXXXXXX",
            ...
        },
        "partner_id": "ABC",
        "market_id": "Saskatoon"
    }
}
```

## User Webhook

The user webhook is called every time permission is granted/revoked for an account that uses any of your products. This endpoint is useful for applications wanting to keep a local cache of users and the accounts they have access to. This webhook will be triggered for both the `permission-granted` and `permission-revoked` events, which are specified in the 'action' field. Note that it does not notify all the new users associated to an account that just activated the product.

SAMPLE DECODED PAYLOAD - Permission-Granted:

```json
{
    "iss": "Vendasta Marketplace",
    "iat": 1457560237,
    "exp": 1457560297,
    "vendasta.com/marketplace/webhook": {
        "webhook_id": "user",
        "action": "permission-granted",
        "user_id": "UID-b0d9dc56",
        "account_ids": [
            "AG-XXXXXXXX",
            "AG-XXXXXXXX"
        ],
        "partner_id": "ABC",
        "market_id": "Saskatoon"
    }
}
```

SAMPLE DECODED PAYLOAD - Permission-Revoked:

```json
{
    "iss": "Vendasta Marketplace",
    "iat": 1457560237,
    "exp": 1457560297,
    "vendasta.com/marketplace/webhook": {
        "webhook_id": "user",
        "action": "permission-revoked",
        "user_id": "UID-b0d9dc56",
        "account_ids": [
            "AG-XXXXXXXX",
            "AG-XXXXXXXX"
        ],
        "partner_id": "ABC",
        "market_id": "Saskatoon"
    }
}
```

## Customer Webhook

The customer webhook is called every time a customer is created, or deleted in _Customer List_.

SAMPLE DECODED PAYLOAD - Create:

```json
{
  "iss": "Vendasta Marketplace",
  "iat": 1517504974,
  "exp": 1517505034,
  "vendasta.com/marketplace/webhook": {
       "action":"create",
       "origin": "VBC",
       "city": "Saskatoon",
       "first_name": "John",
       "last_name": "Smith",
       "created": "2018-02-01T17:09:23.358779241Z",
       "country": "Canada",
       "contact_id": "CON-12345678901234567890123456789012",
       "phone": "1234567890",
       "state": "SK",
       "account_group_id": "AG-XXXXXXXX",
       "address":    "7 - 3th Street, Saskatoon",
       "email": [ "john@smith.com" ]
  }
}
```

SAMPLE DECODED PAYLOAD - Delete:

```json
{
  "iss": "Vendasta Marketplace",
  "iat": 1517504974,
  "exp": 1517505034,
  "vendasta.com/marketplace/webhook": {
       "origin": "VBC",
       "action":"delete",
       "deleted": "2018-02-01T17:09:23.358779241Z",
       "contact_id": "CON-12345678901234567890123456789012",
       "account_group_id": "AG-XXXXXXXX"
  }
}
```

## Spend Change Request Webhook

The spend change request webhook is called every time a spend change is requested for one of the vendor's products.

SAMPLE DECODED PAYLOAD:

```json
{
  "iss": "Vendasta Marketplace",
  "iat": 1457560237,
  "exp": 1457560297,
  "vendasta.com/marketplace/webhook": {
    "webhook_id": "service-change-request",
    "action": "spend-change",
    "account_id": "AG-123456",
    "partner_id": "ABC",
    "market_id": "Saskatoon",
    "activation_id": "02e9929d-35f2-4c70-988f-5650b183ef9f",
    "change_request_id": "SCR-30ccd1f5-edd0-42ef-b540-b76e7417bba5",
    "edition_id": "EDITION-123",
    "app_id": "MP-123",
    "addon_id": "A-123",
    "effective_date": "Fri, 17 Jan 2021 17:59:47 -0000",
    "requested_value": 30000,
    "currency" : "USD",
    "billing_frequency": "Monthly",
    "requester_email": "abc123@vendasta.com",
    "requester_note": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
  }
}
```

