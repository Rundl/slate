# Checkouts

## Overview

A checkout represents the attributes of in-app purchase to be made on Rundl. Creating a checkout is required before making a purchase, such as when starting a rundl or consuming an add-on. The checkout id is passed as a URL parameter to APIs related to purchase flows.

## The checkout object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the checkout.
`object` | string | String representing the object's type. Value is checkout
`seller` | object | The [gid object](#the-gid-object) for the seller's account.
`buyer` | object | The [gid object](#the-gid-object) for the buyer's account.
`status` | string | Status of the order. See states reference.
`product_order` | integer | Unique identifier for the product order created after the checkout and subsequent steps in the purchase workflow. 
`items` | list | A list of checkout items.
`items_total_price` | string | The total price of all the checkout items.
`total_tax` | string | The total tax of the checkout.
`total_price` | string | The total price of the checkout including tax.
`currency` | string | ISO 4217 three-letter code.
`payment_account_types` | string array | Options: `credit_card`, `manual` 
`payment_token` | string | Security token for credit card checkouts
`related` | integer | unique identifier of the related entity being purchased
`service` | integer | Unique identifier of the service type related to a rundl checkout
`rundl` | integer | Unique identifier of the rundl related to the checkout
`created_date` | string | Date the resource was created.

## The (checkout) item object

Attribute | Type | Description
--------- | ------- | -----------
`product` | object | The [product](#the-product-object) being purchased.
`quantity` | string | The quantity of the product being purchased.
`amount` | string | The amount of the checkout item being purchased.
`tax_id` | integer | Unique identifier for the relevant tax for the checkout item.
`tax_amount` | string | The amount of tax for the product being purchased.
`tax_status` | integer | List of participant_lead objects
`tax_lines` | list | List of [tax_line](#the-tax_line-object) items
`reference` | list | 
`data` | integer | 

## The product object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the product.
`object` | string | String representing the object's type. Value is product
`name` | string | The name of the product.
`price` | string | The price of the product.
`currency` | string | ISO 4217 three-letter code.

## The tax_line object

Attribute | Type | Description
--------- | ------- | -----------
`amount` | integer | The percentage amount of the tax.
`name` | string | The name of the tax.

## Create a checkout

```shell
curl -X POST https://test-go.rundl.com/api/checkouts?account=17357980 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <Base64-Encoded-User-Access-Token>' \
  --data '
    {
      "checkout": {
        "service": 30798101,
        "payment_token": null,
        "items": [
          {
            "product": {
              "id": 30798131
            },
            "quantity": 1
          }
        ]
      }
    }
  '
```

> The above command returns JSON structured like this:

```json
{
  "id": 30799601,
  "object": "checkout",
  "buyer": {
    "global_type": 0,
    "id": 17357980,
    "name": "World Conveyancing"
  },
  "created_date": "/Date(1532940986653+0000)/",
  "currency": "AUD",
  "items": [
      {
        "amount": 0,
        "data": null,
        "product": {
            "id": 30798131,
            "object": "product",
            "currency": "AUD",
            "name": "kzi68b4 hosted rundl",
            "price": 0
        },
        "quantity": 1,
        "reference": null,
        "tax_amount": 0,
        "tax_id": 30734732,
        "tax_lines": [
            {
              "amount": 0,
              "name": "GST"
            }
        ],
        "tax_status": 1
      }
  ],
  "items_total_price": 0,
  "payment_account_types": [
    "credit_card",
    "manual"
  ],
  "payment_token": null,
  "product_order": null,
  "related": null,
  "rundl": null,
  "seller": {
    "global_type": 0,
    "id": 286896,
    "name": "Rundl Pty Ltd"
  },
  "service": 30798101,
  "status": 0,
  "total_price": 0,
  "total_tax": 0
}
```

Create a checkout for a product related to a service.

### HTTP Request

`POST	/checkouts?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl


### Request body

See example request.