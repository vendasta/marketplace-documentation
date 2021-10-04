# Provisioning Workflow

An activation is considered to have been successful when a sku has been added to an _Account_ and the activation has been approved by the Vendor, or by Vendasta on behalf of the Vendor.

## Activation Types

### Trial Activation
Trials are currently activated via self serve in the Business App

![Trial Activation](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/trial_800.png)

<div class="background-accent info short">

Trials will be sent on the purchase webhook with the `action` parameter set to _provisioned-trial_
</div>

There are two steps to making a trial available to be activated.

1. As a Vendor, configure the Trial in Vendor Center. This is configured in the _Pricing & Trials_ section in the _App Info_ tab

2. As a Channel Partner, set the Trial to be available in Business App via _Marketplace -> Manage Product -> App -> Product Settings_:
![Make Trial Available](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/trial_config_800.png)

The Vendor is responsible for the trial lifecycle, including:

1. If Editions are used, informing Vendasta as to which Edition of the product should be trialed.
2. Any communications via the [Activity Stream](/vendors/integration/activity-stream) with calls to action, informing users of the trials state, when it ends, and encouraging upgrades.
3. Behavior after the trial ends.



### Product or Add-on Activation

There are many ways a sku can be activated. For testing purposes, the easiest method is via Partner Center

Activate products from the "Account Details" screen. To access this page, go to [Partner Center > Businesses > Accounts](https://partners.vendasta.com/manage-accounts) and click on the account name.
![Activate Products](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/activation_route_800.png)

Activation Options:

![Activation Example](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/product_activation_labels.png)

<div class="background-accent info short">

* Product, and Add-on purchases will be sent to their respective purchase webhooks with the `provisioned` action.
* If you have configured _Editions_ then the `edition_id` will be populated in the purchase payload, and present in the purchase email.
</div>

### Edition Change

![Edition Change](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/edition_change.gif)

_Edition Change from Partner Center Account Details Page_

<div class="background-accent info short">

The Edition Change will trigger a purchase email to be sent, which will contain the `edition_id`, as well as the Purchase Webhook with the `change-edition` action.
</div>


## Product Lifecycle

An activation can be in one of 4 states for any given Account:

![Product Lifecycle Graphic](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/11/product_lifecycle3-296x300.png)

**#1 Purchase Event:** An order has been completed, and the provisioning event triggered to be sent to the Vendor. There are multiple ways an activation could be triggered. It may be triggered by either a Reseller Admin, or a user from an end business. [Submitting an order as a Reseller Admin](https://support.vendasta.com/hc/en-us/articles/4406958134807) is the easiest way to test the triggering of an _Provisioning Event_.

**#2 Resolve Pending Activation Event:** Depending on the sku configuration, either Vendasta, or the Vendor confirms that the product is now active by calling the 'Resolve Pending Activation' endpoint in the Accounts API. This can be accomplished automatically via the API, or via the Vendor Center Accounts page -> Pending Activations(_this is hidden when there are no pending activations_).

**#3 Cancellation Event:** A sku has been [canceled](https://support.vendasta.com/hc/en-us/articles/206621228) for a given Account, and the activation goes into a _Pending De-Provisioning State_. This can be undone if the user changes their mind. Vendors may optionally subscribe to this event.

**#4 Deactivation Event:** Product is fully deactivated at the end of it's billing cycle if it is pending cancellation, or immediately upon cancellation if the sku _Billing Frequency_ is set to 'One Time'.

## Receiving order & provisioning events
Notification of a purchase and the purchase data included in the message may be received by the Vendor via Webhook AND/OR Email. These notifications are triggered when your Product, or Add-on is activated on an Account

<div class="background-accent remember">

_Please Note:_

* An Add-on can only be active when its parent Product is active.
* When a product is deactivated, all of its Add-ons are automatically deactivated.
</div>

### Email Notification

Purchase notification emails will always be sent to any recipients configured. You may add as many notification recipients as you like:

_Vendor Center -> App -> App Info -> Product Activation->Notifications_

![Add Notification Recipients](https://storage.googleapis.com/wordpress-www-vendasta/developers/2020/Notifications_800.png)

Activation email notifications can be sent in conjunction with the webhooks to keep your team in the loop. If your offering has a dashboard, and you are providing SSO it is not recommended to use email as the sole source of your product provisioning as it is a manual process. The email will contain the order form, but doesn't include all of the Account data that the webhook does.

### Webhooks

The Purchase Webhooks will receive the basic data that is available on the Account, as well as the order form data if an order form is configured for the Application.

See the [Webhooks Page](/vendors/web-hooks#product-purchase-hook) for details on the payloads. 

## Pending Activation & Possible Workflows

![Activation Workflow](https://storage.googleapis.com/wordpress-www-vendasta/developers/2019/Activation%20Workflow.png)

## Configuration

Apps & Add-ons have individual Purchase webhooks. Both of these Webhooks are configured on the Integration page of the Parent App.

![Purchase Webhook Configuration](https://storage.googleapis.com/wordpress-www-vendasta/developers/2018/11/PurchaseWebhooks.jpg)

### Mapping Accounts

|Field| Description
| --------------|:----|
|`action`|  _provision/de-provision/trial/change-edition_ The action field indicates which payload type you are receiving. The Purchase Webhook is sent for purchase and deactivation events, as well as for trial activations, and edition changes.|
|`account-->id`| This is the unique Vendasta ID representing a Vendasta Account. There should be a 1:1 relationship between a Vendasta Account and an instance of the Vendor Product. This field has the key 'id', and is found within the account object in the purchase webhook json. |
|`activation_id` | This is a unique activation tracking id, it will be required to [resolve a Pending Activation](https://developers.vendasta.com/swaggerui#/account/post_activation_resolve_).|
|`order_form_submission_id` |This is a unique id for tracking order form data from an Activation.

## Testing

See the [Receiving Marketplace Payloads](/vendors/getting-started/receive-payload) page for details on using the testing tool in Vendor Center for sending mock activations. If you require order form data to provision your sku, you should test the activations from Partner Center, as the mock data will not suffice. 
You can create as many test Accounts as you want in Partner Center, and activate and deactivate your own offerings as much as you desire.

---

**Next:** [Session Transfer](/vendors/integration/session-transfer)
