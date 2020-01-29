## Application authentication flow with OAuth

A client application can directly request an access token from the Authorization server by using the client's id and secret to authenticate. The token will represent the client application (instead of a user). Our implementation is modelled on the [Client Credentials Grant](https://tools.ietf.org/html/rfc6749#section-4.4) flow in the OAuth 2 spec.

This flow is useful in the following cases:

- Machine-to-machine calls without the intervention of a user
- Non-user-centric API calls

### Step 1: Get client access token

```shell
curl -X POST https://stage-go.rundl.com/api/oauth/client_tokens \
  --header 'Content-Type: application/json' \
  --data '
    {
      "grant_type":"client_credentials",
      "scope":"private",
      "client_id":1234,
      "client_secret":"<Client-Secret>"
    }
  '
```
 
> The above command returns JSON structured like this:

```json
{
    "access_token": "yUwDyJ7x3gn8s...ZSihtsApgy6bnZI",
    "expires": "2018-09-07T05:39:51.4699703Z",
    "refresh_token": null,
    "scope": "private"
}
```

Request a client access token using the client secret provided to you by Rundl when registering your application.

`POST	/oauth/client_tokens`

**Body parameters**

Parameter | Default | Description
--------- | ------- | -----------
`grant_type` | **Required.** Available grants: `client_credentials`
`scope` |  Options: `private` (default) Authorised access to secure private resources.
`client_id` | **Required.** The application's client id, provided during application registration.
`client_secret` | **Required.** The application's client secret, provided during application registration.
`refresh_token` | *Not implemented.* 

**Error codes**

Code | Description
--------- | -----------
480 | Unauthorized - Invalid Client Key/Secret
400 | Bad Request - Missing or unsupported parameters

> To get a client access token, passing the credentials in the header:

```shell
curl -X POST https://stage-go.rundl.com/api/oauth/client_tokens \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Basic <Base64-Encoded-ClientId-Colon-ClientSecret>' \
  --data '
    {
      "grant_type":"client_credentials",
      "scope":"private"
    }
  '
```

<aside class="notice">
Instead of passing the client_id and client_secret values as query parameters in the post body, you may also pass them as a base64 URL encoded string in the Authorization header. To do this, base64 encode the url encoded client_id and client_secret together, with a colon separating them: aBase64EncodeFunction(clientidvalue:clientsecret). In this case the client_id and client_secret are not required as body parameters.
</aside>

### Step 2: Use the client access token

Your application is now authorised to make calls to the Rundl API, to access protected resources on behalf of your application itself, by passing the access token in the Authorization header.

`Authorization: OAuth <base64 encoded client access token>`

The available REST API resources are documented in this API reference. We also plan to document a number of walkthroughs that explain how to peform common tasks using the API.