# Orders

## Overview

Take control of acquiring new customers and starting transactional work. Service providers can offer services to their customers and referral partners and track opportunities sent as orders.

## The order object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the order.
`service` | integer | Unique identifier for the service being ordered.
`subject` | string | Summary of the order.
`detail` | string | Details about the order, such as a message or description.
`sender` | object | Sender of the order as a participant_lead object.
`participant_leads` | list | List of participant_lead objects
`files` | list | List of Rundl file identifiers. Stage files on auth user's account via the files API prior to sending the order.
`status` | integer | Status of the order. See approval states reference.
`referral_fee` | string | Referral fee currency amount payable to the sender.
`referral_fee_currency` | string | Three-letter ISO currency code. Currently only AUD supported.
`source` | object | Information about the source of the order.
`rundl` | object | Information about the rundl to create when accepting an order.
`request_id` | integer | The unique identifier of the related request. Currently orders are coupled with requests. This will soon be deprecated and orders will stand alone.
`metadata` | object | Context information related to the order
`version` | integer | 	

## Order a service

```shell
curl -X POST https://test-go.rundl.com/api/orders?account=286894 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth Q2owOEdEekpmamFLdU5xMVNiYTYyNmpqak9ENGtaMjI1RFFmWjJYY2MxVzBRRWxZT3kzOUpCck9pcVFadzY2MUxRdw==' \
  --data '
    {
      "order": {
        "service":23225632,
        "subject":"123 Baker St Caulfield (Demo)",
        "detail":"Sale of property at 123 Baker St Caulfield Vic 3126 ",
        "sender":{
          "context":873900,
          "roles":["Seller"],
          "person":{"id":873900}
        },
        "participant_leads":[
          {
            "person":{
              "first_name":"Rod",
              "surname":"Montford",
              "email":"rmontford@gmail.com",
              "mobile":"+61412356789"
            },
            "roles":["Seller"]
          }
        ]
      }
    }
  '
```

> The above command returns JSON structured like this:

```json
{
  "detail":"Sale of property at 123 Baker St Caulfield Vic 3126 ",
  "files":null,
  "id":23226946,
  "metadata":{
    "created_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":873900,
      "name":"Zoe Montford",
      "parent":null,
      "profile_icon_url":null,
      "video_id":null
    },
    "created_date":"\/Date(1513314441677+0000)\/",
    "created_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":873900,
      "name":"Zoe Montford",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/e7392fe26191f428e9a8e8f7d9aee55c",
      "video_id":null
    },
    "id":23226946,
    "updated_context":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":873900,
      "name":"Zoe Montford",
      "parent":null,
      "profile_icon_url":null,
      "video_id":null
    },
    "updated_date":"\/Date(1513314441677+0000)\/",
    "updated_user":{
      "account_id":null,
      "content_image_url":null,
      "description":null,
      "global_type":43,
      "id":873900,
      "name":"Zoe Montford",
      "parent":null,
      "profile_icon_url":"https:\/\/secure.gravatar.com\/avatar\/e7392fe26191f428e9a8e8f7d9aee55c",
      "video_id":null
    }
  },
  "participant_leads":[
    {
      "context":null,
      "id":23226951,
      "person":{
        "email":"rmontford@gmail.com",
        "first_name":"Rod",
        "id":null,
        "mobile":"+61 412 356 789",
        "surname":"Montford"
      },
      "roles":[
        "Seller"
      ],
      "subscribe":null
    }
  ],
  "referral_fee":null,
  "referral_fee_currency":"AUD",
  "request_id":23226952,
  "rundl":null,
  "sender":{
    "context":873900,
    "id":23226950,
    "person":{
      "email":null,
      "first_name":null,
      "id":873900,
      "mobile":null,
      "surname":null
    },
    "roles":[
      "Seller"
    ],
    "subscribe":null
  },
  "service":23225632,
  "source":null,
  "status":0,
  "subject":"123 Baker St Caulfield (Demo)",
  "version":1
}
```

Order a service from a Rundl service provider. The user context sending the order must have permission to order the service. Service settings control which contexts can order.

### HTTP Request

`POST	/orders`

### Query parameters

Parameter | Description
--------- | -----------
account | The id of the selected account when ordering the service

### Path parameters

### Body parameters


<aside class="success">Remember — a happy kitten is an authenticated kitten!</aside>

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>
