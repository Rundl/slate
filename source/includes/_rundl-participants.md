# Rundl participants

## Overview

Participants are the stakeholders in the business transaction that's getting delivered as a rundl. 

Contexts (people, groups, or members and teams within groups) can participate in rundls. The rundl participants API supports adding existing contexts or inviting new users as participants in a rundl. 

For existing contexts, clients can implement the search API to find existing users/groups. For groups, clients can optionally query the Contexts API to find available contexts for that group. 

Note: Upon adding an existing context, a context's settings determines if a full participant is added, or if a pending participant is added and a request is created for their approval. Where a request is created, the participant transitions from pending to full after accepting the request.

For new users, add participants by supplying person details including their email address. A pending user and participant is added and an invitation to join Rundl is sent to the address. Upon accepting the invitation (email receiver clicks link in email and signs up) the pending user and participant transitions to a full. 

Virtual participants include a contact instead of a context and are not invited to participate. Virtual participants remain passive until they are 'upgraded' later to participate.

<aside class="warning">When attempting to add a new user, if the supplied email is already a verified email for an existing user, the existing user is simply added/invited. Other person details will be ignored.</aside>

## The participant object

Attribute | Description
--------- | -----------
`id` | The id of the participant.
`categories` | List of strings to represent participant category. 
`rundl` | The id of the rundl the participant is added to.
`context` | The context that is participating. <br/>`Expandable`
`contact` | The contact associated with the participant. <br/>`Expandable`
`status` | Status of the participant. 
`created_date` | The date the participant was added.
`roles` | Array of role names asigned to the participant.
`is_host` | Flag to indicate if participant is the host.
`observer` | Flag for making participant an observer.
`message` | Message included in invitation email when adding a participant.
`reference` | The participant's own reference for the rundl they're participating in.

## Add a rundl participant

Not the lack of category. No category implies a virtual participant.

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