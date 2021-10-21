# Implementation of the Activity Stream

[View the Activity Stream Schema](https://developers.vendasta.com/vendor/c2NoOjE2NTY5NDA3-activity)

## Filters

The following fields are strings used as categories and filters. These fields ENUMS must be pre configured by Vendasta with your required values before you can start using the Activity Stream API:

**“activity_type” ( required ) - Requires ENUM to be configured**

The activity_type is displayed as a Category Header in the Activity Filter in Business App, so should be a user-friendly string. An activity_type should be configured for each type of Notification that will be pushed for an Application

![Activity Type](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/06/ActivityStreamNew.jpg)

**"settings_tags" (optional) - Requires ENUM to be configured if being used**

Optional sub categories per "activity_type". These Activity sub categories don't show up in the Activity Stream filters, and only apply to the Notification System Emailing preferences.


## Drive Users into your product

The `link` is a required field. When a User selects ‘View’, it calls the Application's Entry URL passing the 'link' as the 'nextUrl' parameter.  This is why the implementation of the nextUrl parameter for your SSO Entry URL is important. The nextUrl allows for deeplinking (directing a user to a page relevant to your Notification). Encode the nextUrl in the state parameter for OAuth SSO, and retrieve it when passed to your redirect_uri.

![Deep Linking](https://storage.googleapis.com/wordpress-www-vendasta/developers/2017/06/imageedit_1_7626234665.png)

## Email Template Options

Contact your Vendasta Representative if you desire email alert templates for your Activity to be structured differently than they are in the default templates seen below. We can use your existing email html templates if you have them, or work with you to create a new dynamic template, for both the Instant Emails, and the Daily Digest.

**Instant Notification Template**

Default Structure:

![Example Instant Notification Email](https://storage.googleapis.com/wordpress-www-vendasta/developers/2017/06/Instant-Notification.png)

**Digest Template**

Default Structure: This email has a section for each application that has had activity for the day

![Example Digest Email](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/02/DailyDigest_twoActivityTypes.jpg)

*There will be up to 3 of each type of Notification displayed, and then a ‘View all’ button below if there are more.*

If you feel that your emails require something different for the inner tiles displayed for each activity, you can supply a custom html template for the individual activity tiles.

## Adjust Communications Accordingly

When you switch your end user communications to flow through the Activity Stream you will want to turn off the corresponding emails on your end so users don’t receive double communications.