# Orders

## Overview

Receive new orders for transactional work. Service providers can offer services to their customers and referral partners and track opportunities sent as orders.

## The order object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the order.
`summary` | string | Summary of the order.
`detail` | string | Details about the order, such as a message or description.
`sender` | object | Order sender as a participant object. Only returned in responses. Use participants attribute when creating orders.
`receiver` | object | Order receiver as a participant object. Only returned in responses. Automaticallay assiged after creating orders.
`referrer` | object | Order referrer as a participant object. Only returned in responses. Automaticallay assiged after creating orders. See referrer_code attribute.
`state` | object | Status of the order. See approval states reference.
`referrer_code` | string | Code to identify the order referrer.
`rundl` | object | Information about the rundl that's started when accepting an order. Each order has an associated preliminary rundl to support collaboration on the order.
`service` | object | Identifies the service being ordered.
`metadata` | object | Context information related to the order
`participants` | list | List of participants. Not returned in responses. Attribute only support supplying participants when creating orders.
`fields` | list | List of fields. Not returned in responses. Attribute only support supplying custom field data when creating orders.
`actions` | list | List of actions for additional operations on the order. e.g. to associate optional Files or a Real Property where Rundl Property AU addon is enabled for the service.

## Order a service

```shell
curl -X POST https://stage-go.rundl.com/api/orders?account=123456 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
    {
      "order": {
        "service":{
          "id":23225632
        },
        "summary":"123 Baker St Caulfield (Demo)",
        "detail":"Sale of property at 123 Baker St Caulfield Vic 3126 ",
        "participants":[
          {
            "categories": ["sender"],
            "context":{
              "id":574997
            },
            "roles":["Seller"]
          },
          {
            "contact": {
              "first_name":"Rod",
              "surname":"Montford",
              "email":"rmontford@email.com",
              "mobile":"+61412356789"
            },
            "roles":["Seller"]
          }
        ],
        "actions":[
          {
            "name":"add",
            "id":123456,
            "object":"file"
          },
          {
            "name":"add",
            "id":123456,
            "data":"{\"id\":123456,\"version\":1}",
            "object":"realproperty"
          }
        ]
      }
    }
  '
```

> The above command returns JSON structured like this (some objects truncated for brevity):

```json
{
  "id":42704777,
  "object":"order",
  "version":1,
  "version_latest":null,
  "detail":"123 Baker St Caulfield Vic 3126",
  "metadata":{ ... },
  "receiver":{ ... },
  "referrer":{ ... },
  "referrer_code":"yzfZxGNL",
  "rundl":null,
  "sender":{ ...},
  "service":{ ... },
  "state":{ ... },
  "summary":"Sale of property at 123 Baker St Caulfield Vic 3126"
}
```

Order a service from a Rundl service provider. The user context sending the order must have permission to order the service. Service settings control which contexts can order.
Example shows optional actions to attach additional resources such as files or a realproperty.

### HTTP Request

`POST	/orders?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when ordering the service
referrer_code | A code that can be associated with the order. If the code (stored in Rundl against a contact) is assigned a context, the context will be added to the order as referrer.

### Request body

See example request.