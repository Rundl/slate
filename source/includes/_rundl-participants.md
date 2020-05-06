# Rundl participants

## Overview

Participants are the stakeholders in the business transaction that's getting delivered as a rundl. 

The participants API supports adding existing contexts (users, groups, or members and teams within groups) via the *context* attribute. Contexts can actively participate in rundls. For existing contexts, clients can implement the search API to find existing users/groups. For groups, clients can optionally query the Contexts API to find available member of team contexts for that group. 

<aside class="notice">
When adding an existing context, a context's settings determines if a full participant is added, or if a pending participant is added and a request is created for their approval. Where a request is created, the participant transitions from pending to full after accepting the request.
</aside>

The participants API also supports inviting a new user by supplying person details and an email address via the *contact* attribute. A *pending* user is made the participant context and an invitation to join Rundl is sent to the address. If the receiver accepts the invitation (by clicking the sign up link in the invitation email) the pending user and participant transitions to a full. 

The participants API also supports adding passive participant via the *contact* attribute and setting *category* to *virtual*.Virtual participants may be updated to full participants later by setting the *category* to *participant*.

## The participant object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | The id of the participant.
`categories` | string array | List of strings to represent participant category. <br/>Options: `participant`, `virtual`, `sender`, `receiver`, `referrer`
`rundl` | integer | The id of the rundl the participant is added to.
`context` | object | The context that is participating. <br/>`Expandable`
`contact` | object | The contact associated with the participant. <br/>`Expandable`
`status` | object | Status of the participant. 
`created_date` | The date the participant was added.
`roles` | string array | Array of role names asigned to the participant.
`is_host` | boolean | Flag to indicate if participant is the host.
`observer` | boolean | Flag for making participant an observer.
`message` | string | Message included in invitation email when adding a participant.
`reference` | string | The participant's own reference for the rundl they're participating in.

## Add a rundl participant

<aside class="notice">
1. When attempting to add a new user via the *contact* attribute, if the supplied email is verified for an existing user, the existing user is simply added/invited.<br/>
2. If no category is assigned, the new participant will default to the `virtual`.
</aside>

```shell
curl -X PUT https://stage-go.rundl.com/api/rundls/123456/participants?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
      {
        "participant":{
          "rundl":42980836,
          "message":"I would like to add you as a participant in \"Sale of property at 123 Baker St Caulfield Vic 3126\".",
          "observer":false,
          "contact":{
            "first_name":"Demo",
            "surname":"User",
            "mobile":"+61411111111",
            "email":"demo_user@rundl.com"
          },
          "roles":[
            "Default"
          ]
        }
      }
  '
```

> The above command returns JSON structured like this:

```json
{
  "id":42980998,
  "object":"participant",
  "version":null,
  "version_latest":null,
  "categories":[
    "virtual"
  ],
  "contact":{
    "id":42980996,
    "object":"contact",
    "version":1,
    "version_latest":1,
    "account_id":1687398,
    "display_name":"Demo User",
    "email":"demo_user@rundl.com",
    "first_name":"Demo",
    "metadata":{
      ...
    },
    "mobile":"+61 411 111 111",
    "referrer_codes":null,
    "state":2,
    "surname":"User"
  },
  "context":null,
  "created_date":"\/Date(1581998394354+0000)\/",
  "is_host":false,
  "message":null,
  "observer":false,
  "order":null,
  "reference":null,
  "roles":[
    "Default"
  ],
  "rundl":42980836,
  "status":1
}
```

### HTTP Request

`POST	/rundls/{rundl_id}/participants?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
`account_id` | The id of the selected account when starting the rundl.

### Path parameters

Parameter | Description
--------- | -----------
`rundl_id` | The id of the rundl the participant is added to.

### Request body

See example request.


## Update a rundl participant

For example, upgrade a virtual participant to full participant.

```shell
curl -X PATCH https://stage-go.rundl.com/api/rundls/123456/participants/25577359?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
      {
        "participant":{
          "categories":[
            "participant"
          ],
          "id":42980998
        }
      }
  '
```

> The above command returns JSON structured like this:

```json
{
  "id":42980998,
  "object":"participant",
  "version":null,
  "version_latest":null,
  "categories":[
    "participant"
  ],
  "contact":{
    "id":42980996,
    "object":"contact",
    "version":1,
    "version_latest":1,
    "account_id":1687398,
    "display_name":"Demo User",
    "email":"demo_user@rundl.com",
    "first_name":"Demo",
    "metadata":{
      ...
    },
    "mobile":"+61 411 111 111",
    "referrer_codes":null,
    "state":2,
    "surname":"User"
  },
  "context":{
    "id":42981002,
    "object":"context",
    "version":null,
    "version_latest":null,
    "account_id":null,
    "content_image_url":null,
    "description":null,
    "global_type":43,
    "name":"Demo User",
    "parent":null,
    "profile_image_urls":null,
    "user_id":null,
    "video_id":null,
    "is_current":null,
    "privacy":null,
    "team_type":null
  },
  "created_date":"\/Date(1581998394354+0000)\/",
  "is_host":false,
  "message":null,
  "observer":false,
  "order":null,
  "reference":null,
  "roles":[
    "Default"
  ],
  "rundl":42980836,
  "status":1
}
```

Updates the participant's roles or toggles being an observer.

### HTTP Request

`PATCH	/rundls/{rundl_id}/participants/{participant_id}?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
`account_id` | The id of the selected account when starting the rundl.

### Path parameters

Parameter | Description
--------- | -----------
`rundl_id` | The id of the rundl the participant is added to.
`participant_id` | The id of the participant to update.

### Request body

See example request.


## Remove a rundl participant

To revoke a participant's access to a rundl remove the participant.

```shell
curl -X DELETE https://stage-go.rundl.com/api/rundls/123456/participants/25577359?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data ''
```