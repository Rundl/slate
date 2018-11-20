# Rundl participants

## Overview

Participants are the stakeholders in the business transaction that's getting delivered as a rundl. People, groups, or members and teams within groups, can participate in rundls.

The rundl participants API supports adding existing contexts or inviting new users as participants in a rundl. 

For existing contexts, clients can implement the search API to find existing users/groups. For groups, clients can optionally query the Contexts API to find available contexts for that group. Note: Upon adding an existing context, a context's settings determines if a full participant is added, or if a pending participant is added and a request is created for their approval. Where a request is created, the participant transitions from pending to full after accepting the request.

For new users, add participants by supplying person details including their email address. A pending user and participant is added and an invitation to join Rundl is sent to the address. Upon accepting the invitation (email receiver clicks link in email and signs up) the pending user and participant transitions to a full. 

<aside class="warning">When attempting to add a new user, if the supplied email is already a verified email for an existing user, the existing user is simply added/invited. Other person details will be ignored.</aside>

## The participant object

Attribute | Description
--------- | -----------
`id` | The id of the participant.
`rundl` | The id of the rundl the participant is added to.
`context` | The id of the existing context being added as a participant. <br/>`Expandable`
`status` | Status of the participant. 
`roles` | Array of role names asigned to the participant.
`is_host` | Flag to indicate if participant is the host.
`observer` | Flag for making participant an observer.
`created_date` | The date the participant was added.
`person` | An object with `first_name`, `last_name`, `mobile`, and `email` details for a new user to add as a participant.
`message` | Message included in invitation email when adding a participant.

## Add a rundl participant

```shell
curl -X PUT https://stage-go.rundl.com/api/rundls/123456/participants?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
      {
        "participant":{
          "rundl":123456,
          "message":"I would like to add you as a participant in this rundl.",
          "observer":false,
          "context":1688908,
          "person":{}
        }
      }
  '
```

> The above command returns JSON structured like this:

```json
{
  "context":1688908,
  "created_date":"\/Date(1520237422810+0000)\/",
  "id":25577359,
  "is_host":false,
  "observer":false,
  "person":null,
  "roles":["Default"],
  "rundl":123456,
  "status":2,
  "message":null
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

```shell
curl -X PUT https://stage-go.rundl.com/api/rundls/123456/participants/25577359?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
      {
        "participant":{
          "message":null,
          "context":1688908,
          "created_date":"/Date(1520237422810+0000)/",
          "id":25577359,
          "is_host":false,
          "observer":false,
          "person":null,
          "roles":["Seller"],
          "rundl":123456,
          "status":2
        }
      }
  '
```

> The above command returns JSON structured like this:

```json
{
  "context":1688908,
  "created_date":"\/Date(1520237422810+0000)\/",
  "id":25577359,
  "is_host":false,
  "observer":false,
  "person":null,
  "roles":["Seller"],
  "rundl":123456,
  "status":2,
  "message":null
}
```

Updates the participant's roles or toggles being an observer.

### HTTP Request

`PUT	/rundls/{rundl_id}/participants/{participant_id}?account={account_id}`

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