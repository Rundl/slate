# Files

## Overview

Upload files to Rundl before attaching to other entities within the Rundl API. Rundl uses AWS S3 as its file storage backend. 

Uploading files is a three-step process:

1. Create an upload_session. An upload_session contains the required short-lived credentials to access AWS S3.
2. Upload the file to AWS S3. Uploading is best done using one of the many AWS SDKs.
3. Update the upload_session to complete the upload.

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
`upload_token` | string | Server allocated token identifying the upload session.


## Create an upload_session

```shell
curl -X POST https://test-go.rundl.com/api/files/upload_sessions?account=123456 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <Base64-Encoded-User-Access-Token>' \
  --data '
    {
      "fileUploadSession": {
        "filename": logo.png,
        "size": 15992,
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
  "upload_token":"d419b96c-x111-1111-x11y-7b7706cfdc57",
}
```

Create an upload session prior to uploading a file. The upload session will return the parameters required by AWS S3 when uploading a file.

### HTTP Request

`POST	/files/upload_sessions?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when uploading the file


## Update an upload_session

```shell
curl -X PUT https://test-go.rundl.com/api/files/upload_sessions/d419b96c-b320-4088-b66a-7b7706cfdc57?account=123456 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <Base64-Encoded-User-Access-Token>' \
  --data '
    {
      "fileUploadSession": {
        "aws_access_key":"ASIAJAY...WXXTCA",
        "aws_bucket":"uploadsdevrundlco",
        "aws_region":"ap-southeast-2",
        "aws_resource_key":"d419b96c-x111-1111-x11y-7b7706cfdc57",
        "aws_secret_key":"584veUApzU...a57z0AFHp",
        "aws_session_token":"FQoDYXdzEOj//////////wEaDDp8CIKb1dxGiL2hNyK...rNyAw+vZGSPQbCCyoEHStewo177t0QU=",
        "content_type":"image/png",
        "file":null,
        "filename":"logo.png",
        "publish":false,
        "size":15992,
        "upload_token":"d419b96c-x111-1111-x11y-7b7706cfdc57",
      }
    }
  '
```

### HTTP Request

`PUT	/files/upload_sessions/{upload_token}?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when uploading the file

### Path parameters

Parameter | Description
--------- | -----------
upload_token | The token to identify the upload session.