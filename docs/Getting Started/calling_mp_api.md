# Calling the Marketplace API

Now that you are able to retrieve [authorization tokens](/vendors/getting-started/authentication) to use the Marketplace API, you can make use of the testing tools built into the API documentation itself.

## Testing Tool

You won’t have any products active on your test Accounts yet, which means you don't have permission to call any of the Account speific APIs. So start by testing the Marketplace App API. You can find your marketplace_app_id in the url when editing your application in Vendor Center(format MP-XX….)

**Authorize**

Select 'Authorize' at the top of the page and enter the OAuth token , this will authorize you to use any of the endpoints:
![Authorizeation Placement](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/11/Vendasta-Marketplace-V1-APIs.jpg)

**Run a Test**

Hit the 'Try it out' Button, and observe the response:
![API Testing Tool](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/11/api_tryitout.jpg)

**API Responses**

All API response messages are in JSON format. The actual data of the response will be nested inside a data object.

Example response:

```json
{
  "data": {
    "vendor": "Vendor Name",
    "description": "Application Description",
    "name": "Application Name",
    "entry_url": "https://yourdomain.com/<accountId>/",
    "id": "MP-c316837acf404de681cd4883331c43ae",
    "icon": null
  },
  "took": 14
}
```

## Additional Testing Options

**Auth Structure**

For all calls to the Marketplace API, the user agent should send the payload with an Authorization header using the Bearer schema. The header should be as follows:

```
Authorization: Bearer <OAuthToken>
```

### Examples

A typical Postman setup would look like this:
![Postman Example](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/03/API_Postman-610x156.png)

Read only endpoints will often contain path parameters such as an accountId, or userId. Create and Update endpoints will generally take a json object containing the parameters in the POST body.

**Example Get**

```text
curl -X GET \
  https://developers.vendasta.com/api/v1/account/AG-RN53F9WD/users \
  -H 'Authorization: Bearer eyJhcHBfaWQiOiAiTVAtYzMxNjgzN2FjZjQwNGRlNjgxY2Q0ODgzMzMxYzQzYWUiLCAic2Vzc2lvbl9pZCI6ICJkNDJmYTQzOC03NmJhLTFmNmQtMGE5YS01NzY2NGFhMTYzNmUifQ==' \
  -H 'Cache-Control: no-cache' \
  -H 'Content-Type: application/json' \
```

**Example POST**

```json
curl -X POST \
  https://developers.vendasta.com/api/v1/activity/ \
  -H 'Authorization: Bearer eyJhcHBfaWQiOiAiTVAtYzMxNjgzN2FjZjQwNGRlNjgxY2Q0ODgzMzMxYzQzYWUiLCAic2Vzc2lvbl9pZCI6ICJkNDJmYTQzOC03NmJhLTFmNmQtMGE5YS01NzY2NGFhMTYzNmUifQ==' \
  -H 'Cache-Control: no-cache' \
  -H 'Content-Type: application/json' \
  -d '{
  "account_id": "AG-RN53F9WD",
  "activity_type": "Test Activity",
  "title": "Example Title",
  "link": "https://iamawesome.com",
  "content": "This is the plaintext body of your notificiation or message",
  "requires_platform_auth": true
}'
```