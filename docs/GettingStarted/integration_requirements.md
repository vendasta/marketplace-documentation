# Integration Requirement Checklist

Every Product that is integrated into the Marketplace will have to follow the points outlined below before it can be approved and launched to all channel partners. Please read through these points carefully and don't hesitate to contact us with any questions. We will help you with templates and guidance throughout the process.

## Legal

<!-- theme: danger -->
>**Signed Contract** - This should be in place before integration commences

## Operations

![Operations Requirements](https://storage.googleapis.com/wordpress-www-vendasta/developers/2021/operations_requirements_1.png)


<!-- theme: warning -->
>_Please Review:_
>
>[Supported Billing Models](https://docs.google.com/document/d/1NL_u4CFMoBw0p1t3UKvyQJsd6f-UX51Qj92P0wvvA2M/edit#heading=h.nscvy0c6qcnm)
>
>[Vendor Billing Guidelines](https://docs.google.com/document/d/1QYHHyw66OPEmhvYmne8b-liJHTbMNp8IO4wszu_6sHc/edit#heading=h.fw1y7oviytoe)


## Technical

Requirement | Description
---------|----------
|**Authentication Token** </br> *Marketplace API Authentication*| Cached for 24hrs, and properly refreshes upon expiry, or upon receiving a 401 response from API.
 **Product Provisioning** | Vendor system implements the provisioning webhook, and at minimum the provisioned and de-provisioned actions. *For services without a dashboard, manually provisioning may be accomplished via Email. We suggest using the Vendasta Task Manager for fulfillment in these instances.* | 
**Order Form** </br> *Optional * | If required for Channel Partners to submit new orders. Should be reviewed and verified by Vendasta team member before launch.
**SSL** | Application has a valid SSL, and all communications during SSO use the https protocol.  
**Executive Report** </br> *Applicable if Vendor Dashboard contains analytics, or if reports are sent to customers* | Populated with Vendor data on a weekly, and monthly basis.
**SSO Integration** </br> *Applicable if an End User dashboard exists for Vendor offering. We don’t currently support Reseller Dashboards* | OAuth2.0 or OpenIdConnect SSO must be enabled from the Vendasta Business App to the Vendor Dashboard
**Dashboard Adjustments** </br> *Required if SSO has been implemented* | <ul><li>Navbar integrated into the SMB Dashboard</li><li>This replaces Vendor's user logout, which should be hidden</li><li>Product UI is adjusted to hide user management, unless used for permission changes</li><li>In-App upgrade CTA’s, In-App Purchases, referral programs etc. need to be hidden.</li></ul>
**Activity Stream/Notifications**
*No emails should be sent directly from Vendor to End Users. All communications must flow through Vendasta. Legal exclusions may apply.* | * Activity Types configured
* Custom Notification Emails configured if applicable | 
**Customer List** </br> *Applicable if Vendor gathers or utilizes customer data. i.e the SMB/SME’s patrons* | Two way CRM Syncing implemented | 
**Vendasta White-label** | The term *‘Vendasta’* is not mentioned anywhere in the dashboard, error codes, or client side redirect paths, etc. 


## Marketing

<!-- theme: warning -->
>Please Review the [Marketing Guidelines Document](https://docs.google.com/document/d/15XmlwdsE5QeAxHVhZTb3RE6wkRHQbtRLgjTo4GuaCeg/edit?usp=sharing)

**Training**
- One pager reference about the solution
- 30 minute Training and Q&A with the Team

## Ongoing Product Updates

<!-- theme: info -->
>_Please Note:_
>
>Once your Product has been released, changes made to it in Vendor Center require approval, and publishing from a Vendasta Admin before those changes are reflected in Marketplace. You must let a Vendasta Admin know when you have updates that you want reviewed and published.
</div>
