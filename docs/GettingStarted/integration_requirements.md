# Integration Requirement Checklist

Every Product that intends to be listed in the Marketplace will have to follow the applicable points outlined below **before it can be approved and distributed to all channel partners.** 


<!-- theme: danger -->
>**Signed Contract** - This should be in place before integration commences

## WebApp Checklist
 
|  | **Checklist Item**  | **Details** 
|---| --- | --- |
| ▢ | **[Product Provisioning](../Guides/provisioning_workflow.md)** </br>_Vendors may receive orders via Email or Webhook. **Email is not recommended for products with SSO or API integrations**_|Automated provisioning recommended, in which case the Vendor implements the provisioning webhook, with the `provisioned` and `de-provisioned` actions at minimum| 
| ▢ | **[Executive Report](../Guides/reporting.md)** </br>_Applicable if Vendor dashboard contains analytics, or if reports are sent to customers_ | Populated with Vendor data on a **weekly & monthly** basis | 
| ▢ | **[SSO Integration](../Guides/oauth2.md)** </br>_Applicable if an End User dashboard exists for Vendor sku when sold direct. Reseller level administrative dashboards may also be applicable depending on their configuration_ | OAuth2.0, SAML, or OpenIdConnect SSO must be enabled to allow user session transfer from the Vendasta Platform into the Vendor Dashboard | 
| ▢ | **Dashboard Adjustments** </br>_Required if [SSO](../Guides/oauth2.md) has been implemented_ |<ul><li>Navbar integrated into the SMB Dashboard</li><li>Logout button on Navbar replaces Vendor's user logout, which should be hidden</li><li>User creation, deletion, and password change elements should be hidden as these functions are now managed by Vendasta as your Identity Provider. The only user management that should be exposed is role management.</li><li>In-App upgrade CTA’s, In-App Purchases, referral programs, sales, etc. need to be hidden</li><li>Application must have a valid SSL, and all data transfer during SSO use the https protocol</li></ul>| 
| ▢ | **[Activity Stream/Notifications](../Guides/activity_stream_overview.md)** </br>_No emails should be sent directly from the Vendor to End Users. All communications must flow through Vendasta. Legal exclusions may apply_ |<ul><li>Activity Types configured</li><li>Custom Notification Emails configured if applicable</li></ul> | 
| ▢ | **Customer List(On hold pending integration with new CRM)** </br>_Applicable if Vendor gathers or utilizes customer data. i.e the SMB/SME’s patrons (or prospects)_| Two way CRM Syncing implemented | 
| ▢ | **Vendasta White-label**| The term *‘Vendasta’* is not mentioned anywhere in the dashboard, error codes, or client side redirect paths, etc. _For details on optional product branding see [White-label Support](../Guides/whitelabeling.md)_ | 

## Services

Services **require** reporting and document handoff to be within the platform. Some services may require no technical integration at all. See the [Vendor Services Guide](https://support.vendasta.com/hc/en-us/articles/11468617792279-Service-Vendor-Requirements-for-Marketplace-Vendors) for more information.

**Options for file sharing:**

* [File Group Upload](../reference/marketplace-api.yaml/paths/~1account~1{account_id}~1file-group~1/post) (Account Scope)
* [Inbox File Messages](https://support.vendasta.com/hc/en-us/articles/24292844456599-Inbox-Messages-in-Partner-Center) (Partner Scope)
* Task Manager Project File transfer (Account-->Order/Project Scope)


Client communications should run through the [Inbox](https://support.vendasta.com/hc/en-us/articles/7573902039575-Inbox-Messaging-Product-Overview#h_01HCWYPD7GAYGRVHQJ47MSKYCK) messaging system, and any shared project management be executed via the Tasks Manager [project fulfillment](https://support.vendasta.com/hc/en-us/articles/4407264708887-Fulfillment-Integration#to-begin-0-1) system. 