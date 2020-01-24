# Real estate

## Overview

APIs to support real estate transactions, in particular by supporting property data.

Creating a new property involves a the following workflow using real estate endpoints:

1. Use the `real_estate/au/addresses/autocomplete` endpoint to retrieve a list of address suggestions.
2. Use the `real_estate/au/addresses/{uuid}` endpoint to retrieve the chosen address. An address record is returned.
3. Use the `real_estate/au/properties` endpoint with address record id to check for an existing rundlproperty.
4. Use the `real_estate/au/properties` endpoint to create a new `realproperty`.

## The realproperty object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Unique identifier for the real property.
`address` | object | The name of the rundl.
`administration_areas` | object | The description of the rundl.

## The address autocomplete object

Attribute | Type | Description
--------- | ------- | -----------
`full_address` | string | Full address as one line.
`uuid` | string | The unique identifier returned by the address lookup service. Rundl uses AddressFinder. If you have your own AddressFinder account you can do the address lookup direct with AddressFinder.

## The address object

Attribute | Type | Description
--------- | ------- | -----------
`id` | integer | Rundl's unique identifier for the address.
`object` | string | The Rundl object type.
`version` | integer | The object version.
`version_latest` | string | Latest version number.
`admin_level_1` | string | First level administration area. In Australia, state e.g. VIC.
`building_name` | string | Buidling name
`country` | string | Country as code e.g. AUS
`data_type` | string | The address lookup provider e.g. address_finder
`dpid` | string | AusPost's unique identifier for the address.
`full_address` | string | 28 St Edmonds Road, PRAHRAN VIC 3181",
`gnaf_id` | string | PSMA's unique identifier for the address.
`latitude` | string | Latitude coordinate
`level` | string | Building level,
`level_type` | string | Type of building level,
`locality_level_1` | string | First level locality. In Australia, town/subers e.g. PRAHRAN.
`longitude` | string | Longitude coordinate.
`lot` | string | Lot number.
`non_standard_line_1` | string | Address line 1 where address entered as lines.
`non_standard_line_2` | string | Address line 2 where address entered as lines.
`postcode` | string | Postcode
`street_name` | string | Street name
`street_number` | string | Street number
`street_suffix` | string | Street suffix e.g. 12 Smith Street 'West'
`street_type` | string | Type of street.
`unit` | string | Building unit number.
`unit_type` | string | Building unit type.
`uuid` | string | The address lookup provider's unique identifier. Rundnl uses AddressFinder. Note the uuid from AddressFinder changes each time they update their dataset.


## Autocomplete address

```shell
curl -X GET https://stage-go.rundl.com/api/real_estate/au/addresses/autocomplete?account=123456&address=28%20St%20Edmonds%20Road&index=0&count=25 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
```

> The above command returns JSON structured like this:

```json
[
  {
    "full_address":"28 St Edmonds Road, PRAHRAN VIC 3181",
    "uuid":"7b21e40e-378e-11ea-9609-68c0611977d0"
  },
  {
    "full_address":"28 St Edmonds Road, ARMADALE VIC 3181",
    "uuid":"c4433c3a-3795-11ea-8b73-68c0629ca78b"
  },
  ...
]
```

Lookup an address with an address part as search string.

### HTTP Request

`GET	/real_estate/au/properties?account={account_id}&address={text}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl
address | Text to search addresses.


## Get address

```shell
curl -X GET https://stage-go.rundl.com/api/real_estate/au/addresses/7b21e40e-378e-11ea-9609-68c0611977d0?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
```

> The above command returns JSON structured like this:

```json
{
  "id":123456,
  "object":"address",
  "version":1,
  "version_latest":null,
  "admin_level_1":"VIC",
  "building_name":null,
  "country":"AUS",
  "data_type":"address_finder",
  "dpid":null,
  "full_address":"28 St Edmonds Road, PRAHRAN VIC 3181",
  "gnaf_id":"GAVIC412129732",
  "latitude":-37.85035600,
  "level":null,
  "level_type":null,
  "locality_level_1":"PRAHRAN",
  "longitude":144.99193600,
  "lot":null,
  "non_standard_line_1":null,
  "non_standard_line_2":null,
  "postcode":"3181",
  "street_name":"St Edmonds",
  "street_number":"28",
  "street_suffix":null,
  "street_type":"Road",
  "unit":null,
  "unit_type":null,
  "uuid":"7b21e40e-378e-11ea-9609-68c0611977d0"
}
```

Get an address with address autocomplete uuid. The endpoint to be used together with address autocomplete endpoint.

### HTTP Request

`GET	/real_estate/au/addresses/{uuid}?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl

### Path parameters

Parameter | Description
--------- | -----------
uuid | The address autocomplete service's unique id.


## Get properties

```shell
curl -X GET https://stage-go.rundl.com/api/real_estate/au/properties?account=123456&address_id=42704758 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
```

> The above command returns JSON structured like this:

```json
{
  "data":[ ... ],
  "object":"list",
  "paging":{
    "next":null,
    "previous":null,
    "strategy":0
  }
}
```

Get a collection of properties passing the address id as a filter. Use this to check for the existence of a property for the given address.

### HTTP Request

`GET	/real_estate/au/properties?account={account_id}&address_id={address_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl
address_id | The id of an address to filter the collection


## Create a realproperty

```shell
curl -X POST https://stage-go.rundl.com/api/real_estate/au/properties?account=123456 \ 
  --header 'Content-Type: application/json' \
  --header 'Authorization: OAuth <User-Access-Token>' \
  --data '
    {
      "real_property":{
        "actions":[
          {
            "name":"add",
            "id":123456,
            "object":"address"
          }
        ]
      }
    }
  '
```

> The above command returns JSON structured like this:

```json
{
  "id":123456,
  "object":"realproperty",
  "version":1,
  "version_latest":null,
  "address":{
    "id":123456,
    "object":"address",
    "version":1,
    "version_latest":null,
    "admin_level_1":"VIC",
    "building_name":null,
    "country":"AUS",
    "data_type":"address_finder",
    "dpid":"44418691",
    "full_address":"28 St Edmonds Road, PRAHRAN VIC 3181",
    "gnaf_id":"GAVIC412129732",
    "latitude":null,
    "level":null,
    "level_type":null,
    "locality_level_1":"PRAHRAN",
    "longitude":null,
    "lot":null,
    "non_standard_line_1":null,
    "non_standard_line_2":null,
    "postcode":"3181",
    "street_name":"St Edmonds",
    "street_number":"28",
    "street_suffix":null,
    "street_type":"Road",
    "unit":null,
    "unit_type":null,
    "uuid":"7b21e40e-378e-11ea-9609-68c0611977d0"
  },
  "administration_areas":null
}

```

Create a new property with an address id. An address is the natural key for the property. Create/retrieve the address using address-related endpoints.

### HTTP Request

`POST	/real_estate/au/properties?account={account_id}`

### Query parameters

Parameter | Description
--------- | -----------
account_id | The id of the selected account when starting the rundl

### Request body

See example request.