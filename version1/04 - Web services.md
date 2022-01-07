Web services
-------

# Basic concepts

### Supported protocols

The web services support the following protocols:

- HTTPS

### Authentication and authorization

The web services require an access token (Bearer) to be specified in the HTTP headers when sending requests. A token can be obtained from the OpenID Connect Identity Provider used by the Documaster instance.

### HTTP status codes

The following table lists the HTTP status codes used by the web services.

| Status code                | Definition                                                                                                                                                                                                        | Example                                                         |
|:---------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------|
| 200 OK                     | The request was successful.                                                                                                                                                                                       | HTTP/1.1 200 OK                                                 |
| 201 Created                | The request was fulfilled and resulted in a new resource being created.                                                                                                                                           | HTTP/1.1 201 Created                                            |
| 202 Accepted               | The request has been accepted for processing, but the processing has not been completed. The request might or might not eventually be acted upon, as it might be disallowed when processing actually takes place. | HTTP/1.1 202 Accepted                                           |
| 204 No Content             | The request was successful, but there is no content to send in the response payload body.                                                                                                                         | HTTP/1.1 204 No Content                                         |
| 400 Bad Request            | The syntax of the request was invalid.                                                                                                                                                                            | HTTP/1.1 400 Bad Request                                        |
| 415 Unsupported Media Type | The payload format requested by the client is not supported by the server.                                                                                                                                        | HTTP/1.1 415 Unsupported Media Type                             |
| 401 Unauthorized           | The request was denied due to an invalid or missing access token.                                                                                                                                                 | <p>HTTP/1.1 401 Unauthorized</p><p>WWW-Authenticate: Bearer</p> |
| 403 Forbidden              | The request was denied due to the access token having insufficient privileges.                                                                                                                                    | HTTP/1.1 403 Forbidden                                          |
| 404 Not Found              | The requested resource does not exist or is not accessible.                                                                                                                                                       | HTTP/1.1 404 Not Found                                          |
| 409 Conflict               | Data was updated by another client after the last read by this client. The client may retry the request.                                                                                                          | HTTP/1.1 409 Conflict                                           |
| 500 Internal Server Error  | An internal server error has occurred.                                                                                                                                                                            | HTTP/1.1 500 Internal Server Error                              |
| 503 Service Unavailable    | The service is temporarily unavailable.                                                                                                                                                                           | HTTP/1.1 503 Service Unavailable                                |

### Paths

The web services are available at the following path:

**/rms/api/public/noark5/v1/{service-name}**

### Dates encoding

- Dates must be encoded as **ISO 8601** strings prior to sending them to the web services.
- Dates are returned by the web services as **ISO 8601** formatted strings.

### Error message formats

The web services support the following error message response formats:
- *plain text* (default)
- *JSON*

#### Plain text

The default error message response format. Use JSON format for more detailed messages.

#### JSON

JSON error message responses will generally provide more detailed information about the problem when it is related to client errors.

To enable JSON error message responses, the following HTTP header has to be added to the request:
``` text
X-Documaster-Error-Response-Type: application/json
```

**Response**

``` text
Content-Type: application/json

{
  "errorId": string,
  "description": string,
  "status": number
}
```

###### Details

- **errorId**
  - unique identifier of the occurrence of the problem
- **description**
  - human-readable explanation specific to the occurrence of the problem
- **status**
  - HTTP status code

# Core services

Address:
```
https://{server}:{port}/rms/api/public/noark5/v1
```

## **query**

Queries for objects of a given type.

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
  "joins":
    {
      string: string,
      ...
    }
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
- **joins**
  - alias name : reference field
  - allows to search in directly and indirectly linked objects (by executing database JOINs)
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
            "fieldId": {"type": "string|long|double|timestamp|encrypted", "values": [string|long|double|timestamp|encrypted] },
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
      - will be returned (if present) only for objects of type *Mappe*, *Moetemappe*, *Saksmappe*, *Sakspart*, *Registrering*, *Moeteregistrering*, *Journalpost*, *Korrespondansepart*, *Dokument*, and *Klasse*
      - **groupId**
        - group ID placeholder (e.g. sf3298fyf-8f9oqhf3)
      - **fieldId**
        - field ID placeholder (e.g. sf3298fyf-8f9oqhf3)
        - **type**
          - field type (possible values are *string*, *long*, *double*, *timestamp*, and *encrypted*)
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
            "fieldId": {"values": [string|long|double|timestamp|encrypted] },
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
    - may be specified only for objects of type *Mappe*, *Moetemappe*, *Saksmappe*, *Sakspart*, *Registrering*, *Moeteregistrering*, *Journalpost*, *Korrespondansepart*, *Dokument*, and *Klasse*
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

# Full text web services

Address:
```
https://{server}:{port}/rms/api/public/noark5/v1/full-text
```

Perform operations on index documents in a search index.
- An **index document** is a set of fields and their corresponding values stored in a search index.
- Each index document has a **doctype** which corresponds to a set of Noark 5 fields stored in the index document.
- The supported doctypes and the object types (or fields) included in them are:

| doctype            | Included object types                                                                                                                                                                                                                                                                                                                                                                    |
|:-------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tekst              | Tekst (document text), Dokumentversjon (latest only), Dokument, AbstraktRegistrering (and its Noekkelord, Korrespondansepart, virksomhetsspesifikkeMetadata, secondary Klasse, and secondary Klassifikasjonssystem), AbstraktMappe (and its Noekkelord, virksomhetsspesifikkeMetadata, primary Klasse, secondary Klasse, secondary Klassifikasjonssystem, Sakspart, Arkivdel, and Arkiv) |
| Korrespondansepart | Korrespondansepart                                                                                                                                                                                                                                                                                                                                                                       |
| Registrering       | AbstraktRegistrering (and its Noekkelord, Korrespondansepart, virksomhetsspesifikkeMetadata, secondary Klasse, and secondary Klassifikasjonssystem), AbstraktMappe (and its Arkivdel, and Arkiv)                                                                                                                                                                                         |
| Mappe              | AbstraktMappe (and its Noekkelord, virksomhetsspesifikkeMetadata, and Sakspart)                                                                                                                                                                                                                                                                                                          |
| Arkivdel           | Arkivdel                                                                                                                                                                                                                                                                                                                                                                                 |

## **search**

Performs free text search in index documents.

**Request**

``` text
POST /rms/api/public/noark5/v1/full-text/search HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "doctype": string,
  "query": string,
  "offset: number,
  "limit": number
}
```

###### Details

- **doctype**
  - search for index documents with the specified doctype
- **query**
  - free text search string
  - the search is performed across multiple fields
  - the default search fields in the corresponding object types are:
    - Klasse: klasseIdent, tittel
    - AbstraktMappe: mappeIdent, tittel, offentligTittel, saksansvarlig
    - Sakspart: sakspartNavn, kontaktperson
    - AbstraktRegistrering: registreringsIdent, tittel, offentligTittel
    - Korrespondansepart: korrespondansepartNavn, epostadresse, kontaktperson, foedselsnummer
    - Dokument: tittel, beskrivelse
    - Tekst (of the last Dokumentversjon)
    - Noekkelord: verdi
    - Arkivdel: tittel
  - other search fields may be returned too; clients must not make an assumption about the exact set of fields returned by this endpoint
- **offset** (optional)
  - offset of the first result to retrieve
  - defaults to *0*
- **limit** (optional)
  - maximum number of results to retrieve
  - defaults to *10*

**Response**

``` text
Content-Type: application/json

{
  "total": number,
  "results": [
    {
      "ids": {
        string: [string],
        ...
      },
      "highlights": {
        string: [string],
        ...
      }
    },
    ...
  ],
  "facets": [
    {
      "field": string,
      "values": {
        string:number,
        ...
      }
    },
    ...
  ]
}
```

**Details**
- **total**
  - total number of results (index documents that match the query)
  - each result includes the IDs of the Noark 5 objects whose fields are included in the index document as well as highlighted snippets from matching fields
- **results**
  - **ids**
    - Noark 5 object ID field: object IDs
  - **highlights**
    - matching field: highlighted text snippets from that field
    - the beginning of a highlight is indicated by the string *|=hlstart=|*
    - the end of a highlight is indicated by the string *|=hlend=|*
- **facets**
  - **field**
    - name of a facet field
    - the default facet fields in the corresponding object types are:
      - Arkivdel: title,
      - Klasse: klasseIdent_tittel (concatenated)
      - Korrespondansepart: epostadresse
      - AbstraktMappe: mappeIdent_tittel (concatenated), saksaar, saksansvarlig
      - AbstraktRegistrering: journalansvarlig
      - Korrespondansepart: foedselsnummer
      - Noekkelord: verdi
      - virksomhetsspesifikkeMetadata: documaster-technical-archive.project-type
    - other facet fields may be returned too; clients must not make an assumption about the exact set of facets returned by this endpoint
  - **values**
    - facet value: number of index documents with that value

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
      "defaultValue": {
        "code": string,
        "name": string,
        "description": string,
        ...
      },
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
  - **defaultValue**
    - default code list value for the specified type and field
    - **code**
      - unique identifier of the code list value in the particular code list
    - **name**
      - user-friendly name of the code list value
    - **description**
      - description of the code list value
  - **values**
    - all code list values for the specified type and field
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
          "fieldType": string|long|double|timestamp|encrypted,
          "fieldValues": [string|long|double|timestamp]
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
      - field type (possible values are string, long, double, timestamp, and encrypted)
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
  "fieldType": string|long|double|timestamp|encrypted,
  "fieldValues": [string|long|double|timestamp]
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
  - field type (possible values are string, long, double, timestamp, and encrypted)
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
  "fieldType": string|long|double|timestamp|encrypted,
  "fieldValues": [string|long|double|timestamp]
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
  - field type (possible values are string, long, double, timestamp, and encrypted)
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

# Bulk operations

Address:
```
https://{server}:{port}/rms/api/public/noark5/v1/bulk
```

## **close**

Closes a single entity and its underlying objects recursively and asynchronously: the web service returns immediately after checking that the entity exists and performs the operation asynchronously.

**Request**

``` text
GET /rms/api/public/noark5/v1/bulk/close HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "type": string,
  "id": string
}
```

###### Details

- **type**
  - object type \[Arkiv, Arkivdel, AbstraktMappe, AbstraktRegistrering, DokumentBeskrivelse, Klassifikasjonssystem, Klasse\]
- **id**
  - the ID of the object

**Response**

202 Accepted

## **open**

Opens a single entity and its underlying objects recursively and asynchronously: the web service returns immediately after checking that the entity exists and performs the operation asynchronously.

**Request**

``` text
GET /rms/api/public/noark5/v1/bulk/open HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "type": string,
  "id": string
}
```

###### Details

- **type**
  - object type \[Arkiv, Arkivdel, AbstraktMappe, AbstraktRegistrering, DokumentBeskrivelse, Klassifikasjonssystem, Klasse\]
- **id**
  - the ID of the object

**Response**

202 Accepted

# Access Control web services

## Managing access modifiers

This set of endpoints allows you to mark classifications systems (Klassifikasjonssystem) and Code lists as access controlling.

Address:
```
https://{server}:{port}/rms/api/public/noark5/v1/access-modifier/
```

### Set determines access control flag on Klassifikasjonssystem

Marks a Klassifikasjonssystem as participating in access control.

**Request**

``` text
POST /rms/api/public/noark5/v1/access-modifier/klassifikasjons-system HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "id": string,
  "determinesAccessControl": boolean
}
```

###### Details

- **id**
  - the ID of the Klassifikasjonssystem
- **determinesAccessControl**
  - whether the Klassifikasjonssystem participates in access control.

**Response**

200 OK

### Set determines access control flag on code list

Marks a code list as participating in access control.

Supported code lists are mappetype, dokumenttype, skjerming, administrativEnhet.

**Request**

``` text
POST /rms/api/public/noark5/v1/access-modifier/code-list HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "id": string,
  "determinesAccessControl": boolean
}
```

###### Details

- **id**
  - the ID of the code list
  - one of mappetype, dokumenttype, skjerming, administrativEnhet
- **determinesAccessControl**
  - whether the code list participates in access control.

**Response**

200 OK

### Get determines access control flag on Klassifikasjonssystem

Fetch information whether a Klassifikasjonssystem participates in access control.

**Request**

``` text
GET /rms/api/public/noark5/v1/access-modifier/klassifikasjons-system/{id} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

###### Details

- **id**
  - the ID of the Klassifikasjonssystem

**Response**

```text
Content-type: application/json

{
  "determinesAccessControl": boolean
}
```

### Get determines access control flag on code list

Fetch information whether a Code list participates in access control.

Supported code lists are mappetype, dokumenttype, skjerming, administrativEnhet.

**Request**

``` text
GET /rms/api/public/noark5/v1/access-modifier/code-list/{id} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

###### Details

- **id**
  - the ID of the code list
  - one of mappetype, dokumenttype, skjerming, administrativEnhet

**Response**

```text
Content-type: application/json

{
  "determinesAccessControl": boolean
}
```