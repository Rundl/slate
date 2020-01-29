## User authentication flow with OAuth  

A client application can get permission from a user to access protected resources on their behalf. The applications passes control to Rundl, we let the user log in to their chosen account, and pass back control to your application together with an assertion token. The token is linked to the user and your client id and can be exchanged for a user access token.

This is a standard flow in many web applications. By delegating authentication to Rundl your application doesn't have to handle usernames and passwords.

Our implementation is modelled on the [Authorisation Code Grant](https://tools.ietf.org/html/rfc6749#section-4.1) flow in the OAuth 2 spec. 

This is useful in the following cases:

- User grants access to the client application to access its protected data.
- Client application makes calls to the Rundl API on behalf of the authorised user.

Every time your application makes an API call it will be issued on behalf of the user. If the user doesn't have permissions to access the requested resource the call will be blocked.

### Step 1: Redirect users to the Rundl authorisation page

Send your users to the Rundl authorisation page. 

`https://stage-go.rundl.com/oauth/authorize`

Pass the following query parameters:

Parameter | Description
--------- | -----------
`response_type` | Options: `code` (default) Authorization code grant flow is the only user authorised grant type currently supported.
`client_id` | The client id you received from Rundl when registering your application.
`redirect_uri` | The URI in your application where users get sent after a user decides to log in to your application. If not supplied, user will be redirected to https://stage-go.rundl.com/dashboard.
`scope` | *Not implemented.*
`state` | *Not implemented.*

The page asks the user to log in, before they are redirected back to your application.

<aside class="notice">
Currently Rundl's OAuth implementation supports a user implicitly granting your application access to the protected resources on their behalf, via the act of logging in. In future Rundl will support explicit re-delegation of access rights to a protected resource. The user will explicitly grant access to the requested types of protected resources (scopes).
</aside>

### Step 2: Handle authorisation response

Rundl will return the user to your specified `redirect_uri` with the authorisation code as a parameter.

`https://example.com/your_redirect_uri?code=<Auth-Code>`

Handle the following query parameters:

Parameter | Description
--------- | -----------
`code` | The short lived authorisation code.
`redirect_uri` | *Not implemented.* Included for verification.
`state` | *Not implemented.* Included for verification.
`stay_signed_in` | Optional parameter passing through the intent of the user for the session length. Support for this will be dropped once refresh token support is added. Your app will be able to implement its own session length strategy.

### Step 3: Get user access token from Rundl

```shell
curl -X POST https://stage-go.rundl.com/api/oauth/access_tokens \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  --data '
    {
      "grant_type":"authorization_code",
      "client_id":286454,
      "client_secret":"<Client-Secret>",
      "scope":"private",
      "code":"<Auth-Code>"
    }
  '
```
 
> The above command returns JSON structured like this:

```json
{
    "access_token": "yUwDyJ7x3gn8s...ZSihtsApgy6bnZI",
	  "refresh_token": null,
    "expires": "2018-02-07T05:39:51.4699703Z",
    "scope": "private"
}
```

Exchange the authorisation code for a user access token.

`POST	/oauth/access_tokens`

**Body parameters**

Parameter | Description
--------- | -----------
`grant_type` | **Required.** Options: `authorization_code`
`client_id` | **Required - unless passed in the Authorization header.** The application's client id, provided during application registration.
`client_secret` | **Required - unless passed in the Authorization header.** The application's client secret, provided during application registration.
`code` |  The authorization code returned from Rundl when the user authorises your application to access Rundl on their behalf.
`scope` | Options: `private` (default) authorised access to secure private resources, `public` some resources are available for anonymous access via a public access token
`redirect_uri` | *Not implemented.*
`state` | *Not implemented.*
`refresh_token` | *Not implemented.*
`stay_logged_in` | Optional parameter passing through the intent of the user for the session length. Options: `false` (default) access token will expire after 8 hours, `true` access token will expire after 30 days<br/>*This parameter will be dropped once refresh tokens are implemented.*

**Error codes**

Code | Description
--------- | -----------
400 | Bad request. e.g. User not enabled for login. Grant type not supported.
480 | Revoked client id
498 | Token invalid

> To get a user access token, passing the credentials in the header:

```shell
curl -X POST https://stage-go.rundl.com/api/oauth/access_tokens \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Basic <Base64-Encoded-ClientId-Colon-ClientSecret>' \
  --data '
    {
      "grant_type":"authorization_code",
      "scope":"private",
      "code":"<Auth-Code>"
    }
  '
```

<aside class="notice">
Instead of passing the client_id and client_secret values as query parameters in the post body, you may also pass them as a base64 URL encoded string in the Authorization header. To do this, base64 encode the url encoded client_id and client_secret together, with a colon separating them: aBase64EncodeFunction(clientidvalue:clientsecret). In this case the client_id and client_secret are not required as body parameters.
</aside>

### Step 4: Use the access token

Your application is now authorised to make calls to the Rundl API, to access protected resources on behalf of the authenticated user, by passing the access token in the Authorization header.

`Authorization: OAuth <User-Access-Token>`

See REST API method reference for information about the the available resources.