# Customer List

## Overview

The Customer List is a lightweight crm that allows for syncing of customer records between a central repository for an Account, and all products active on an Account that support the Customer List. It could be thought of as a hub and spoke configuration. The Customer List resides in the *Business App*, and is the hub, while all of the Products are the spokes. 

Any time a Product receives data for a new `Customer`, and syncs it to the central repository, it is subsequently synced out to all other products that are integrated with the Customer List. If a Customer is updated anywhere, that data syncs, and the record is updated everywhere.

The `Customer` entities are the clients/patrons of End Business Locations(`Accounts`) that belong to a `Partner`

``` mermaid
erDiagram
    PARTNER ||--o{ ACCOUNT : has
    ACCOUNT |o--o{ CUSTOMER : has
    ACCOUNT ||--|{ PRODUCT : has
    PRODUCT ||--o{ CUSTOMER :owns
```

## Parameter Details

Vendasta's `Customer` record utilizes an external id - `id` at time of Customer creation, and referenced as the `vendor_customer_id` thereafter. 

|  API Create    |   API List/Get        | Webhook | Description |
|----------------|-----------------------| ------- |-------------|
| N/A            | `customer_id`(string) | `contact_id` (string)  | Vendasta assigned central id|
| `id`           | `vendor_customer_id`(string) | `vendor_customer_id` (string)  | Vendor assigned customer id(Vendasta does not enforce uniqueness, though it is recommended) |
| `permission_to_contact`(boolean)]| `permission_to_contact` (date-time string)["0001-01-01T00:00:00Z" if false] | `permission_to_contact`(boolean)| Business or Vendor has confirmed customer has assented to being contacted by this Business for marketing purposes |

## Customer Syncing

Most products will maintain a two way sync between the Account's Customer List and their own list. In some situations there may be a one way sync from the Product to the central repository if the product only collects data, and doesn't have an interface for displaying or interacting with customers.

### Syncing to Product

The [Customer Webhook](https://developers.vendasta.com/vendor/ZG9jOjIxNzM0NjA3-overview#customer-webhook) will trigger when a customer is removed or added to an account, or updated. In the case of an addition, the Vendor will then utilize the [Associate Customer Ids](https://developers.vendasta.com/vendor/4e54c2707bf93-associate-customer-i-ds) endpoint to register an external id for this customer. This is **required** to be able to interact with this customer record.

### Syncing from Product

When a Product creates a customer record internally, that record should be synced to the Customer List. Utilize the [Create Customer](https://developers.vendasta.com/vendor/6f3aac2c426a2-create-customer) endpoint. 

**One way sync**

The `id` field is optional and if excluded the `vendor_customer_id` will be set to the Vendasta `customer_id`. The only reason to exclude the `id` would be if the Product only passes on Customer data to the Customer List, and doesn't have an interface to view or interact with the data on the Product itself.

**Two way sync**

A mapping between the `customer_id`/`contact_id` and the `vendor_customer_id` will need to be maintained by the Vendor to allow for full two way syncing.

<!-- theme: warning -->
> Though `account_id` isn't a required field it is suggested that a `Customer` always be associated with an Account.

### Customer Assent

Customer data regardless of what Product holds a copy of said data is assumed to be 'owned' by the Business that purchased any one of these Products.

There is a `permission_to_contact` field that needs to be observed by all Products. It will default to `false` if excluded at time of Customer creation. 

When receiving customer data via the [Customer Webhook](https://developers.vendasta.com/vendor/ZG9jOjIxNzM0NjA3-overview#customer-webhook), if this value is `false`, depending on the nature of the Product the Vendor can optionally ignore this record, or if the Product has a similar field, the record could be synced with this equivalent flag configured.

When syncing a customer to the Customer List the flag should be left as `false` unless the Vendor has explicitly ascertained consent to contact this Customer via appropriate means.

*The Customer List is not fully GDPR compliant at this time.*


## Customer Duplication

Customer records on Vendasta's side will be de-duped based on conflicts with either `email` or `phone number`.

When a duplicate is detected, the `customer_id` on the response will contain a conflict id(e.g `CONFLICT-ab2d5470-7acf-4496-ba45-946f566b2667`). The Business App doesn't display conflict records, and they aren't returned by the List API, but can be retrieved via the customer GET.

We don't have a conflict resolution system at this time, but this is something that we aim to add in the future.

**Vendor Best Practice**

It is suggested that Vendors don't proceed with Customer creation if a duplicate is detected. Alternatively the record could be created for future proofing in anticipation of a duplicate resolution feature, but not displayed.

## Testing

It will be easiest to perform QA by interacting with the [Business App](https://support.vendasta.com/hc/en-us/articles/4406958960023) Customer page.

**Customer Create: Product --> Customer List:** Check the list of customers in the Business App Customer page to see if the customer you created appears there.

**Customer Update: Product --> Customer List:** Check the customer record updated in the Business App Customer page to see if your updates were successful.

**Customer Delete: Product --> Customer List:** [List Customers](https://developers.vendasta.com/vendor/f2315cac76bc8-list-customers) endpoint should no longer return this record. Check the Business App Customer page to see if the customer you deleted is no longer displayed. 

**Customer Create: Customer List --> Product:** A customer [GET](https://developers.vendasta.com/vendor/20d4c9cb8e13b-get-customer) should succeed utilizing your assigned `vendor_customer_id`. If the product has a UI to display customers, the new customer record should be visible here.

**Customer Update: Customer List --> Product:** A customer [GET](https://developers.vendasta.com/vendor/20d4c9cb8e13b-get-customer) response should match the updated data pushed via the update. If the product has a UI to display customers, the updates to the customer record should be visible here.

**Customer Delete: Customer List --> Product:** [List Customers](https://developers.vendasta.com/vendor/f2315cac76bc8-list-customers) endpoint should no longer return this record. If the product has a UI to display customers, the customer record should no longer be visible here. 
