---
stoplight-id: jd66qo3rwdql2
---

# miniOrange Service Provider Wordpress Plugin

Here is MiniOrange's guide for Vendasta:
https://plugins.miniorange.com/vendasta-sso-using-wordpress-as-oauth-server

Download the Provider Server Plugin [here](https://plugins.miniorange.com/wordpress-oauth-server)

## Configuring the MiniOrange Plugin

## Handling custom authentication url params

MiniOrange does have [a filter](https://developers.miniorange.com/docs/oauth/wordpress/client/wordpress-hooks) for altering the Authorization URL in flight. However, they suggest for Vendasta's use case that their Identity Provider Initiated SSO should be utilized.

Here are the instructions for the [Link Login option](https://developers.miniorange.com/docs/oauth/wordpress/client/login-options#link-login):

This link is in the format:
```<your-wordpress-site>?option=oauthredirect&app_name=<application-name>&redirect_url=<redirect-url-of-your-wordpress-site>```

Where `<your-wordpress-site>` is your WP domain, `<application-name>` is the application that would be created in the OAuth Client plugin on WordPress, and `<redirect-url-of-your-wordpress-site>` will be the WP site link where the user will be redirected after the SSO.

To know how to get the application name, please refer the [Misc. Section for How to get the application name?](https://developers.miniorange.com/docs/oauth/wordpress/client/miscellaneous-info#getting-application-name)
For Example: If your domain is miniorange.com, your application name is OAuthApplication and redirect url is https://miniorange.com/single-sign-on-sso this link would look like:
`https://miniorange.com?option=oauthredirect&app_name=OAuthApplication&redirect_url=https://miniorange.com/single-sign-on-sso`
