# Whitelabelling & Branding

## Vendasta's Platform Whitelabel

Vendasta is a white-labelled platform from the perspective of its Resellers' clients. There must not be any mention of ‘Vendasta’ in your content.
You may want to keep the term 'Vendasta' out of your codebase entirely; but if not, aim to avoid this on anything client-facing:
* Marketing material
* UI (including dashboard URLs)
* Cookies
* Client side redirects
* Error messages

## Product Branding

Many of our Partners will take more interest in selling your offering if they can brand it as their own. If you support branding your dashboard in any way, this guide will go over how you can add support for this to your product, as well as the different types of branding there are.

### Option 1 - Partner Market Branding

You are able to brand your dashboard with the custom logos, colours, and potentially names that the Resellers supply for each of their [Markets]()

The *Account API* [Get Account Settings](https://developers.vendasta.com/swaggerui#/account/get_account__account_id__settings) endpoint provides you with:

* Partner Name
* Partner Logo URL
* Partner Primary Colour(hex)

<!--theme:warning -->
>Test

### Option 2 - Product Branding Override(Beta)