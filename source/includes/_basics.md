# Basics

## REST API

The Rundl API is a RESTful Web API, providing programmatic access to resources representing relevant concepts on our platform. It includes URLs to access resources, leveraging built-in HTTP features to handle requests and return responses.

Contact [support@rundl.com](mailto:support@rundl.com?subject=Register API client) to register for API access. Registration is currently a manual process. 

## Event Subscriptions

Rundl Event Subscriptions is a callback solution that enables your application to be called when an event of interest occurs on the platform. The solution involves subscribing to particular event types, which are sent to a target service in your own infrastructure that you manage. 

An [AWS SNS topic](https://aws.amazon.com/sns/) is our first target (we envisage a range of different targets in future, including a HTTP end-point). 

Contact [support@rundl.com](mailto:support@rundl.com?subject=Subscribe to Rundl Events) to subscribe to Rundl Events. Subscription is currently a manual process. 

Rundl will trigger sending events in 10 second intervals.

## Embedded Apps

You can embed your custom HTML web app in an iframe container that's loaded in our web app. Your app can load in a sidebar within a rundl for presentation layer integration of your app. 

An embedded app gets unique OAuth credentials upon registration, and is configured with an HTTP end-point where your embedded app resides, and your public key. Contact [support@rundl.com](mailto:support@rundl.com?subject=Register Embedded app) to register an embedded app. Registration is currently a manual process. Please provide the url for your app and your public key.

After we've registered your app, you can link to it using the following internal link format in a rundl message, comment or step description:

`[Your embedded page link](!{rundl.id},addon_embeddedpage,your_app_id)`

Our web app will automatically resolve this link in Rundl activity as:

`<a href="https://stage-go.rundl.com/rundls/123456?sb=addon_embeddedpage&sb_app=123456">Your embedded page link</a>`

> The token will include a JWT structured like this:

```json
{
  "app_data": "{\"account_id\":\"123456\",\"rundl_id\":\"123456\"}",
  "code": "NnlNIO1B3OiigddIXEkGWHvhVKBESWNFTGQHxbZzJCnSA",
  "algorithm": "http://www.w3.org/2001/04/xmldsig-more#rsa-sha512",
  "nbf": 1542698558,
  "exp": 1574234558,
  "iat": 1542698558,
  "iss": "stage-go.rundl.com",
  "aud": "https://your-example-uri.com/some-path"
}
```

When a rundl participant clicks the link, our web app will send a GET request to your HTTP end-point with a JWT sent in the `token` url parameter. 

`https://your-example-uri.com/some-path?token=<JWT>`

The JWT is signed and can be used to verify the authenticty of the request. The JWT also includes information that allows you to establish a session for a user when further interactions with the Rundl REST API are required. 

Your end-point should return valid HTML that will be loaded in the iframe.

## Environments

The API can be accessed from our staging and live environments:

`https://stage-go.rundl.com/api`

`https://go.rundl.com/api`

Initially we'll give you access to our staging environment to start your integration with Rundl. Request to register an app and we'll supply an integration key/secret for your application in our staging environment.

<aside class="notice">
As you'll first get API access in our staging environment, this documentation uses https://stage-go.rundl.com in examples. After you've developed you app or integration, we'll need to review and approve it before distributing a client secret that will allow you to make requests in our live environment, https://go.rundl.com.
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

A user may have multiple account scopes in a single session on Rundl. For example, a user will always have their personal account, and additionally, may be part of a group account (typically, a group account for their company). Therefore, most APIs on Rundl require passing an account parameter. This parameter reflects the user's current account, which is the account scope to use when making a request. Importantly, a user's authorisations will most likely differ depending on their account.

## Rate limits

Rundl does not currently apply rate limiting. We do log all API requests and monitor usage to ensure that we have sufficient resources to support the needs of all Rundl integrated client applications.

## Pagination

> To specify the number of items in a list, use the count query parameter:

```shell
curl https://stage-go.rundl.com/api/activity?account=123456&count=12 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>'
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
    "next":"https:\/\/stage-go.rundl.com\/api\/activity?account=123456&cursor=9a0c1d45d46",
    "previous":"https:\/\/stage-go.rundl.com\/api\/activity?account=123456&cursor=f5d8f4e51aa",
    "strategy":2
  }
}
```

When the response has more items than the initial page, you can paginate through the results. 

Some list endpoints have outdated limiting parameters, and we'll document these where relevant. However the Rundl API now offers pagination via the concept of cursors. These are special tokens that function as markers in a series. If a list endpoint supports cursor-based pagination you'll receive an extra `paging` object as part of the response.

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
curl https://stage-go.rundl.com/api/messages/23420341?account=123456&context_mode=insensitive&expand=comments%2Clinks%2Cfiles%2Creminders \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>'
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

In future, all timestamps will be formatted as ISO 8601 strings. For example: `2016-04-23T18:25:43.511Z`


(TODO) Currently API reponses will serialise dates differently. 

Format | Description
--------- | ---------
wcf DateTime JSON | Date object, represented in JSON as "\/Date(number of ticks)\/".<br/>See https://msdn.microsoft.com/en-us/library/system.web.script.serialization.javascriptserializer.aspx.


## Errors
The API policy is to return a relevant HTTP Error code with an error description.