# Rundl messages

## Overview

A rundl message is a communication sent within a rundl to some or all participants. A message may have attached files and links. Permissions can be set to control who can see the message.

## The message object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the rundl.
`text` | string | The message content
`privacy` | integer | Privacy category information.
`parent` | object | Object representing the parent rundl entity related to the message.
`metadata` | object | Context information related to the message
`pin` | object | Information about the pinning of this message in the rundl.
`comments` | list | Comments attached to the message.
`links` | list | Links attached to the message.
`files` | list | Files attached to the message.
`reminders` | list | Reminders attached to the message.

## Post a message

```shell
curl -X POST https://stage-go.rundl.com/api/rundls/42980836/messages?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
      {
        "message":{
          "text":"Message here",
          "actions":[
            {
              "name":"add",
              "id":42981063,
              "object":"file"
            }
          ]
        }
      }
  '
```

> The above command returns JSON structured like this:

```json
{
  "id":42981072,
  "object":"message",
  "version":null,
  "version_latest":null,
  "comments":{
    "data":[],
    "object":"list",
    "paging":{
      "next":null,
      "previous":null,
      "strategy":0
    }
  },
  "files":{
    "data":[
      {
        "id":42981063,
        "object":"file",
        "version":4,
        "version_latest":null,
        "category":2,
        "content_type":"image\/jpeg",
        "metadata":{
          ...
        },
        "name":"handshake.jpg",
        "privacy":6,
        "published":true,
        "related_entities":[
          ...
        ],
        "size":233106,
        "status":1,
        "thumb_urls":[
          ...
        ]
      }
    ],
    "object":"list",
    "paging":{
      "next":null,
      "previous":null,
      "strategy":0
    }
  },
  "links":{
    "data":[],
    "object":"list",
    "paging":{
      "next":null,
      "previous":null,
      "strategy":0
    }
  },
  "metadata": {
    ...
  },
  "parent":{
    "id":42980836,
    "object":"entity_identity",
    "version":null,
    "version_latest":null,
    "account_id":null,
    "content_image_url":null,
    "description":"Sale of property at 123 Baker St Caulfield Vic 3126",
    "global_type":32,
    "name":"Sale of property at 123 Baker St Caulfield Vic 3126",
    "parent":null,
    "profile_image_urls":null,
    "user_id":null,
    "video_id":null
  },
  "pin":null,
  "privacy":2,
  "reminders":{
    "data":[],
    "object":"list",
    "paging":{
      "next":null,
      "previous":null,
      "strategy":0
    }
  },
  "text":"dsadsa"
}
```

Post a message to a rundl. The user context posting the message must have permission to collaborate on the rundl. .

### HTTP Request

`POST	/rundls/{rundl_id}/messages?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl

### Path parameters

Parameter | Description
--------- | -----------
rundl_id | The id of the rundl being posted to

### Request body

See example request.