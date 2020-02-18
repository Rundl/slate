# Rundls

## Overview

A rundl is a shared digital space that brings together people and information around a business transaction.

## The rundl object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the rundl.
`name` | string | The name of the rundl.
`description` | string | The description of the rundl.
`video` | string | Ignore. Shouldn't be here.
`service` | integer | Unique identifier of the type of service for the rundl.
`service_name` | string | The name of the service
`service_profile_banners` | list | Object containing sized image urls.
`order` | integer | Unique identifier for an order, where the rundl was initiated from order flow.
`model_id` | integer | Unique identifier for service model.
`brand_color` | string | Hexadecimal colour value. If not set on the rundl, inherits from the service.
`profile_banners` | list | List of sized image urls.
`metadata` | object | Context information related to the rundl
`state` | object | Status of the order. See approval states reference.
`last_actioned_step` | object | Object representing the step that was last updated.
`last_actioned_step_state` | object | Object representing the status of the step that was last updated.
`last_actioned_step_state_metadata` | object | Object with standard metadata relating to the last status update.
`percentage_complete` | integer | 
`reference` | string | Host's own reference for this rundl. Optionally overridden by each participant.
`host` | string | The host of the rundl. Gid if start, or entity when get.
`related_rundls` | list | List of related rundls
`add_ons` | list | Expandable. List of add-ons enabled for this rundl.

## Variant rundl object to support start rundl only

Attribute | Type | Description
--------- | ------- | -----------
`name` | 
`description` | 
`host` | 
`fields` | list | List of fields
`subscriptions` | list | List of event subscriptions
`related_rundls` | list | List of related rundl identifiers
`files` | list | List of Rundl file identifiers. Stage files on auth user's account via the files API prior to sending the order.
`participants` | list | List of participant objects


## Start a rundl

```shell
curl -X POST https://stage-go.rundl.com/api/services/24415244/rundls?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
      {
        "rundl":{
          "name":"Sale of property at 123 Baker St Caulfield Vic 3126",
          "description":"Sale of property at 123 Baker St Caulfield Vic 3126",
          "host":{
            "id":2237816
          },
          "participants":[
            {
              "contact":{
                "first_name":"Demo",
                "surname":"User",
                "email":"demo_user@gmail.com",
                "mobile":"+61411111111"
              },
              "roles":[
                "Seller"
              ]
            }
          ],
          "files":[
            42980832
          ]
        }
      }
  '
```

> The above command returns JSON structured like this:

```json
{
  "id":42980836,
  "object":"rundl",
  "version":null,
  "version_latest":null,
  "add_ons":null,
  "brand_color":"#53C876",
  "description":"Sale of property at 123 Baker St Caulfield Vic 3126",
  "host":{
    "id":2237816,
    ...
  },
  "last_actioned_step":null,
  "last_actioned_step_state":null,
  "last_actioned_step_state_metadata":null,
  "metadata":{
    ...
  },
  "model_id":42979888,
  "name":"Sale of property at 123 Baker St Caulfield Vic 3126",
  "order":null,
  "percentage_complete":0,
  "profile_banners":null,
  "reference":null,
  "related_rundls":[],
  "service":42979887,
  "service_name":"Selling your home with Property Agents",
  "service_profile_banners":null,
  "state":{
    ...
  },
  "video":null
}
```

Start a rundl based on a service. The user context starting the rundl must have permission to start rundls for the service. The chosen host must have permission to host rundls for the service.

### HTTP Request

`POST	/services/{service_id}/rundls?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl

### Path parameters

Parameter | Description
--------- | -----------
service_id | The id of the service being started

### Request body

See example request.