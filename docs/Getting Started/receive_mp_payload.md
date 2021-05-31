# Receiving a Marketplace Payload

## Prerequisites

* Having reviewed the [Integration Requirements](/vendors/overview), you understand the components of a Marketplace integration.
* You have [registered as a Markeptlace Vendor](https://vendasta.com/marketplace/vendors#sign-up) and/or the Vendasta team has set you up with an Account.

If you haven't completed the above steps, go back to [Getting Started](/vendors/getting-started). Otherwise, continue to learn the basics with the quickstart below before moving on to the Integration Guide for integrating your Application into the Vendasta Marketplace.

---

## Introducing the JSON Web Token (JWT) Standard

Vendasta takes security and privacy as a primary concern. Data passed to Marketplace Vendors is sent over HTTPS, and all payloads are signed by the Vendasta Marketplace so applications can verify their authenticity. In order to make this process secure, extensible, and simple for developers we have chosen the [JSON Web Token Format](https://jwt.io/introduction/) signed by an RSA256 key for data delivery. It will also be used for requesting an authentication token for the Marketplace API, discussed in the next section.

The format Marketplace uses to send a Vendor information will be the same for Webhooks and Session Transfer, a topic which will be dicussed in the Integration Guide. We will start with testing Webhooks.

### Step 1 - Examining a Marketplace Webhook

**Payload Details**

``` text
content type: text/plain
body: JWT signed base64 string sent as plaintext raw in the POST body
```

**Testing**
The best way to initially test receiving a payload from Vendasta is to use the Testing Page in Vendor Center. The Testing Page will load the Purchase Webhook URLs you have entered in the Integration Page by default, but you can set them to whatever url you would like.

For your first test, try a service like [Webhook Tester](https://webhook.site) or [RequestBin](https://requestbin.fullcontact.com/). Copy the unique url they give, and enter it into the on the testing page, and select "SEND TEST APP WEBHOOK":

![](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/10/webhooktesting.jpg)

Review the results on the Webhook Tester page. It will have received a dummy payload. To see the contents, place the JWT token into the Encoded text area of the [jwt.io debugger](https://jwt.io). You will see that it indicates that you have an Invalid Signature since no Public Key is in place yet.

### Step 2 - Processing a Payload

Auth0 has [libraries](https://jwt.io/#libraries-io) for most popular languages to help speed up integrations. Most of these libraries can both validate, and decode a JWT.

For validating JWT tokens sent to your endpoints by Vendasta, the public key for the Vendasta Marketplace is:

``` text
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

**Security Note**
The JWT issuer claim value(*iss*) will be ‘Vendasta Marketplace’, and the JWT will be signed with the Vendasta Marketplace private key. In addition to validating the JWT signature using the Marketplace public key, the issuer of any payload you receive should be verified to be ‘Vendasta Marketplace’, or ‘Vendasta Marketplace Test’.

---

**Next:** [API Authentication](/vendors/getting-started/authentication)
