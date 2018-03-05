# Basics

## REST API

The Rundl API has a RESTful interface, providing programmatic access to most of the data available in the system. It provides URLs for access resources and leverages built-in HTTP features to take requests and return responses.

## Event subscriptions

Rundl is currently developing an event callback solution that enables your application to be called when an event of interest occurs.

The solution is based on the concept of configuring targets for events. An [AWS SNS topic](https://aws.amazon.com/sns/) is the first target we're implementing. 

We envisage a range of different targets in future, including a HTTP end-point. 

## Environments

The API can be accessed from our test and live environments:

`https://test-go.rundl.com/api`

`https://go.rundl.com/api`

Initially we'll give you access to our test environment to start your integration with Rundl. Upon request, we'll supply an integration key/secret for your application in our test environment. 

<aside class="notice">
As you'll first need access to our test environment, this documentation uses https://test-go.rundl.com in the examples. After you've developed you app or integration, we'll need to review and approve it before distributing a client secret that will allow you to make requests in our live environment, https://go.rundl.com.
</aside>

## Authorisation and security

You can authorise against the API using an OAuth2 access token. OAuth2 tokens are acquired programmatically and issuing tokens requires authentication. [Read more](#authentication) about Authentication below.

Tokens are used to determine whether the appropriate authorisation has been achieved to access a requested resource. For access to user data, you're app will need to acquire a user access token. In server-to-server scenarios, your app can pass a client access token (note that not all data can be accessed with a client access token).

## Versioning

The Rundl API doesn't currently implement versioning. We plan to implement a date-based version strategy in future that will slot in seamlessly.

## Requests

Rundl is an SSL-only API. 

CORS requests are supported.

UTF-8 character encoding is supported in requests and responses.

The content type being passed to the API for all requests is application/json. Supply the following header with your requests: `Content-Type: application/json`

## Accounts

Understanding Rundl accounts is an important part of using the API. 

A user may have multiple account scopes in a single session on Rundl. For example, a user will always have their personal account, and additionally, may be part of their employer's group account. Therefore, most APIs on Rundl require passing an account parameter. This parameter reflects the user's current account, which is the account scope to use when making a request. Importantly, the user's authorisations will most likely differ depending on their account.

## Rate limits

Rundl does not currently apply rate limiting. We do log all API requests and monitor usage to ensure that we have sufficient resources to support the needs of all Rundl integrated client applications.

## Pagination

> To specify the number of items in a list, use the count query parameter:

```shell
curl https://test-go.rundl.com/api/activity?account=873925&count=12 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth S29Fb3c5bFZ3azRDVmxNdnBVY1BibjhrbFpwTG5HdTIzTVVlbUduMVRYeEhFWm8zUVpveGpQQThrSXVLa0tnOGc='
```

In the Rundl API you'll come across list endpoints, like lists of activity, rundls, services or reminders. 

By default most list endpoints return a maximum of 100 items per page. However, you can specify the number of items in the response by passing a `count` parameter in the request URL (but you can't exceed 100 items per page).

> The above request returns a paging object as part of the response (example json truncated for brevity):

```json
{
  "data":[
    {...}
  ],
  "object":"list",
  "paging":{
    "next":"https:\/\/test-go.rundl.com\/api\/activity?account=5099809&cursor=9a0c1d45d46",
    "previous":"https:\/\/test-go.rundl.com\/api\/activity?account=5099809&cursor=f5d8f4e51aa",
    "strategy":2
  }
}
```

When the response has more items than the initial page, you can paginate through the results. 

Some list endpoints have outdated limiting parameters, and we'll document these where relevant. However the Rundl API now offers pagination via the concept of cursors. These are special tokens that function as markers in a series. If a list endpoint supports cursor-based pagination you'll receive and extra object as part of the response, called `paging`.

To move forwards or backwards through the pages send the `next` or `previous` cursor to the endpoint.

Some lists can be ordered by specifying a `sort` parameter to the end point. Check the documentation for a specific endpoint to tell if it can be ordered. Some endpoints have outdated sorting parameters, and we'll document these where relevant.

Parameter | Description
--------- | -----------
`count` | **Optional.** The number of items to return per page. Defaults number of items is 10. -1 means all. Maximum number of items is usually 100. 
`cursor` | Pointer to the next or previous page in the result set.
`sort` | Comma separated array of strings e.g. sort=-updated_date. Names need to be top-level atomic resource attributes. Plus (+) means ascending and is the default. Minus (-) means descending. Sorting is best effort and if something is not found it's ignored. *Note: Requires URL encoding.*

## Expanding objects

> To retrieve related objects as part of a single request, use the expand query parameter:

```shell
curl https://test-go.rundl.com/api/messages/23420341?account=286894&context_mode=insensitive&expand=comments%2Clinks%2Cfiles%2Creminders \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth S29Fb3c5bFZ3azRDVmxNdnBVY1BibjhrbFpwTG5HdTIzTVVlbUduMVRYeEhFWm8zUVpveGpQQThrSXVLa0tnOGc='
```

> The above request returns an expanded message object as part of the response (example json truncated for brevity):

```json
{
  "id":23420341,
  "object":"message",
  "comments":{
    "data":[
      {
        "id":23420441,
        "object":"comment",
        "message_id":23420341,
        "metadata":{ ... },
        "text":"Hi, This is a comment. Thanks"
      }
    ],
    "object":"list",
    "paging":{ ... }
  },
  "distribution_mode":1,
  "files":{
    "data":[
       { ... }
    ],
    "object":"list",
    "paging":{ ... }
  },
  "links":{
    "data":[ ... ],
    "object":"list",
    "paging":{ ... }
  },
  "metadata":{ ... },
  "pin":null,
  "primary_related_entity":{ ... },
  "reminders":{
    "data":[
      { ... }
    ],
    "object":"list",
    "paging":{ ... }
  },
  "text":"Hi, This is a message. Thanks"
}
```


Expanding objects allows you to retrieve related objects as part of a single request. 

For example, a message object has related comments, reminders, files and links. These related objects can be retrieved independently via separate requests. However, you can get a message's comments, reminders, files and links along with the message by passing a comma-separated string of related objects in the expand parameter: `expand=comments%2Clinks%2Cfiles%2Creminders`

## Data types

### Time stamps

Time stamps are presented in UTC time.
TODO: Current format. In future, all timestamps will be formatted as ISO 8601 strings. For example: `2016-04-23T18:25:43.511Z`

## Errors
The API policy is to return a relevant HTTP Error code with an error description.