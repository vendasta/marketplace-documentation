# Whitelabelling & Branding

Vendasta is a white-labelled platform from the perspective of its Resellers' clients. There must not be any mention of ‘Vendasta’ in your content.
You may want to keep the term 'Vendasta' out of your codebase entirely; but if not, aim to avoid this on anything client-facing:
* Marketing material
* UI (including dashboard URLs)
* Cookies
* Client side redirects
* Error messages

Many of our Partners will take more interest in selling your offering if they can brand it as their own. If you support whitelabelling, or are able to brand your dashboard with colours and logos, you can configure this at the time of purchase.

The *Account API* [Get Account Settings](https://developers.vendasta.com/swaggerui#/account/get_account__account_id__settings) endpoint provides you with:

* Partner Name
* Partner Logo URL
* Partner Primary Colour(hex)