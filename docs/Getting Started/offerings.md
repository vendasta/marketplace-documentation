# Configuring your Products and Services

Adapt your Product or Services to be Marketplace Offerings. You have 3 SKU types to work with.

![Product Creation](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/offering_structure_noborder.png)

You manage your all of your offerings from Vendor Center.  
<div class="background-accent info">

_**Access Vendor Center**_ by logging in directly in at <a href="https://vendors.vendasta.com" target="_blank">https://vendors.vendasta.com</a>, from the top left of this documentation, or navigate from Partner Center using the top navbar.
</div>

## Product

This will represent a _Base Offering_ - Whether that be a Digital Product, a Service, or a Physical Product.

**Creating your first Product**

![Product Creation](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/productcreationfull.gif)

<div class="background-accent info">

_**Steps to allowing for your first test Activation**_
1. Create Product
2. Publish Product - This makes the Product available in Partner Center
3. Navigate to Partner Center. Find your Product in the [Discover Products page](https://partners.vendasta.com/marketplace/products) via Search or checking 'Show My Products' in the filter
4. Enable Your Product - This makes it available to package or [activate](https://support.vendasta.com/hc/en-us/articles/360033743333)
</div>

**Trials**

You are able to set a timed trial for your Product. The Vendor is responsible for in Product behavior after the trial expires.

![trial configuration](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/app_trials.png)

&nbsp;

**Multi-purchase**

Products are able to be purchased multiple times on the same Account, but not multiple times at once.

![Multiple Purchase Setting](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/multi-purchase_app.png)

_This is set under the Advanced Settings, in the Product Info Tab_

### Edition

If your _Base Offering_ has multiple plans or versions you can configure it to have any number of `Editions` to represent all product variations.

![Edition Setup Example](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/editions-768.png)

<div class="background-accent info">

_Tips:_
* If you have a _Freemium_ option for your offering, that can be the first Edition. The nomenclature most commonly used for Freemium offerings in the Marketplace is `Express`
* The Full offering name as displayed will be `[Product Name] | [Edition Name]` so keep this in mind when naming your Editions
</div>

### Add-on

Offerings that can not stand on their own, but rather bolt onto an Product. Add-ons can be very simple - utilized as a simple billing mechanism, or can be built out with the same marketing and details as an Product. Add-ons can be used for a variety of purposes. 

_Common uses for Add-ons:_
* Services associated with a specific Product
* À la carte features that accentuate an app, and are independent of Editions.
* Hardware purchases - _Utilize Order Form for collecting shipping information_

_Using Add-ons to help fill out your billing model:_
* User Seats - _Utilize the 'End User' field on the Order From_
* Purchase of Credits - _Credits usage is managed by Vendor, and displayed in Vendor Dashboard_


**Multi-purchase**

Unlike Products, multiple Add-ons can be purchased at once

![Multi-Purchase Add-on](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/multi-buy_addon.png)

For Multi-purchasable Add-ons, the Order Form can be set to appear once, or you may require a form be filled for every instance of an Add-on purchase.

<div class="background-accent remember">

_Please Note:_

* Currently an Add-on can not be associated with more than one Product, and would need to be copied to any additional Products it applies to
* Add-on can not be associated with specific Editions. If an Add-on is activated on an Edition that doesn't support it, at this time you will need to reject the activation with an appropriate error message.
* Beyond activation, all other integration features belong to the parent Product. Trials, Reporting, SSO, Activity Stream, and Customer List data is shared with and managed by the parent Product.
</div>

## Order Forms

If your application requires additional information beyond the basic Account information you receive from the _Purchase Webhook_, or your offering is a service that isn't utilizing the Webhooks, you may configure an order form for your Product or Add-on in Vendor Center.

There are two types of order form fields

**Common Form Fields** - _fields to be auto filled if the data exists for the Account_

These fields are optional by default, but you may check them off as required.

1. Business Information
2. Salesperson – Principle Salesperson the Channel Partner has assigned to the Account
3. Contact – The first *User* from the Account. It is suggested if you need particular contact information that you add required _Custom Fields_ for this, so you can add a description indicating who's information you need, and what you will use it for.

**Custom fields**

* Provide an id that won’t change so the webhooks can operate without interruption even if label names are changed
* The optional front end validation regex uses javascript formatting

<div class="background-accent info">

_Special Field - **End User**:_
* This field provides a dropdown with all the users on the Account
* Use this to who the Admin or Workspace owner will be if this is something that your Product requires
* Use this for your Add-on if you offer user seat billing
</div>

**More information** 
For a detailed breakdown of how to build out a Marketplace Order Form check out the [Order Form Guide](https://docs.google.com/document/d/1kYCSYxcSu650aWa9fhaQnDEFiXP1jiPGdShtKK8kO80/edit?usp=sharing)

## Publishing

**Publishing Status**
![publishing status](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/publish_status.png)

Publishing your Product will allow it to be found in the [Discover Products page](https://partners.vendasta.com/marketplace/products) via Search or by checking 'Show My Products' in the filter.

**Draft Mode**

Your Product will start in draft mode when created in Vendor Center. Draft mode allows you to work on your Productlication without it being able to be seen by the rest of your company in Partner Center. If you create an Product via Partner Center, draft mode will be skipped, and the product will be immediately _Published_ & _Enabled_ allowing it to be used right away. 

<div class="background-accent remember">

_Move to Draft:_

 You may only move an Product from a Published State back to Draft if it has been [Disabled](https://support.vendasta.com/hc/en-us/articles/115001596367#h_cf09ba71-da66-4723-8b6d-b0072f540079). 
</div>

**Integration Testing**

It is suggested that if you are utilizing any of the advanced integration features that you publish your Product so that you may do integration testing since most tests require the Product to be active on an Account.

**Add-ons**

Add-ons do not currently have a versioning system. Any updates you make to your Add-on Marketing, Order Form, etc. will immediately go live.

## Pricing & Distribution

For _Free_ anyone can become a Vendasta Channel Partner, upload their offerings to the platform, and leverage Vendasta's white-labeled end to end E-Commerce Platform to sell more effectively using their own sales team. 

If you are a Software Vendor, an ISV, or a Channel Partner that wants to expand through Vendasta's 20,000+ channel partners and their 45000+ Salespeople there are _no platform fees_ for distributing and selling your product in the Vendasta Marketplace.

There is a simple revenue share based on the wholesale price of your products. Options are available for Vendor transaction fees to be rendered by _**percentage of the wholesale price**_, or based on a reseller model, where Vendasta is considered a reseller of the Vendor product. Vendasta manages billing of Channel Partners for product activations, and dispenses payment to Vendor monthly.

![product wholesale price](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/wholesaleprice.png)

With this in mind, set your wholesale price and recommended retail so that Channel Partners are enticed to offer your product, and it has a healthy margin.  We recommend setting your wholesale price to allow Channel Partners at least 30% margin on the Recommended Retail Price. Remember, the higher the margin the more attractive it becomes for Channel Partners to sell.

For details on how to set up the pricing for your offerings, please review the Pricing section under the _Product Info_ tab, then refer to the Marketplace <a href="https://docs.google.com/document/d/1NL_u4CFMoBw0p1t3UKvyQJsd6f-UX51Qj92P0wvvA2M/edit" target="_blank">Billing Models Guide</a>

**Distribution**

Once your Product has met all integration requirements, and has been reviewed and Productroved by Vendasta, your Product will be distributed to the Vendasta Channel Partner network to be discovered and sold.

---

**Next:** [Receiving Marketplace Payloads](/vendors/getting-started/receive-payload)