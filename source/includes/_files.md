# Files

## Overview

Upload files to Rundl before attaching to other entities within the Rundl API. Rundl uses [AWS S3](https://docs.aws.amazon.com/s3) as its file storage backend. 

Uploading files is a three-step process:

1. Create an upload_session. An upload_session contains the required short-lived credentials to access AWS S3.
2. Upload the file to AWS S3. Uploading is best done using one of the many AWS SDKs.
3. Update the upload_session to complete the upload.


## File categories

Name | Value | Description
--------- | ------- | -----------
`unknown` | 0 | 
`logo` | 1 | eg group logo
`data` | 2 | data file (eg rundl file, wall file, service resource)
`avatar` | 3 | user avatar
`design_header` | 4 | branding / design header image
`design_content` | 5 | eg Step content image

## The upload_session object

Attribute | Type | Description
--------- | ------- | -----------
`aws_access_key` | string | AWS access key used in signing.
`aws_bucket` | string | Target AWS bucket name.
`aws_region` | string | AWS region.
`aws_resource_key` | string | Target AWS resource key.
`aws_secret_key` | string | AWS access key used in signing.
`aws_session_token` | string | AWS access key used in signing.
`content_type` | string | Mime content type for the file to be uploaded.
`file` | string | File object for a verified successful upload.
`filename` | string | Filename with extension (pathless). Note that content-type is derived from the filename extension.
`hash` | string | MD5 file hash.
`publish` | boolean | Publish immediately on upload success.
`size` | integer | File size in bytes.
`sstatus` | string | The session status.
`upload_token` | string | Server allocated token identifying the upload session.


## Create an upload_session

```shell
curl -X POST https://stage-go.rundl.com/api/files/uploadsession?account=123456&target=42980836&category=2 \  
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
    {
      "upload_session": {
        "filename": logo.png,
        "size": 15992
      }
    }
  '
```

> The above command returns JSON structured like this:

```json
{
  "aws_access_key":"ASIAJAY...WXXTCA",
  "aws_bucket":"uploadsrundlco",
  "aws_region":"ap-southeast-2",
  "aws_resource_key":"d419b96c-x111-1111-x11y-7b7706cfdc57",
  "aws_secret_key":"584veUApzU...a57z0AFHp",
  "aws_session_token":"FQoDYXdzEOj//////////wEaDDp8CIKb1dxGiL2hNyK...rNyAw+vZGSPQbCCyoEHStewo177t0QU=",
  "content_type":"image\/png",
  "file":null,
  "filename":"logo.png",
  "hash":null,
  "publish":false,
  "size":15992,
  "status": "pending",
  "upload_token":"d419b96c-x111-1111-x11y-7b7706cfdc57",
}
```

Create an upload session prior to uploading a file. The upload session will return the parameters required by AWS S3 when uploading a file.

### HTTP Request

`POST	/files/uploadsession?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when uploading the file
target | The id of the entity the file will be associated with e.g. a rundl id.
category | The category of file in Rundl. 


## Update an upload_session

```shell
curl -X PATCH https://stage-go.rundl.com/api/files/uploadsession/d419b96c-b320-4088-b66a-7b7706cfdc57?account=123456 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
    {
      "upload_session":{
        "aws_access_key":"ASIAJAY...WXXTCA",
        "aws_bucket":"uploads.stage.rundl.co",
        "aws_region":"ap-southeast-2",
        "aws_resource_key":"d419b96c-x111-1111-x11y-7b7706cfdc57",
        "aws_secret_key":"584veUApzU...a57z0AFHp",
        "aws_session_token":"FQoDYXdzEOj//////////wEaDDp8CIKb1dxGiL2hNyK...rNyAw+vZGSPQbCCyoEHStewo177t0QU=",
        "content_type":"image/jpeg",
        "file":null,
        "filename":"handshake.jpg",
        "publish":null,
        "size":233106,
        "status":"uploaded",
        "upload_token":"d419b96c-x111-1111-x11y-7b7706cfdc57"
      }
    }
  '
```

### HTTP Request

`PATCH	/files/upload_sessions/{upload_token}?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when uploading the file

### Path parameters

Parameter | Description
--------- | -----------
upload_token | The token to identify the upload session.