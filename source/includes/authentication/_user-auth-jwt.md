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
openssl genrsa -out private_key.pem 2048
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
curl -X POST https://stage-go.rundl.com/api/oauth/access_tokens \
  --header 'Content-Type: application/json' \
  --header 'Authorization: JWT <jwt> \
  --data '
    {
      "grant_type":"jwt_bearer",
      "client_id":"1234",
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

`https://go.rundl.com/auth/session?access_token={<User-Access-Token>}&redirect_url={redirect_url}`