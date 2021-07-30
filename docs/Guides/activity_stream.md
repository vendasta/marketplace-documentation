# Activity Stream

Communications with the end user will flow through the Vendasta Activity Stream API. Email notifications will be generated based on data pushed to Activity Stream, and managed on a per User basis from the Vendasta Business App via the Vendasta Notification System *as stated in the Technical Requirements*.

## What is the Activity Stream

The Activity Stream resides in the Business App Inbox. It is a notification hub, which allows Users to quickly see what’s happening in all the applications they have purchased from the Marketplace within their Business Operating System - the Vendasta Business App.

It is also tied into our email Notification System. Anything that appears in the Stream can be pushed out through instant, or digest based emails which are **automatically branded for the Channel Partner**. By default it will be pushed to all users on the Account; Notification Recipients can then set their preferences individually.

**What should be pushed to the Activity Stream**

This depends on what communication you regularly have with your end users. Currently the Activity Stream supports plain text only. This is great for quick updates. If you have more media heavy reports, you can send a message with a link to the report. Even if you don’t have any regular communications we recommend setting up both a welcome message, and a generic Activity Type for any one off communication needs.

**How the Activity Stream & Notification Emails Work**

When an event occurs in a Vendor's Product that the users of the product should know about, the vendor would create an `Activity` representing that event within Vendasta.

![highlights](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/02/ActivityStream_HighLevel-1.png)

The end user communications that are pushed to the Activity Stream firstly appear in the Business App Activity Stream within the Inbox page, filterable by 'Activity Type'. This information will also go out to the Notification Recipients on the Account based on their email preferences.

The Activity Type is essentially a Notification Category, that allows for filtering of all the 'Activity' that is occurring in a User’s different applications.

![new](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/06/ActivityStreamNew.jpg)


## Implementation of the Activity Stream

The Activity Stream serves as a Notification Hub for an Activity Stream in the Business App Dashboard, while also driving the User Email Notification System.

[View the Activity Stream Schema](/reference/marketplace-api.yaml/paths/~1activity~1/post)

**UI Tile Body**

TODO

### Filters

The following fields are strings used as categories and filters. These fields ENUMS must be pre configured by Vendasta with your required values before you can start using the Activity Stream API:

**“activity_type” ( required ) - Requires ENUM to be configured**

The activity_type is displayed as a Category Header in the Activity Filter in Business App, so should be a user-friendly string. An activity_type should be configured for each type of Notification that will be pushed for an Application

![Activity Type](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/06/ActivityStreamNew.jpg)

**"settings_tags" (optional) - Requires ENUM to be configured if being used**

Optional sub categories per "activity_type". These Activity sub categories don't show up in the Activity Stream filters, and only apply to the Notification System Emailing preferences.


## Drive Users into your product

Every Activity passed to the Activity Feed is passed with a link. When a User selects ‘View’, it calls the Application's Entry URL passing the 'link' as the 'nextUrl' parameter.  This is why the implementation of the nextUrl parameter for your [Entry URL](https://www.vendasta.com/developers/vendors/session-middleware#entry-url) is important. The nextUrl allows for deeplinking (directing a user to a page relevant to your Notification).

![Deep Linking](https://storage.googleapis.com/wordpress-www-vendasta/developers/2017/06/imageedit_1_7626234665.png)

### Email Template Options

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
