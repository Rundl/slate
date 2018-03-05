# Files

## Overview

Upload files to Rundl before attaching to other entities within the Rundl API. Rundl uses AWS S3 as it's file storage backend. 

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
`category` | string | File categorisation.
`client_upload_completion_success` | boolean | Success / failure
`content_type` | string | Mime content type for the file to be uploaded.
`file_id` | string | Server allocated file id for a verified successful upload.
`filename` | string | Filename with extension (pathless). Note that content-type is derived from the filename extension.
`hash` | string | MD5 file hash.
`publish` | boolean | Publish immediately on upload success.
`size` | integer | File size in bytes.
`thumb_url` | string | Thumb url, signed where private.
`upload_token` | string | Server allocated token identifying the upload.
`url` | string | Non-null where public URL


## Create an upload_session

```shell
curl -X POST https://test-go.rundl.com/api/files/upload_sessions?account=286894 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth Q2owOEdEekpmamFLdU5xMVNiYTYyNmpqak9ENGtaMjI1RFFmWjJYY2MxVzBRRWxZT3kzOUpCck9pcVFadzY2MUxRdw==' \
  --data '
    {
      "upload_session": {
        "filename": logo.png,
        "category": "2",
        "size": 15992,
        "publish":false
      }
    }
  '
```

> The above command returns JSON structured like this:

```json
{
  "aws_access_key":"ASIAJAY45BDQBSWXXTCA",
  "aws_bucket":"uploadsrundlco",
  "aws_region":"ap-southeast-2",
  "aws_resource_key":"d419b96c-b320-4088-b66a-7b7706cfdc57",
  "aws_secret_key":"584veUApzUYialehk89YwyZIQvNxURpa57z0AFHp",
  "aws_session_token":"FQoDYXdzEOj//////////wEaDDp8CIKb1dxGiL2hNyKqAl582KEZ6B15nkI0zt7u39BpqhdH7/fhdbJLdq5cOw9jSsbMnvKl9dtyaE1BBGj4NISBJwBqgV8o43lq3PqmNkNSzmVtihTH+zIg8VIdRtfOm/yY8JMB7/dTxuGxipEwpFGLre6I75RAQQeY1D097mCVIujghOGR4xbBPPJo5RVyDJ8929FMJ0oW0OWkbdMLNw2L4+HEErRm3SOK2L430QrYvCEX4aTGhbdZma+81kpa9D8xbJO01h1VOK4mJNvKddgOV8nYJiJJk2O2UjScYOTZKQNfV2mwtP3qzjdktrS58eCnybl1Nak+L1SF79Nnr5d4We75/vQdyyUjMdNdIpR4nz6F3uQuOfuykReRxe0uX9JcOXWjBg9NrNyAw+vZGSPQbCCyoEHStewo177t0QU=",
  "category":2,
  "client_upload_completion_success":null,
  "content_type":"image\/png",
  "file_id":null,
  "filename":"logo.png",
  "hash":null,
  "publish":false,
  "size":15992,
  "thumb_url":null,
  "upload_token":"d419b96c-b320-4088-b66a-7b7706cfdc57",
  "url":null
}
```

Create an upload session prior to uploading a file. The upload session will return the parameters required by AWS S3 when uploading a file.

### HTTP Request

`PUT	/files/upload_sessions`

### Query parameters

Parameter | Description
--------- | -----------
account | The id of the selected account when uploading the file


## Update an upload_session

```shell
curl -X PUT https://test-go.rundl.com/api/files/upload_sessions/d419b96c-b320-4088-b66a-7b7706cfdc57?account=286894 \
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth Q2owOEdEekpmamFLdU5xMVNiYTYyNmpqak9ENGtaMjI1RFFmWjJYY2MxVzBRRWxZT3kzOUpCck9pcVFadzY2MUxRdw==' \
  --data '
    {
      "upload_session": {
        "aws_access_key":"ASIAJAY45BDQBSWXXTCA",
        "aws_bucket":"uploadsdevrundlco",
        "aws_policy_encoded":"",
        "aws_region":"ap-southeast-2",
        "aws_resource_key":"d419b96c-b320-4088-b66a-7b7706cfdc57",
        "aws_secret_key":"584veUApzUYialehk89YwyZIQvNxURpa57z0AFHp",
        "aws_session_token":"FQoDYXdzEOj//////////wEaDDp8CIKb1dxGiL2hNyKqAl582KEZ6B15nkI0zt7u39BpqhdH7/fhdbJLdq5cOw9jSsbMnvKl9dtyaE1BBGj4NISBJwBqgV8o43lq3PqmNkNSzmVtihTH+zIg8VIdRtfOm/yY8JMB7/dTxuGxipEwpFGLre6I75RAQQeY1D097mCVIujghOGR4xbBPPJo5RVyDJ8929FMJ0oW0OWkbdMLNw2L4+HEErRm3SOK2L430QrYvCEX4aTGhbdZma+81kpa9D8xbJO01h1VOK4mJNvKddgOV8nYJiJJk2O2UjScYOTZKQNfV2mwtP3qzjdktrS58eCnybl1Nak+L1SF79Nnr5d4We75/vQdyyUjMdNdIpR4nz6F3uQuOfuykReRxe0uX9JcOXWjBg9NrNyAw+vZGSPQbCCyoEHStewo177t0QU=",
        "aws_signature_encoded":"",
        "category":2,
        "client_upload_completion_success":true,
        "content_type":"image/png",
        "file_id":null,
        "filename":"logo.png",
        "publish":false,
        "size":15992,
        "thumb_url":null,
        "upload_token":"d419b96c-b320-4088-b66a-7b7706cfdc57",
        "url":null
      }
    }
  '
```