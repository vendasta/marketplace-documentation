# API Authentication

The Vendasta APIs are built for serving server-to-server interactions. Your web application retrieves an authorization token(referred to in this documentation as an OAuth Token or Bearer Token) on behalf of one of your marketplace applications, so user credentials from your end are not directly involved. This scenario is known as “two-legged OAuth”; user consent is not directly required by your application, but is implied by a user having your application added to their account. Interaction with Vendasta Accounts, Users, and the Marketplace API can only occur once the Vendasta User has added your application to their account.

**Auth Structure**
For all calls to the Marketplace API, the user agent should send the payload with an Authorization header using the Bearer schema. The header should be as follows:

```hljs
Authorization: Bearer <OAuthToken>
```

We will walk through retrieving an OAuthToken step by step:

## Step 1 - Public Key

To be able to retreive an OAuthToken, you must have a Marketplace Application created, and your public key in place. This public key will be used to verify any JWT (JSON Web Tokens) that you send us, which you will sign with your private key, just as you verify payloads from us using the Marketplace public key, we will verify your requests for an OAuth token with the public key you provide.

You can use the following bash script to generate a private/public key pair.

```bash
#!/bin/bash
openssl genrsa -out privkey.pem 2048
openssl rsa -in privkey.pem -pubout -out pubkey.pem
```

The file contents of “pubkey.pem” are to be placed in the public key box at the top of the ‘Integration’ page of your Application in [Vendor Center](https://vendors.vendasta.com). Ensure there are no trailing spaces after the "-----END PUBLIC KEY-----". If you have multiple applications they can share a key.

**Note for Windows Users:**

openssl is not a native cmd tool. The latest version of Win32 OpenSSL can be found [here](https://slproweb.com/products/Win32OpenSSL.html). Don't forget to run the binary as an Admin, and that openssl is not added to your path by default.
Alernatively, if you already have [GitBash](https://gitforwindows.org/) for Windows it comes with openssl support.

## Step 2 - Creating a JSON Web Token

Once your public key is in place, you must prepare a JSON Web Token to exchange for an OAuth Bearer Token. JSON Web Tokens are an open standard [(RFC-7519)](https://tools.ietf.org/html/rfc7519) that define a way to securely transmit information between two parties as a JSON object. These tokens are referred to as a JWT(pronounced *jot*).

[https://jwt.io](https://jwt.io) is a useful website for more in-depth information on the JWT spec. They also provide a [list of open source libraries](http://jwt.io/#libraries) in a variety of languages, as well as a debugging tool for decoding and encoding Jots. If you want

The JSON Web token is composed in the following format:

    {Header}.{Payload}.{Signature}

_This is broken down below_

**Header**

The header consists of the type of the token, which in our case will always be “JWT”. The other part is the hashing algorithm, which in our case will always be “RS256”.
```json
{
		"typ": "JWT",
		"alg": "RS256"
}
```

**Payload**

The second part is the payload, which contains the claims and the metadata required to retrieve an OAuth token. 

```json
{
  "iss":"MP-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "iat":1511988126,
  "exp":1511989146
}
```

Claim Details:

|Name|Description
|:---|:---
|*iss* | [definition](https://tools.ietf.org/html/rfc7519#section-4.1.1) - The marketplace application id. This can be found in the address bar while editing your app in Vendor Center. It begins with MP-.
| *iat*| [definition](https://tools.ietf.org/html/rfc7519#section-4.1.6) - The time the assertion was issued, specified as an integer timestamp in *seconds* since epoch. **To allow for servers not being perfectly in sync, it is suggested to set this to a few seconds before the current time.**
| *exp*| [definition](https://tools.ietf.org/html/rfc7519#section-4.1.4) - The expiration time of the assertion, specified as an integer timestamp in *seconds* since epoch. This value **must be within 1 hour of the iat(issued at time)**.  


**Signature**

The third and last portion of the JWT is the signature. This signature is a hash of the header and body encoded in the format:

    {Base64url encoded header}.{Base64url encoded body}

Sign the encoded format with SHA256withRSA (also known as RSASSA-PKCS1-V1_5-SIGN with the SHA-256 hash function) with the private key that was generated in Step 1.

Example of a JWT in plain text

```text
{"alg": "RS256","typ": "JWT"}.{"iss": "MP-123","exp": 1328554385,"iat": 1328550785}.EkN-DOsnsuRjRO6BxXemmJDm3HbxrbRzXglbN2S4sOkopdU4IsDxTI8jO19W_A4K8ZPJijNLis4EZsHeY559a4DFOd50_OqgHGuERTqYZyuhtF39yxJPAjUESwxk2J5k_4zM3O-vtd1Ghyo4IbqKKSy6J9mTniYJPenn5-HIirE
```

Example of the same JWT in over the wire format - encoded in url safe base 64.

```base64
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiAiTVAtMTIzIiwiZXhwIjogMTMyODU1NDM4NSwiaWF0IjogMTMyODU1MDc4NX0.Adq0I54p0qEc_ioLz2zzhQRaGBcAE7Hf7aslSGW_cJ5fyBkQWeqniCtM6SzxzI0f2k2qk7PLSFNIUJkLlTzte6fyF_JZ8_97meNpFnHWQe_WJilrogLYopqftaB-59Vm-5tMuaKKR8A3oKTLwiLBqrYKaiZEtSaAq5souI2Ttgo
```

You can validate the JWT with the following public key(this example won't validate as the issuer doesn't actually exist):

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

## Step 3 - Getting an OAuth Bearer Token

After generating the signed JWT, an application can use it to request an access token. This access token request is an HTTPS POST request. The URL is shown as below.

|Name|Description
|:---|:---
| *grant_type* | Use the following string: *urn:ietf:params:oauth:grant-type:jwt-bearer*
| *assertion* | The JWT

Below is a raw dump of the HTTPS POST request used to retrieve an access token.

*POST* /api/v1/oauth/token HTTP/1.1
*Host*: developers.vendasta.com
*Content-Type*: application/json

```json
{"grant_type":"urn:ietf:params:oauth:grant-type:jwt-bearer","assertion":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOi
JNUC0xMjMiLCJleHAiOiIxMzI4NTU0Mzg1IiwiaWF0IjoxMzI4NTUwNzg1fQ.Adq0I54p0qEc_ioLz2zzhQRaGBcAE7Hf7aslSGW_cJ5fyBkQWeqniCtM6Sz
xzI0f2k2qk7PLSFNIUJkLlTzte6fyF_JZ8_97meNpFnHWQe_WJilrogLYopqftaB-59Vm-5tMuaKKR8A3oKTLwiLBqrYKaiZEtSaAq5souI2Ttgo"}
```

<h2 id="token-management">Step 4 - Token Management</h2>

**Handling the response and token lifetime**
If everything succeeds, the response will contain a OAuth Bearer Token that will be used for authenticating with all other Marketplace APIs. The following is an example response.

```json
{
    "data": {
        "access_token": "eyJhcHBfaWQiOiAiTVAtMTIzIiwgInNlc3Npb25faWQiOiAiYzNhNzgzZGQxMjMzODQwZWU4ZGQ5YjhmZmQ2OTUxMzE5In0=",
        "expires": 1328550785,
        "token_type": "Bearer"
    },
    "took": 38
}
```

* The access_token will expire 24 hours after it was issued.
* If an access token has been granted, any subsequent calls during its validity period will respond with the same token, **so the token should be cached by the vendor for it's validity period.**
* An Application should request a new token shortly after the current token's marked expiry time, **but also after receiving a 401 response from the API** _(There may be some rare instances where a token is invalidated early)._
* There is no Refresh Token with this workflow, a new JWT must be passed for every new Authorization Token to be generated.

**Next:** [Your First API Call](/vendors/getting-started/calling-api)
