# Integration Requirement Checklist

Every Product that intends to be listed in the Marketplace will have to follow the applicable points outlined below **before it can be approved and distributed to all channel partners.** 


<!-- theme: danger -->
>**Signed Contract** - This should be in place before integration commences

## WebApp Checklist
 
|  | **Checklist Item**  | **Details** 
|---| --- | --- |
| ▢ | **Product Provisioning** </br>_Manual provisioning may be accomplished via Email, but this is not recommended for products with SSO or API integrations_|Vendor system implements the provisioning webhook, and at minimum the provisioned and de-provisioned actions.| 
| ▢ | **Executive Report** </br>_Applicable if Vendor Dashboard contains analytics, or if reports are sent to customers_ | Populated with Vendor data on a weekly, and monthly basis | 
| ▢ | **SSO Integration** </br>_Applicable if an End User dashboard exists for Vendor offering. Reseller level administrative dashboards may also be applicable depending on their configuration_ | OAuth2.0, SAML, or OpenIdConnect SSO must be enabled to allow user session transfer from the Vendasta Platform into the Vendor Dashboard | 
| ▢ | **Dashboard Adjustments** </br>_Required if SSO has been implemented_ |<ul><li>Navbar integrated into the SMB Dashboard</li><li>This replaces Vendor's user logout, which should be hidden</li><li>Product UI is adjusted to hide user management, unless used for permission changes</li><li>In-App upgrade CTA’s, In-App Purchases, referral programs etc. need to be hidden</li><li>Application has a valid SSL, and all data transfer during SSO uses the https protocol</li></ul>| 
| ▢ | **Activity Stream/Notifications** </br>_No emails should be sent directly from Vendor to End Users. All communications must flow through Vendasta. Legal exclusions may apply_ |<ul><li>Activity Types configured</li><li>Custom Notification Emails configured if applicable</li></ul> | 
| ▢ | **Customer List(On hold pending release of new CRM)** </br>_Applicable if Vendor gathers or utilizes customer data. i.e the SMB/SME’s patrons (*not applicable for prospects)_| Two way CRM Syncing implemented | 
| ▢ | **Vendasta White-label**| The term *‘Vendasta’* is not mentioned anywhere in the dashboard, error codes, or client side redirect paths, etc. | 

## Services

Services **require** reporting and document handoff to be within the platform. 

Client communications should run through the [Inbox](https://support.vendasta.com/hc/en-us/articles/7573902039575-Inbox-Messaging-Product-Overview#h_01HCWYPD7GAYGRVHQJ47MSKYCK) messaging system, and any shared project management be executed via the Tasks Manager [project fulfillment](https://support.vendasta.com/hc/en-us/articles/4407264708887-Fulfillment-Integration#to-begin-0-1) system. 