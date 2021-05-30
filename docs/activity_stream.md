# Activity Stream

Communications with the end user will flow through the Vendasta Activity Stream API. Email notifications will be generated based on data pushed to Activity Stream, and managed on a per User basis from the Vendasta Business Center via the Vendasta Notification System *as stated in the Technical Requirements*.

## What is the Activity Stream

The Activity Stream resides in a Business Center User’s Dashboard. It allows Users to quickly see what’s happening in all the applications they have purchased from the Marketplace. It is also tied into our Notification System, so that anything that appears in the Stream can be pushed out through instant, or digest based emails. By default it will be pushed to all; Notification Recipients can then set their preferences individually.

**The Activity Stream is important to your clients - and ours**

Part of the power of the Vendasta platform is to centralize reporting, and provide notification management for the many products and services that our users and yours use on a daily basis. This centralized management is essential for supporting the many user types that have access to your applications through the Vendasta Business Center, and their different communication use cases. This is both an opportunity to display ROI for your clients, and to encourage them to log into the product for further details.

**What should be pushed to the Activity Stream**

This depends on what communication you regularly have with your end users. Currently the Activity Stream supports plain text only. This is great for quick updates. If you have more media heavy reports, you can send a message with a link to the report. Even if you don’t have any regular communications we recommend setting up both a welcome message, and a generic Activity Type for any one off communication needs.

**How the Activity Stream & Notification Emails Work**

![highlights](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/02/ActivityStream_HighLevel-1.png)

The client communications that are pushed to the Activity Stream firstly appear in the Business Center Activity Stream page, filterable by ‘Activity Type’. This information will also go out to the Notification Recipients on the Account based on their email preferences.

The Activity Type is essentially a Notification Category, that allows for filtering of all the ‘Activity’ that is occurring in a User’s different applications.

![new](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/06/ActivityStreamNew.jpg)


## Implementation of the Activity Stream

The Activity Stream serves as a Notification Hub for an Activity Stream in the Business Center Dashboard, while also driving the User Email Notification System.

View the Activity Stream Model in the [API Documentation](https://developers.vendasta.com/swaggerui#/activity/post_activity_)

### Filters

The following fields are strings used as categories and filters. These fields ENUMS must be preconfigured by Vendasta with your required values before you can start using the Activity Stream API:

**“activity_type” ( required ) - Requires ENUM to be configured**

The activity_type is displayed as a Category Header in the Activity Filter in Business Center, so should be a user friendly string. An activity_type should be configured for each type of Notification that will be pushed for an Application

![Activity Type](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/06/ActivityStreamNew.jpg)

**"settings_tags" (optional) - Requires ENUM to be configured if being used**

Optional sub categories per "activity_type". These Activity sub categories don't show up in the Activity Stream filters, and only apply to the Notification System Emailing preferences.

### Notification Settings

Notification Settings only affect the emails sent out for a particular Activity Type, or Setting Tag, all Activities sent to the API will appear in the Activity Stream. All Notifications emails are on by default. Users can edit their preferences, or a Partner can bulk edit preferences for all their Users. Partners may add Reporting Only Users, that don't have access to the platform or Marketplace Products directly.

*Notification Settings example from Vendasta’s Reputation Management Application:*

![Notification Settings Example](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/06/Recent-Activity.jpg)

*The Activity Type is "Mention". There are 7 settings_tags for the activitytype "Mention". In this example, a User would still see Somewhat Positive Mention Activities under the ‘Mention’ Category in their Activity Stream, but they would not receive emails for them in their ‘Daily Digest’ report, as the User has it disabled.*

## Drive Users into your product

Every Activity passed to the Activity Feed is passed with a link. When a User selects ‘View’, it calls the Application's Entry URL passing the 'link' as the 'nextUrl' parameter.  This is why the implementation of the nextUrl parameter for your [Entry URL](https://www.vendasta.com/developers/vendors/session-middleware#entry-url) is important. The nextUrl allows for deeplinking (directing a user to a page relevant to your Notification).

![Deep Linking](https://storage.googleapis.com/wordpress-www-vendasta/developers/2017/06/imageedit_1_7626234665.png)

### Email Templates Options

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
