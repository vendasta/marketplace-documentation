# Integration Requirement Checklist

Every Product that is integrated into the Marketplace will have to follow the points outlined below **before it can be approved and launched to all channel partners.** Please read through these points carefully and don't hesitate to contact us with any questions. We will help you with templates and guidance throughout the process.

## Legal

<!-- theme: danger -->
>**Signed Contract** - This should be in place before integration commences

## Operations

**Operations Onboarding**

Scheduled meeting with operations team to discuss pricing, billing, payments, support, and any related questions. 

**Pricing Structure Document Completed** 

*Source of truth post product launch* - A Google Doc will be shared with you after onboarding. This will be used to lay out the structure, currency, and pricing of your offerings at time of launch.

**Payment Method** 

*Payouts monthly, and will be completed by the 15th of the month* 

*Credit Card* - A secure link to accept payments is required for credit card payments. The payment request must be submitted to marketplace@vendasta.com no later than 10 days after the monthly statement remittance from Vendasta is received. AMEX must be an accepted form of payment.

*ACH* - A bank letter will be required for ACH payments. A bank letter can be requested from your bank if you don’t have one already. It must include the bank account information on bank letterhead. Note, at our discretion Vendasta may withhold payment if the bank fees are higher than the payment. Once the total accumulated payment is of a substantial amount, the entire sum will be paid at once.

**Support Contact Information** 

Update contact information in Vendor Center:
* Vendor Contact Information for sales related support. _Product Info-->The Basics-->Vendor Contact Email_
* 'Need Help?' for technical or general support. _Marketing-->Need help?_


<!-- theme: warning -->
>_Please Review:_
>
>[Supported Billing Models](https://docs.google.com/document/d/1NL_u4CFMoBw0p1t3UKvyQJsd6f-UX51Qj92P0wvvA2M/edit#heading=h.nscvy0c6qcnm)
>
>[Vendor Billing Guidelines](https://docs.google.com/document/d/1QYHHyw66OPEmhvYmne8b-liJHTbMNp8IO4wszu_6sHc/edit#heading=h.fw1y7oviytoe)


## Technical

***All requirements need to be implemented prior to the distribution of your product to the Vendasta reseller network.***

### Requirement 1: Provisioning Workflow

**Product Provisioning** 
 
Vendor system implements the provisioning webhook, and at minimum the provisioned and de-provisioned actions. *For services without a dashboard, manually provisioning may be accomplished via Email. We suggest using the Vendasta Task Manager for fulfillment in these instances.*

<!-- theme: info -->
>*Optional Order Form*
>
>If addtional data is required for Vendor to provision and fulfill order. Will be reviewed and verified by Vendasta team member before launch.

### Requirement 2: SSO

**SSO Integration** 

*_Applicable if an End User dashboard exists for Vendor offering. We don’t currently support Reseller Dashboards. Reseller dashboard functionality is managed through the integration_

OAuth2.0 or OpenIdConnect SSO must be enabled from the Vendasta Business App to the Product dashboard.

**Dashboard Adjustments** 

*_Required if SSO has been implemented_

* Navbar integrated into the SMB Dashboard
* This replaces Vendor's user logout, which should be hidden
* Product UI is adjusted to hide user management, unless role management is required
* In-App upgrade CTA’s, In-App Purchases, referral programs etc. need to be hidden.

**SSL**

Application has a valid SSL, and all communications during SSO use the https protocol.  


### Requirement 3: Reporting

**Executive Report** 

*_Applicable if Vendor Dashboard contains analytics, or if reports are sent to customers_

Raw data is pushed to the Vendasta vAnalytics API. Vendasta aggregates and displays data based on pre-determined charting templates.

### Requirement 4: Communications

**Activity Stream/Notifications**

* No emails should be sent directly from Vendor to End Users. All communications must flow through Vendasta. *Legal  and technical exclusions may apply.*

### Requirement 5: CRM Syncing

**Customer List** 

*Applicable if Vendor gathers or utilizes customer data. i.e the SMB/SME’s patrons* 

Two way CRM Syncing implemented

### Requirement 6: White-labeling

The term *‘Vendasta’* is not mentioned anywhere in the dashboard, error codes, or client side redirect paths, etc. 


## Marketing

Please Review the [Marketing Guidelines Document](https://docs.google.com/document/d/15XmlwdsE5QeAxHVhZTb3RE6wkRHQbtRLgjTo4GuaCeg/edit?usp=sharing)

**Training**
- One pager reference about the solution
- 30 minute Training and Q&A with the Vendasta Sales Team

## Ongoing Product Updates

<!-- theme: info -->
>_Please Note:_
>
>Once your Product has been released, changes made to it in Vendor Center require approval and publishing from a Vendasta Admin before those changes are reflected in Marketplace. 
>
>Please request a review from the Compare Products tab
