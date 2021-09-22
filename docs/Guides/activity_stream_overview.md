# Activity Stream

Communications with the end user will flow through the Vendasta Activity Stream API. Email notifications will be generated based on data pushed to Activity Stream, and managed on a per User basis from the Vendasta Business App via the Vendasta Notification System *as stated in the Technical Requirements*.

## What is the Activity Stream

The Activity Stream is a notification distribution system, which allows Users to quickly see what’s happening in all the applications they have purchased from the Marketplace within their Business Operating System - the Vendasta Business App. It resides currently within the Inbox Page.

It is also tied into our email Notification System. Anything that appears in the Stream can be pushed out through instant, or digest based emails which are **automatically branded for the Channel Partner**. By default it will be pushed to all users on the Account; Notification Recipients can then set their preferences individually.

**What should be pushed to the Activity Stream**

The Activity Stream is for Account Level notifications that will go to all users on the account. So what should be sent depends on what notifications you already regularly send to your end users. This is not for messaging to Partner Resellers.
Basic HTML is supported by the `content` field and will render appropriately on the Activity Cards in the UI, but if you have more media heavy reports, you may want to send a message with a link to the report. Even if you don’t have any regular communications we recommend setting up both a welcome message, and a generic Activity Type for any one off communication needs.

**How the Activity Stream & Notification Emails Work**

When an event occurs in a Vendor's Product that the users of the product should know about, the vendor would create an `Activity` representing that event within Vendasta.

![highlights](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/02/ActivityStream_HighLevel-1.png)

The end user communications that are pushed to the Activity Stream firstly appear in the Business App Activity Stream within the Inbox page, filterable by 'Activity Type'. This information will also go out to the Notification Recipients on the Account based on their email preferences.

The `Activity Type` is essentially a Notification Category, that allows for filtering of all the 'Activity' that is occurring in a User’s different applications.

![new](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/06/ActivityStreamNew.jpg)



