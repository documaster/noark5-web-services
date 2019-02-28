Web services
-------

## Basic concepts

### Supported protocols

The web services support the following protocols:

- HTTPS

### Authentication and authorization

Authentication and authorization are system-specific. The web services require a *Bearer token* to be specified in the HTTP headers when sending requests, but how the token is obtained and used by the underlying system is beyond this specification.

Documaster uses an OpenID Connect Identity Provider which issues Bearer tokens.

## HTTP status codes

The following table lists the HTTP status codes used by the web services.

| Status code                | Definition                                                                                               | Example                                                         |
|:---------------------------|:---------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------|
| 200 OK                     | The request was successful.                                                                              | HTTP/1.1 200 OK                                                 |
| 201 Created                | The request was fulfilled and resulted in a new resource being created.                                  | HTTP/1.1 201 Created                                            |
| 204 No Content             | The request was successful, but there is no content to send in the response payload body.                | HTTP/1.1 204 No Content                                         |
| 400 Bad Request            | The syntax of the request was invalid.                                                                   | HTTP/1.1 400 Bad Request                                        |
| 415 Unsupported Media Type | The payload format requested by the client is not supported by the server.                               | HTTP/1.1 415 Unsupported Media Type                             |
| 401 Unauthorized           | The request was denied due to an invalid or missing access token.                                        | <p>HTTP/1.1 401 Unauthorized</p><p>WWW-Authenticate: Bearer</p> |
| 403 Forbidden              | The request was denied due to the access token having insufficient privileges.                           | HTTP/1.1 403 Forbidden                                          |
| 404 Not Found              | The requested resource does not exist or is not accessible.                                              | HTTP/1.1 404 Not Found                                          |
| 409 Conflict               | Data was updated by another client after the last read by this client. The client may retry the request. | HTTP/1.1 409 Conflict                                           |
| 500 Internal Server Error  | An internal server error has occurred.                                                                   | HTTP/1.1 500 Internal Server Error                              |
| 503 Service Unavailable    | The service is temporarily unavailable.                                                                  | HTTP/1.1 503 Service Unavailable                                |

## Paths

The following is the standard path for Noark 5 web services:

**{system-specific path}/noark5/v{version number}/{service-name}**

Version 1 of the Noark 5 web services in Documaster are available under:

**/rms/api/public/noark5/v1/{service-name}**

The following naming convention is followed in order to group related services together:

- **/rms/api/public/noark5/v1**
  - **code-lists/{service-name}**
    - Code list management web services
  - **bsm-registry/{service-name}**
    - Business-specific metadata management web services

Note that all services must remain backwards-compatible even when their version is incremented.

## Dates encoding

- Dates must be encoded as **ISO 8601** strings prior to sending them to the web services.
- Dates are returned by the web services as **ISO 8601** formatted strings.

# Code list management web services

Address:
```
https://{server}:{port}/rms/api/public/noark5/v1/code-lists
```

Special note: All code list values have the same base fields: *code*, *name*, and *description*. Certain code list values, however, also support additional fields. They are described below.

The *skjerming* values have the following extra field:
- **authority**
  - authority of the code list value

## **code-lists**

Gets all code lists supported by the system, the code lists for a given object type, or the code list for a specific field in a given type.

**Request**

``` text
GET /rms/api/public/noark5/v1/code-lists?type=OBJECT_TYPE&field=FIELD_NAME HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

###### Details

- **type** (optional)
  - type of object to fetch the code lists for
- **field** (optional)
  - field in the specified type to fetch the code list for

If **type** and **field** are both not specified, the response will contain all code lists supported by the system.

**Response**

``` text
Content-Type: application/json

{
  "results": [
    {
      "type": string,
      "field": string,
      "values": [
        {
          "code": string,
          "name": string,
          "description": string,
          ...
        },
        ...
      ]
    },
    ...
  ]
}
```

###### Details

- **results**
  - array of one or more code lists
  - **type**
    - object type that this code list applies to
  - **field**
    - field in the object type that this code list applies to
    - **code**
      - unique identifier of the code list value in the particular code list
    - **name**
      - user-friendly name of the code list value
    - **description**
      - description of the code list value

## **code-lists/{listId}/{code}**

### create and update

Creates or updates a code list value.

**Request**

``` text
PUT /rms/api/public/noark5/v1/code-lists/{listId}/{code} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "name": string,
  "description": string,
  ...
}
```

###### Details

- **listId**
  - ID of the code list in which to create or update the code list value
  - corresponds to a code list as described in - [03 - Code lists](03%20-%20Code%20lists.md)
- **code**
  - unique identifier of the code list value in the particular code list
- **name**
  - user-friendly name of the code list value
- **description**
  - description of the code list value

**Response**

``` text
Content-type: application/json

{
  "code": string,
  "name": string,
  "description": string,
  ...
}
```

###### Details

- **code**
  - unique identifier of the code list value in the particular code list
- **name**
  - user-friendly name of the code list value
- **description**
  - description of the code list value

### delete

Deletes a code list value.

**Request**

``` text
DELETE /rms/api/public/noark5/v1/code-lists/{listId}/{code} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

###### Details

- **listId**
  - ID of the code list from which to delete the code list value
  - corresponds to a code list as described in - [03 - Code lists](03%20-%20Code%20lists.md)
- **code**
  - unique identifier of the code list value in the particular code list

**Response**

No content will be returned upon successful completion.

# Business-specific metadata management web services

Address:
```
https://{server}:{port}/rms/api/public/noark5/v1/bsm-registry
```

## **bsm-registry**

Gets the definitions of all business-specific metadata fields available in the system, the definitions of a group of fields, or the definition of a single field.

**Request**

``` text
GET /rms/api/public/noark5/v1/bsm-registry?groupId=GROUP_ID&fieldId=FIELD_ID HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

###### Details

- **groupId** (optional)
  - the group ID of the field definitions to be fetched
- **fieldId** (optional)
  - the field ID of the field definition to be fetched

If **groupId** and **fieldId** are both not specified, the response will contain all business-specific metadata field definitions.

Both **groupId** and **fieldId** are strings comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit.

**Response**

``` text
Content-Type: application/json

{
  "results" : [
    {
      "groupId": string,
      "groupName": string,
      "groupDescription": string,
      "fields": [
        {
          "fieldId": string,
          "fieldName": string,
          "fieldDescription": string,
          "fieldType": string|long|double,
          "fieldValues": [string|long|double]
        },
        ...
      ]
    },
    ...
  ]
}
```

###### Details

- **results**
  - array of zero or more groups of business-specific metadata field definitions
  - **groupId**
    - system unique group ID (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
  - **groupName**
    - group name
  - **groupDescription**
    - group description
  - **fields**
    - array of zero or more business-specific metadata field definitions in the particular group
    - **fieldId**
      - system unique field ID (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
    - **fieldName**
      - field name
    - **fieldDescription**
      - field description
    - **fieldType**
      - field type (possible values are string, long, and double)
    - **fieldValues** (optional)
      - if present, an array of zero or more values
      - will be returned only for fields that support pre-defined values (i.e. values assigned to the field upon its creation)

## **bsm-registry/group/{groupId}**

### create and update

Creates or updates a business-specific metadata group definition.

**Request**

``` text
PUT /rms/api/public/noark5/v1/bsm-registry/group/{groupId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "groupName": string,
  "groupDescription": string
}
```

###### Details

- **groupId**
  - group ID of the group definition to be created or updated (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
- **groupName**
  - group name
- **groupDescription**
  - group description

**Response**

``` text
Content-type: application/json

{
  "groupId": string,
  "groupName": string,
  "groupDescription": string
}
```

###### Details

- **groupId**
  - system unique group ID of the created or updated group definition (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
- **groupName**
  - group name
- **groupDescription**
  - group description

### delete

Deletes a business-specific metadata group definition.

**Request**

``` text
DELETE /rms/api/public/noark5/v1/bsm-registry/group/{groupId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

###### Details

- **groupId**
  - group ID of the group definition to be deleted (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)

**Response**

No content will be returned upon successful completion.

## **bsm-registry/group/{groupId}/field/{fieldId}**

### create and update

Creates or updates a business-specific metadata field definition.

**Request**

``` text
PUT /rms/api/public/noark5/v1/bsm-registry/group/{groupId}/field/{fieldId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "fieldName": string,
  "fieldDescription": string,
  "fieldType": string|long|double,
  "fieldValues": [string|long|double]
}
```

###### Details

- **groupId**
  - group ID of the group definition in which to create or update the field definition
- **fieldId**
  - field ID of the field definition to be created or updated
- **fieldName**
  - field name
- **fieldDescription**
  - field description
- **fieldType**
  - field type (possible values are string, long, and double)
- **fieldValues** (optional)
  - if present, an array of zero or more values
  - must be supplied only for fields that support pre-defined values (i.e. values assigned to the field upon its creation)

Both **groupId** and **fieldId** are strings comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit.

**Response**

``` text
Content-type: application/json

{
  "fieldId": string,
  "fieldName": string,
  "fieldDescription": string,
  "fieldType": string|long|double,
  "fieldValues": [string|long|double]
}
```

###### Details

- **fieldId**
  - system unique field ID of the created or updated field definition (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
- **fieldName**
  - field name
- **fieldDescription**
  - field description
- **fieldType**
  - field type (possible values are string, long, and double)
- **fieldValues** (optional)
  - if present, an array of zero or more values
  - will be returned only for fields that support pre-defined values (i.e. values assigned to the field upon its creation)

### delete

Deletes a business-specific metadata field definition.

**Request**

``` text
DELETE /rms/api/public/noark5/v1/bsm-registry/group/{groupId}/field/{fieldId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

###### Details

- **groupId**
  - group ID of the group definition from which to delete the field definition
- **fieldId**
  - field ID of the field definition to delete

Both **groupId** and **fieldId** are strings comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit.

**Response**

No content will be returned upon successful completion.

# Other services

Address:
```
https://{server}:{port}/rms/api/public/noark5/v1
```

## **query**

Queries for objects of a given type in the Noark 5 system.

**Request**

``` text
POST /rms/api/public/noark5/v1/query HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "type": string,
  "offset: number,
  "limit": number,
  "query": string,
  "parameters":
    {
      string: string|number|date|boolean|null,
      ...
    },
  "sortOrder":
    [
      {
        "field": string,
        "order": string,
      },
      ...
    ],
   "publicUse": boolean
}
```

###### Details

- **type**
  - type of object to search for
- **offset** (optional)
  - offset of first result
  - defaults to 0
- **limit**
  - maximum number of results to retrieve
- **query** (optional)
  - an expression written in the web services query language defined in this specification
  - defaults to an empty string, i.e. search for all objects of the specified type
- **parameters** (optional)
  - parameter name used in the query : parameter value
- **sortOrder** (optional)
  - defines a sequence of sort order pairs
    - by default objects are sorted by ID in descending order
  - **field**
    - field to sort on
  - **order**
    - sort order (*desc* or *asc*)
- **publicUse** (optional)
  - omits screened fields from the response
  - intended for public use when fields containing personal or sensitive information should not be returned
  - takes effect only if skjerming has been specified on the object or any of its parent objects
  - defaults to true

**Response**

``` text
Content-Type: application/json

{
  "hasMore": boolean,
  "results": [
    {
      "type": string,
      "id": string,
      "version": number,
      "fields": {
        string: string|date|number|boolean,
        ...,
        "virksomhetsspesifikkeMetadata": {
          "groupId": {
            "fieldId": {"type": "string|long|double", "values": [string|long|double] },
            ...
          },
          ...
        }
      },
      "links": {
        string: number,
        ...
      }
    },
    ...
  ]
}
```

###### Details

- **hasMore**
  - indicates if there are more results matching the query than those in the current page
- **results**
  - objects in the current results page
  - **type**
    - object type
  - **id**
    - object ID
  - **version**
    - version of the object
  - **fields**
    - all regular fields (string, date, number, boolean) of the object
    - **virksomhetsspesifikkeMetadata** (optional)
      - business-specific metadata defined for the object
      - will be returned (if present) only for objects of type *Mappe*, *Moetemappe*, *Saksmappe*, *Registrering*, *Moeteregistrering*, and *Journalpost*
      - **groupId**
        - group ID placeholder (e.g. sf3298fyf-8f9oqhf3)
      - **fieldId**
        - field ID placeholder (e.g. sf3298fyf-8f9oqhf3)
        - **type**
          - field type (possible values are *string*, *long*, and *double*)
        - **values**
          - array of one or more field values of the particular type
- **links**
  - all any-to-one reference fields of the object

## **upload**

Uploads a file to the system. The system returns a unique ID for the uploaded file.

**Request**

``` text
POST /rms/api/public/noark5/v1/upload HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Disposition: attachment; filename="ASCII_FILENAME"; filename*=utf-8''UTF8_FILENAME
Content-Type: application/octet-stream

[Binary stream]
```

###### Details

- One of **filename** and **filename\*** must be specified
  - **filename** is a fallback if **filename\*** is not specified

See [RFC 6266](http://tools.ietf.org/html/rfc6266) (description of the Content-Disposition header) and [RFC 5987](http://tools.ietf.org/html/rfc5987#section-3.2) (description of the encoding used in the filename\* parameter).

The Content-Disposition header must specify at least one of the two parameters filename and filename\*. When both are specified filename\* takes precedence. The original charset of the filename\* parameter value must be UTF-8, while the filename parameter value must be ASCII.

**Response**

``` text
Content-Type: application/json

{
  "id": string
}
```

###### Details

- **id**
  - the unique ID for the uploaded file

## **transaction**

Saves (creates or updates), links, unlinks, and deletes objects in a single transaction.

**Request**

``` text
POST /rms/api/public/noark5/v1/transaction HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "actions": [
    {
      "action": string,
      ...
    },
    ...
  ]
}
```

###### Details

- **actions**
  - array of actions to execute (in the order in which they are specified)
- **action**
  - the action to execute: **save, link, unlink, delete**

**Response**

``` text
Content-Type: application/json

{
  "saved": {
    string: {
      ...
    },
    ...
  }
}
```

###### Details

- **saved**
  - created or updated objects
    - object ID (from the request) : created or updated object
  - if no objects have been saved, an empty JSON object will be returned

### **save**

**Request**

``` text
POST /rms/api/public/noark5/v1/transaction HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "actions": [
    {
      "action": "save",
      "type": string,
      "id": string,
      "version": string,
      "fields": {
        string: string|number|date|boolean,
        ...,
        "virksomhetsspesifikkeMetadata": {
          "groupId": {
            "fieldId": {"values": [string|long|double] },
            ...
          },
          ...
        }
      },
    },
    ...
  ]
}
```

###### Details

- **action**
  - the **save** action
- **type**
  - type of the object to create or update
- **id**
  - the ID of the object you want to create or update
  - if you are creating a new object, this must be a temporary ID (e.g. a UUID) that will be mapped to the ID of the created object and returned in the response
- **version**
  - object version when updating an existing object (may be used for optimistic locking internally)
- **fields** (optional)
  - regular object fields (string, date, number, boolean) to write
  - **virksomhetsspesifikkeMetadata** (optional)
    - business-specific metadata to write for this object
    - may be specified only for objects of type *Mappe*, *Moetemappe*, *Saksmappe*, *Registrering*, *Moeteregistrering*, and *Journalpost*
    - **groupId**
      - group ID placeholder which identifies the group that a business-specific metadata field belongs to
      - **fieldId**
        - field ID placeholder which identifies a business-specific metadata field
        - **values**
          - array of zero or more values to write to the specified business-specific metadata field

**WARNING:** The **virksomhetsspesifikkeMetadata** field describes the desired state of the business-specific metadata for the object. This implies that:
- groups, fields, and values included in **virksomhetsspesifikkeMetadata** will be added to the object;
- existing groups, fields, and values not included in **virksomhetsspesifikkeMetadata** or included, but as empty arrays/objects, will be  removed from the object.

**Response**

``` text
{
  "saved": {
    string: {
      "type": string,
      "id": string,
      "version": number,
      "fields": {
        string: string|number|date|boolean,
        ...
      },
    string: { ... },
    ...
    }
  }
}
```

### **link**

**Request**

``` text
POST /rms/api/public/noark5/v1/transaction HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "actions": [
    {
      "action": "link",
      "type": string,
      "id": string,
      "ref": string,
      "linkToId": [string]
    },
    ...
  ]
}
```

###### Details

- **action**
  - the **link** action
- **type**
  - type of the object to link to other object(s)
- **id**
  - temporary or real ID of the object to link to other object(s)
- **ref**
  - link to one or more objects using the name of a reference field in this object
- **linkToId**
  - temporary or real ID(s) of object(s) to link to

### unlink

**Request**

``` text
POST /rms/api/public/noark5/v1/transaction HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "actions": [
    {
      "action": "unlink",
      "type": string,
      "id": string,
      "ref": string,
      "unlinkFromId": [string]
    },
    ...
  ]
}
```

###### Details

- **action**
  - the **unlink** action
- **type**
  - type of the object to unlink from other object(s)
- **id**
  - real ID of the object to unlink from other object(s)
- **ref**
  - unlink from one or more other objects using the name of a reference field in this object
- **unlinkFromId**
  - real ID(s) of object(s) to unlink from

### delete

**Request**

``` text
POST /rms/api/public/noark5/v1/transaction HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "actions": [
    {
      "action": "delete",
      "type": string,
      "id": string,
      "version": string
    },
    ...
  ]
}
```

###### Details

- **action**
  - the **delete** action
- **type**
  - type of the object to delete
- **id**
  - real ID of the object to delete
- **version** (optional)
  - the fetched version of the object to delete (may be used internally for optimistic locking)

## **download**

Downloads the file with the specified ID. If the file is not registered in a records management structure, no access control limitations will apply and only the creator (uploader) of the file will be able to download it. Once the file has been registered, standard access control will apply.

**Request**

``` text
GET /rms/api/public/noark5/v1/download?id=DOCUMENT_ID HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

###### Details

- Downloads the file with the specified document ID

**Response**

``` text
Content-Type: */*

Binary content
```
