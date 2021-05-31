# Getting Started
Welcome to the Vendasta Marketplace Documentation!

This Guide will cover the benefits of placing your offerings in the Vendasta Marketplace, take you through the Vendasta Dashboards that you will be working with, provide an overview of creating a Marketplace Offering, and detail the basic usage of the Marketplace API & Webhooks. 
Once you've completed this section, you will be ready to move on to the Integration Guide, which will step through the [integration requirements](/vendors/requirements) in more detail.

**Learn More** 

If you are new to Marketplace, and want to discover how it can accelerate your sales, learn more <a href="https://www.vendasta.com/marketplace/vendors" target="_blank">HERE</a>

---

## Getting your solutions into the Vendasta marketplace

For **Free** anyone can become a Vendasta Channel Partner, upload their offerings to the platform, and leverage Vendasta's white-labeled end to end E-Commerce Platform to sell more effectively using their own sales team. 

Once comfortable with the platform, next unlock a powerful new sales channel by distributing your offerings to the Vendasta Marketplace. This will extend your own sales team by exposing your products to Vendasta's entire network of Channel Partners!

![Discover Products](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/partner_center_discover_750.png)

### Quickstart


## High Level Requirements

If you want your Product distributed in the Vendasta Marketplace, then there are a set of requirements that all distributed products must adhere to:

**1 - Order Acceptance**

Vendasta provides webhooks and emails that Vendors must use to manage orders & cancellations of their Product.

**2 - Single Sign On (SSO)**

Allow Vendasta Users to **securely** log into Vendor dashboards with a single click via OAuth2.0 or OpenIdConnect SSO.

Vendasta or verified Channel Partners in the Vendasta reseller network will act as the Identity Provider for Marketplace Product access. User management & User Permissions is controlled within the Vendasta platform. Vendors may keep Users in sync with their system via the Marketplace User API & Webhooks, or leverage JIT(Just in Time) user creation. _Vendor Products may not be logged into directly by Users who purchased through Vendasta_.

**3 - Unified Navigation and Interface**

Insert the Vendasta Business App Navigation Bar into your Product via a simple Javascript snippet. Vendors will also need to make any necessary adjustments to their UI to hide functions managed by Vendasta, including user logout, referrals, product upgrades, and in product purchases.

**4 - Reporting & Proof of Performance**

All Vendor offerings with applicable data are required to upload analytics and attribution metrics to highlight their product's ROI in the Vendasta [Executive Report](/vendors/reporting).

**5 - Communication**

Communication with end users will flow through the Vendasta [Activity Stream](/vendors/activity-stream), a component of the Business App Inbox. The Activity Stream sends emails automatically to all subscribed users. Vendors may use Vendasta provided templates, or may provide their own templates to automatically be wrapped in each Channel Partner's branding. _Vendors may not send any emails to end users directly. To support platform unity, and Channel Partner branding or whitelabelling, all end user communication must come through the Vendasta Platform._

**6 - CRM syncing**

_Customer List_ is a light weight CRM that lives in the end user dashboard Business App. It acts as a central pool for customers synced to the Business App. Any customers that a Vendor aquires the data for are synced to the pool, which will in turn push those customers to any supported Products active on the Account.

--

_Not all requirements may be applicable, for example your product may not have a crm component, in which case requirement 6 would be N/A. However, if any components above exist in your offering they will apply. Requirements will be confirmed on your discovery call with the Vendor Team._

---

## Sign Up

<a href="https://partners.vendasta.com/signup" target="_blank">**Get on the Vendasta Platform**</a>


Once you have a Platform Account, you will be able to enter your offerings into the Vendasta Marketplace as drafts, and get started on setting them up to be distributed and sold via the Vendasta Marketplace!

<div class="background-accent remember">

**Before You Start**
* _Register_ If you are planning on distributing your offerings via the Vendasta Marketplace rather than selling them yourself using the Vendasta Platform, and your company has not yet filled out the Marketplace Vendor Application Form, head <a href="https://vendasta.com/marketplace/vendors#sign-up" target="_blank">HERE</a> to do so.

* _Review Requirements_ You've seen the integration requirements highlighted above, but please take a moment to review the detailed checklist that you can refer back to while working through your integration: [Marketplace Integration Requirements](/vendors/requirements)

</div>

---

## Platform Overview
There are several Vendasta dashboards, which we call 'Centers', that you will interact with to set up and manage your Offerings

### Partner Center

![Partner Center](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/partner_center_activation-1000.png)

_To learn more about Partner Center visit our [Support Center](https://support.vendasta.com/hc/en-us/categories/360000719993-Partner-Center)_

<div class="background-accent info short">

* Manage Admin platform access for your team
* Create test Accounts
* Test activation of your Product
* Manage End User access to Accounts
* Access the End User Business App via sso, which we call 'Impersonation'

</div>

### Vendor Center
![Vendor Center](https://storage.googleapis.com/wordpress-www-vendasta/developers/2021/vendor_center_2021_1000.png)

_To learn more about Vendor Center visit our [Support Center](https://support.vendasta.com/hc/en-us/categories/360001580013-Vendor-Center)_

<div class="background-accent info short">

* Create Apps
* Configure and manage Apps
* Review the interactions Resellers have with your product, and view analytics on how your product is performing
* Contact Resellers that have opted in when you see an opportunity to accelerate their sales
* View, and Accept or Reject orders of your Product

</div>

### Whitelabelled Business App
![Whitelabelled Business App](https://storage.googleapis.com/wordpress-www-vendasta/developers/2021/business_app_2021_1000.png)

_To learn more about Business App visit our [Support Center](https://support.vendasta.com/hc/en-us/categories/360000716114-Business-Center)_

<div class="background-accent info short">

* Test Single Sign On into your Product
* Activate Trials
* Test the Executive Report
* Test the Activity Stream
* Test the Customer List

</div>

---
You should now have a basic understanding of the Vendasta ecosystem. You will always have the Marketplace team at your disposal, so if you need clarification on something, or have any suggestions on how to improve this documentation, let us know via the feedback button on the right hand side of Vendor Center.

**Next:** [Create Your Offerings](/vendors/getting-started/offerings)
