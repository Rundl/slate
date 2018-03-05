# Authentication

Each request to the Rundl API must be authenticated. When you register your application we'll assign it OAuth credentials and send them to you.

Rundl uses OAuth to provide authorised access to resources via its API. OAuth has a number of benefits:

- Users are not required to share their passwords with third party applications.
- Standard libraries available in most application frameworks.

## Application authentication flow with OAuth

A client application can directly request an access token from the Authorization server by using the client's id and secret to authenticate. The token will represent the client application (instead of a user). Our implementation is modelled on the [Client Credentials Grant](https://tools.ietf.org/html/rfc6749#section-4.4) flow in the OAuth 2 spec.

This flow is useful in the following cases:

- Machine-to-machine calls without the intervention of a user
- Non-user-centric API calls

### Step 1: Get client access token

```shell
curl -X POST https://test-go.rundl.com/api/oauth/client_tokens \
  --header 'Content-Type: application/json' \
  --data '
    {
      "grant_type":"client_credentials",
      "scope":"private",
      "client_id":286454,
      "client_secret":"LgIxGhAktqVZm6U7JC56PV8iWCEgwshgBNKfdBZdeCtyhwtkoFslA"
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
`refresh_token` | **Not implemented.** 

**Error codes**

Code | Description
--------- | -----------
480 | Unauthorized - Invalid Client Key/Secret
400 | Bad Request - Missing or unsupported parameters

> To get a client access token, passing the credentials in the header:

```shell
curl -X POST https://test-go.rundl.com/api/oauth/client_tokens \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Basic Mjg2NDU0OkxnSXhHaEFrdHFWWm02VTdKQzU2UFY4aVdDRWd3c2hnQk5LZmRCWmRlQ3R5aHd0a29Gc2xB' \
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

`https://test-go.rundl.com/oauth/authorize`

Pass the following query parameters:

Parameter | Default | Description
--------- | ------- | -----------
`response_type` | Options: `code` (default) Authorization code grant flow is the only user authorised grant type currently supported.
`client_id` | The client id you received from Rundl when registering your application.
`redirect_uri` | The URI in your application where users get sent after a user decides to log in to your application. If not supplied, user will be redirected to https://test-go.rundl.com/dashboard.
`scope` | **Not implemented.**
`state` | **Not implemented.**

The page asks the user to log in, before they are redirected back to your application.

<aside class="notice">
Currently Rundl's OAuth implementation supports a user implicitly granting your application access to the protected resources on their behalf, via the act of logging in. In future Rundl will support explicit re-delegation of access rights to a protected resource. The user will explicitly grant access to the requested types of protected resources (scopes).
</aside>

### Step 2: Handle authorisation response

Rundl will return the user to your specified `redirect_uri` with the authorisation code as a parameter.

`https://example.com/your_redirect_uri?code=auth_code`

Handle the following query parameters:

Parameter | Description
--------- | -----------
`code` | The short lived authorisation code.
`redirect_uri` | **Not implemented.** Included for verification.
`state` | **Not implemented.** Included for verification.
`stay_signed_in` | Optional parameter passing through the intent of the user for the session length. Support for this will be dropped once refresh token support is added. Your app will be able to implement its own session length strategy.

### Step 3: Get user access token from Rundl

```shell
curl -X POST https://test-go.rundl.com/api/oauth/access_tokens \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  --data '
    {
      "grant_type":"authorization_code",
      "client_id":286454,
      "client_secret":"LgIxGhAktqVZm6U7JC56PV8iWCEgwshgBNKfdBZdeCtyhwtkoFslA",
      "scope":"private",
      "code":"p5z8ALCrUfT6XdkjBNujKHjC75ETwgqBKqCeaiM"
    }
  '
```
 
> The above command returns JSON structured like this:

```json
{
    "access_token": "yUwDyJ7x3gn8s...ZSihtsApgy6bnZI",
	  "refresh_token": null,
    "expires": "2016-09-07T05:39:51.4699703Z",
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
`redirect_uri` | **Not implemented.**
`state` | **Not implemented.**
`refresh_token` | **Not implemented.**
`stay_logged_in` | Optional parameter passing through the intent of the user for the session length. Options: `false` (default) access token will expire after 8 hours, `true` access token will expire after 30 days<br/>*This parameter will be dropped once refresh tokens are implemented.*

**Error codes**

Code | Description
--------- | -----------
400 | Bad request. e.g. User not enabled for login. Grant type not supported.
480 | Revoked client id
498 | Token invalid

> To get a user access token, passing the credentials in the header:

```shell
curl -X POST https://test-go.rundl.com/api/oauth/access_tokens \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Basic Mjg2NDU0OkxnSXhHaEFrdHFWWm02VTdKQzU2UFY4aVdDRWd3c2hnQk5LZmRCWmRlQ3R5aHd0a29Gc2xB' \
  --data '
    {
      "grant_type":"authorization_code",
      "scope":"private",
      "code":"Gu9iGjXVBMJZ7LPtfHBTgeohSeEgmk2nieP7Rdq"
    }
  '
```

<aside class="notice">
Instead of passing the client_id and client_secret values as query parameters in the post body, you may also pass them as a base64 URL encoded string in the Authorization header. To do this, base64 encode the url encoded client_id and client_secret together, with a colon separating them: aBase64EncodeFunction(clientidvalue:clientsecret). In this case the client_id and client_secret are not required as body parameters.
</aside>

### Step 4: Use the access token

Your application is now authorised to make calls to the Rundl API, to access protected resources on behalf of the authenticated user, by passing the access token in the Authorization header.

`Authorization: OAuth <base64 encoded user access token>`

See REST API method reference for information about the the available resources.


## User authentication flow with JWT

Rundl allows third party applications to authenticate users directly with Rundl using a JSON Web Token (JWT) signed with an RSA key. This authentication method is intended for server-to-server applications.

Using the JWT flow has a number of benefits:

- Removes the friction of your application's users logging in to Rundl in addition to your application.
- Reduces the number of steps in the authentication flow. Users are not directed to Rundl to authorise your application to access their data on Rundl

Use the JWT flow in the following cases:

- Automatically onboard your application's users as managed Rundl user accounts.
- Single Sign On (SSO) your application's users to a Rundl shared web page.

### About SSO

When you attempt to authenticate your application's user for the first time:

1. A new Rundl user will be created based on the information included in the JWT assertion.
2. A user access token is returned.

After the first successful authentication, in subsequent attempts you will simply be returned a user access token.

There are important conceptual differences with the Rundl user created via this flow:

- This flow essentially drops the first leg of the standard OAuth authorisation flow: the user isn't asked to authorize your application to access Rundl on their behalf.
- Your application is the designated identity provider for the an identity associated with the Rundl user.
- Due to the network and parallel process aspect to Rundl, if your application's user participates in rundls delivered by other service providers, they will get an additional identity from Rundl (or another approved identity provider) to access Rundl. Rundl will facilitate the creation of this separate identity associated with the user.
- The user access token returned is limited in scope to access host data (i.e. rundls) owned by the Rundl account associated with the registered identity provider. It can't be used to access data owned by other hosts. In future we envisage an approval mechanism to extend trust to other hosts. *NOTE: The access token may still be used to access network data not owned by hosts.*

### Step 1: Register your application as an identity provider

In addition to the standard requirements for registering your application, you will need to register to become an identity provider. Contact [support@rundl.com](mailto:support@rundl.com).

### Step 2: Generate an RSA Keypair

> Run the following commands (for example using Cygwin on Windows).

> Generate private key:

```shell
openssl genrsa -aes256 -out private_key.pem 2048
```

> Generate public key:

```shell
openssl rsa -pubout -in private_key.pem -out public_key.pem
```

> If generated properly, the RSA public key should look like this:

```shell
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCqGKukO1De7zhZj6+H0qtjTkVxwTCpvKe4eCZ0
FPqri0cb2JZfXJ/DgYSF6vUpwmJG8wVQZKjeGcjDOL5UlsuusFncCzWBQ7RKNUSesmQRMSGkVb1/
3j+skZ6UtW+5u09lHNsj6tQ51s1SPrCBkedbNf0Tp0GbMJDyR4e9T04ZZwIDAQAB
-----END PUBLIC KEY-----
```

To sign and authenticate a JWT assertion you need to generate a RSA private-public keypair. You will sign your requests with the private key, which is verified by the public key  you provide to Rundl. Be sure to protect your private key.

We suggest generating the keypair using the [OpenSSL](https://www.openssl.org/) tool.


If using other tools, note the following settings:

Setting | Value
--------- | -----------
Key length | 2048
Algorithm | RS512
Public key | Export asn.1 format

### Step 3: Submit the Public Key

The public key is not a secret so can be emailed or posted to a rundl. This key is required to be verifiably under the control of the party identified as the issuer of the JWT.

### Step 4: Construct the JWT assertion

The following table lists libraries that have been used to successfully generate a valid JWT:

Language | Link | Settings
--------- | --------- | ---------
Java	| https://github.com/auth0/java-jwt<br/>Relevant sample code:<br/>com.auth0.jwt.JWTRoundTripRsa256Test | **Key length:** 2048 (default)<br/>**Algorithm:** options.setAlgorithm(Algorithm.RS512)<br/>**Public key:** asn.1 format export.
C#	| https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/	| 
PHP	| https://github.com/emarref/jwt | 

It is likely that other libraries from https://jwt.io/ are also compatible.

**Header attributes**

Parameter | Type | Description
--------- | --------- | ---------
`alg` | String	| **Required.** The algorithm used to verify the signature. "RS512". 512 represents the SHA-2 hashing algorithm that is used by the digital signature.
`typ` | String	| **Required.**	Type of token. Default is “JWT” to specify a JSON Web Token (JWT).

**Payload claims**

Claim | Type | Description | Sample
--------- | --------- | --------- | ---------
`given_name` | String	| **Required.** The user's first name.	| "given_name": "Jerry"
`family_name` | String	|	The user's surname.	| "family_name": "Seldon"
`mobilephone` | String	|	The user's mobile phone number.	| "mobilephone": "+61477289117"
`email` | String	| **Required.**	Email address. Required for notifications.	| "email": "jseldon@gmail.com"
`exp` | Number	| **Required.**		JWT Expiration Time. UTC timestamp - seconds since UnixEpoch (UTC 1970-01-01T0:0:0Z) - after which the JWT cannot be accepted.	| "exp": 1475552704
`sub` | String	| **Required.**	Subject of the JWT. Locally unique id of the app user in the context of the issuer.	| "sub": "1234567890"
`iss` | String	| **Required.**	The id for the Rundl identity provider registered in step 1. Identifies the principal that issued the JWT.	| "iss": "1234567890"
`iat` | Number	| **Required.**	UTC timestamp - seconds since UnixEpoch (UTC 1970-01-01T0:0:0Z) - when JWT issued. This can determine the age of the JWT.	| "iat": 1475549104
`aud` | String	| **Required.**	Audience (JWT target). Identifies the intended recipients of the JWT.  Always "https://go.rundl.com/api/oauth/access_tokens" for getting OAuth access tokens.	| "aud": "https://go.rundl.com/api/oauth/access_tokens"

### Step 5: Get access token from Rundl

> To get a user access token, passing the JWT in the header:

```shell
curl -X POST https://test-go.rundl.com/api/oauth/access_tokens \
  --header 'Content-Type: application/json' \
  --header 'Authorization: JWT <jwt> \
  --data '
    {
      "grant_type":"jwt_bearer",
      "client_id":"YOUR_CLIENT_ID",
      "scope":"private",
      "stay_signed_in":"true"
    }
  '
```

Your service will authenticate to Rundl by requesting a user access token, passing the JWT in the Authorization header.

### Step 6: Use the access token

Your application is now authorised to make calls to the Rundl API, to access protected resources on behalf of the authenticated user, by passing the access token in the Authorization header.

`Authorization: OAuth <base64 encoded user access token>`

See REST API method reference for information about the the available resources.

You can also pass the access token as a URL parameter to one of our responsive shared pages.

`https://go.rundl.com/auth/session?access_token={access_token}&redirect_url={redirect_url}`