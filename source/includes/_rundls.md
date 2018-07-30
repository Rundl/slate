# Rundls

## Overview

A rundl is a shared digital space that brings together people and information around a business transaction.

## The rundl object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the rundl.
`name` | string | The name of the rundl.
`description` | string | The description of the rundl.
`host` | string | The host of the rundl. Gid if start, or entity when get.
`files` | list | List of Rundl file identifiers. Stage files on auth user's account via the files API prior to sending the order.
`participant_leads` | list | List of participant_lead objects
`related_rundls` | list | List of related rundls
`fields` | list | List of fields
`service` | integer | Unique identifier of the type of service for the rundl.
`service_name` | string | The name of the service
`brand_color` | string | Hexadecimal colour value. If not set on the rundl, inherits from the service.
`header_image_url` | string | Url for header image. If not set on the rundl, inherits from the service.
`metadata` | object | Context information related to the order
`state` | integer | Status of the order. See states reference.
`state_metadata` | object | Status of the order. See approval states reference.
`last_actioned_step` | object | 
`last_actioned_step_state` | object | 
`last_actioned_step_state_metadata` | object | 
`percentage_complete` | integer | 	

## Start a rundl

```shell
curl -X POST https://test-go.rundl.com/api/services/24415244/rundls?account=123456&checkout=30798521&payment_account=30736909 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <Base64-Encoded-User-Access-Token>' \
  --data '
      {
      "rundl":{
        "name":"Sale of property at 123 Baker St Caulfield Vic 3126",
        "description":"Sale of property at 123 Baker St Caulfield Vic 3126",
        "host":{
          "id":2237816
        },
        "participant_leads":[
          {
            "context":1688402,
            "roles":[
              "Real Estate Agent"
            ]
          }
        ],
        "files":[
          24415295
        ]
      }
    }
  '
```

> The above command returns JSON structured like this:

```json
{
  "actions":null,
  "id":24415298,
  "object":"rundl",
  "add_ons":[

  ],
  "brand_color":"#53c876",
  "description":"Sale of property at 123 Baker St Caulfield Vic 3126",
  "header_image_url":null,
  "host":{
    "account_id":1687398,
    "content_image_url":null,
    "description":null,
    "global_type":42,
    "id":2237816,
    "name":"Property Sales",
    "parent":{
      "account_id":1687398,
      "content_image_url":null,
      "description":null,
      "global_type":36,
      "id":1687390,
      "name":"Property Agents (Demo)",
      "parent":null,
      "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
      "video_id":null
    },
    "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
    "video_id":null
  },
  "last_actioned_step":{
    "account_id":null,
    "content_image_url":null,
    "description":"Thank you for appointing us to be your selling agents.\u000a\u000aWe look forward to achieving a great sale result for you!\u000aIf you haven't already signed the agreements please download and sign them then upload them using the blue pencil below right.",
    "global_type":39,
    "id":24415316,
    "name":"Agency and marketing agreement",
    "parent":null,
    "profile_icon_url":null,
    "video_id":null
  },
  "last_actioned_step_state":{
    "EffectiveDate":"\/Date(1518004197881+1100)\/",
    "Id":0,
    "PreviousState":null,
    "State":1
  },
  "last_actioned_step_state_metadata":{
    "created_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":42,
      "id":2237816,
      "name":"Property Sales",
      "parent":{
        "account_id":null,
        "content_image_url":null,
        "description":null,
        "global_type":36,
        "id":1687390,
        "name":"Property Agents (Demo)",
        "parent":null,
        "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
        "video_id":null
      },
      "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
      "video_id":null
    },
    "created_date":"\/Date(1518004197736+1100)\/",
    "created_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":1688398,
      "name":"Tyson Trennery",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/1199a56934177446b51650a29a24dd3a",
      "video_id":null
    },
    "id":24415317,
    "updated_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":42,
      "id":2237816,
      "name":"Property Sales",
      "parent":{
        "account_id":null,
        "content_image_url":null,
        "description":null,
        "global_type":36,
        "id":1687390,
        "name":"Property Agents (Demo)",
        "parent":null,
        "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
        "video_id":null
      },
      "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
      "video_id":null
    },
    "updated_date":"\/Date(1518004198072+1100)\/",
    "updated_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":1688398,
      "name":"Tyson Trennery",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/1199a56934177446b51650a29a24dd3a",
      "video_id":null
    }
  },
  "metadata":{
    "created_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":42,
      "id":2237816,
      "name":"Property Sales",
      "parent":{
        "account_id":null,
        "content_image_url":null,
        "description":null,
        "global_type":36,
        "id":1687390,
        "name":"Property Agents (Demo)",
        "parent":null,
        "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
        "video_id":null
      },
      "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
      "video_id":null
    },
    "created_date":"\/Date(1518004197000+1100)\/",
    "created_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":1688398,
      "name":"Tyson Trennery",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/1199a56934177446b51650a29a24dd3a",
      "video_id":null
    },
    "id":24415298,
    "updated_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":42,
      "id":2237816,
      "name":"Property Sales",
      "parent":{
        "account_id":null,
        "content_image_url":null,
        "description":null,
        "global_type":36,
        "id":1687390,
        "name":"Property Agents (Demo)",
        "parent":null,
        "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
        "video_id":null
      },
      "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
      "video_id":null
    },
    "updated_date":"\/Date(1518004197000+1100)\/",
    "updated_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":1688398,
      "name":"Tyson Trennery",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/1199a56934177446b51650a29a24dd3a",
      "video_id":null
    }
  },
  "name":"Sale of property at 123 Baker St Caulfield Vic 3126",
  "percentage_complete":0,
  "related_rundls":[

  ],
  "service":24415244,
  "service_name":"Real estate sale journey (Demo)",
  "state":{
    "EffectiveDate":"\/Date(1518004197881+1100)\/",
    "Id":0,
    "PreviousState":null,
    "State":2
  },
  "state_metadata":{
    "created_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":42,
      "id":2237816,
      "name":"Property Sales",
      "parent":{
        "account_id":null,
        "content_image_url":null,
        "description":null,
        "global_type":36,
        "id":1687390,
        "name":"Property Agents (Demo)",
        "parent":null,
        "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
        "video_id":null
      },
      "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
      "video_id":null
    },
    "created_date":"\/Date(1518004197350+1100)\/",
    "created_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":1688398,
      "name":"Tyson Trennery",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/1199a56934177446b51650a29a24dd3a",
      "video_id":null
    },
    "id":24415299,
    "updated_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":42,
      "id":2237816,
      "name":"Property Sales",
      "parent":{
        "account_id":null,
        "content_image_url":null,
        "description":null,
        "global_type":36,
        "id":1687390,
        "name":"Property Agents (Demo)",
        "parent":null,
        "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
        "video_id":null
      },
      "profile_icon_url":"https:\/\/dev-media.rundl.co\/1687390\/24415219.png",
      "video_id":null
    },
    "updated_date":"\/Date(1518004198159+1100)\/",
    "updated_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":1688398,
      "name":"Tyson Trennery",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/1199a56934177446b51650a29a24dd3a",
      "video_id":null
    }
  },
  "video":null
}
```

Start a rundl based on a service. The user context starting the rundl must have permission to start rundls for the service. The chosen host must have permission to host rundls for the service.

### HTTP Request

`POST	/services/{service_id}/rundls?account={account_id}&checkout={checkout}&payment_account={payment_account}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl
checkout | The id of a checkout resource created for the purpose of the start rundl request
payment_account | The id of the payment account for the rundl host. The host will be charged the relevant host fee.

### Path parameters

Parameter | Description
--------- | -----------
service_id | The id of the service being started

### Request body

See example request.