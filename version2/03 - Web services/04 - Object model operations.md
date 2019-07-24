Object model operations
-------------
# Services

### **query**

Queries for objects of a given type.

**Request**

``` text
POST /rms/api/public/noark5/v2/query HTTP/1.1
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

**Details**

- **type**
  - type of object to search for
- **offset** (optional)
  - offset of first result
  - defaults to 0
- **limit**
  - maximum number of results to retrieve
- **query** (optional)
  - an expression written in the web services query language defined in this document
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
  - takes effect only if a screening has been specified on the object or any of its parent objects
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
        "customMetadata": {
          "groupId": {
            "fieldId": {"type": "string|long|double|date", "values": [string|long|double|date] },
            ...
          },
          ...
        }
      },
      "links": {
        string: number,
        ...
      },
      "effectivePermissions": [string, ...]
    },
    ...
  ]
}
```

**Details**

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
    - **customMetadata** (optional)
      - custom metadata defined for the object
      - will be returned (if present) only for objects that support custom metadata
      - **groupId**
        - group ID placeholder (e.g. sf3298fyf-8f9oqhf3)
      - **fieldId**
        - field ID placeholder (e.g. sf3298fyf-8f9oqhf3)
        - **type**
          - field type (possible values are *string*, *long*, *double*, and *date*)
        - **values**
          - array of one or more field values of the particular type
- **links**
  - all any-to-one reference fields of the object
- **effectivePermissions**
  - effective permissions of the object
  - array of any of "R", "U", "I", "M", "D", "G", "USM" (values are case-sensitive; each value can appear only once)

---

### **upload**

Uploads a file to the system. The system returns a unique ID for the uploaded file.

**Request**

``` text
POST /rms/api/public/noark5/v2/upload HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Disposition: attachment; filename="ASCII_FILENAME"; filename*=utf-8''UTF8_FILENAME
Content-Type: application/octet-stream

[Binary stream]
```

**Details**

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

**Details**

- **id**
  - the unique ID for the uploaded file

---

### **transaction**

Saves (creates or updates), links, unlinks, and deletes objects in a single transaction.

**Request**

``` text
POST /rms/api/public/noark5/v2/transaction HTTP/1.1
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

**Details**

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

**Details**

- **saved**
  - created or updated objects
    - object ID (from the request) : created or updated object
  - if no objects have been saved, an empty JSON object will be returned

#### **Action: save**

**Request**

``` text
POST /rms/api/public/noark5/v2/transaction HTTP/1.1
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
        "customMetadata": {
          "groupId": {
            "fieldId": {"values": [string|long|double|date] },
            ...
          },
          ...
        }
      },
      "grant": {
        number: [string],
        ...
      },
      "revoke": {
        number: [string],
        ...
      }
    },
    ...
  ]
}
```

**Details**

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
  - **customMetadata** (optional)
    - custom metadata to write for this object
    - may be specified only for objects that support custom metadata
    - **groupId**
      - group ID placeholder which identifies the group that a business-specific metadata field belongs to
      - **fieldId**
        - field ID placeholder which identifies a business-specific metadata field
        - **values**
          - array of zero or more values to write to the specified business-specific metadata field
- **grant** (optional)
  - grant permissions to the *new object* for one or more groups:
    - group ID: \[permission, ...\]
- **revoke** (optional)
  - revoke permissions from an *existing object* for one or more groups:
    - group ID: \[permission, ...\]

**WARNING:** The **customMetadata** field describes the desired state of the custom metadata for the object. This implies that:
- groups, fields, and values included in **customMetadata** will be added to the object;
- existing groups, fields, and values not included in **customMetadata** or included, but as empty arrays/objects, will be  removed from the object.

**Response**

``` text
{
  "saved": {
    string: {
      "type": string,
      "id": string,
      "version": number,
      "fields": {
        string: string|number|boolean|date,
        ...
      },
    string: { ... },
    ...
    }
  }
}
```

#### **Action: link**

**Request**

``` text
POST /rms/api/public/noark5/v2/transaction HTTP/1.1
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

**Details**

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

#### **Action: unlink**

**Request**

``` text
POST /rms/api/public/noark5/v2/transaction HTTP/1.1
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

**Details**

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

#### **Action: delete**

**Request**

``` text
POST /rms/api/public/noark5/v2/transaction HTTP/1.1
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

**Details**

- **action**
  - the **delete** action
- **type**
  - type of the object to delete
- **id**
  - real ID of the object to delete
- **version** (optional)
  - the fetched version of the object to delete (may be used internally for optimistic locking)

---

### **download**

Downloads the file with the specified ID. If the file is not registered in a records management structure, no access control limitations will apply and only the creator (uploader) of the file will be able to download it. Once the file has been registered, standard access control will apply.

**Request**

``` text
GET /rms/api/public/noark5/v2/download?id=DOCUMENT_ID HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

**Details**

- Downloads the file with the specified document ID

**Response**

``` text
Content-Type: */*

Binary content
```

---

# Query language

## Overview

The web services support a simple but powerful query language that allows one to query for objects of a particular type, specifying **criteria** on the **object fields** as well as **criteria on the fields of linked objects**. Note that a *linked object* may be linked directly or indirectly (via another object).

## Fields

The metadata model exposed by the web services defines three groups of fields (special, regular, and reference fields) and specifies which fields can be queried. The following table shows examples of how you can use fields when querying for objects:

| Query for                                            | What field to use        | Comment                                                                                                            |
|:-----------------------------------------------------|:-------------------------|:-------------------------------------------------------------------------------------------------------------------|
| **Folder** with a specific **id**                    | id                       | Only exact matching is supported for special fields.                                                               |
| **Folder** with a specific **title**                 | title                    | Different types of criteria can be specified on regular fields.                                                    |
| **Folder** in a specific **Series**                  | refSeries                | The **id** of a linked object is the only type of criterion one can specify on a reference field.                  |
| **Folder** in a **Series** with a specific **title** | refSeries.title          | Chaining via reference fields allows us to specify criteria on regular fields in linked objects.                   |
| **Folder** in an **Fonds** with a specific **title** | refSeries.refFonds.title | More that one level of reference field chaining is allowed. Note that the system may decide to define a threshold. |

## Expressions

An expression consists of a *field*, an *operator*, and one or two *parameters*.

Parameter values are not a part of the query and must match the data type of the field for which a criterion is specified.

### Simple expressions

The following table shows the supported simple expressions:

| Expression       | Supported field data types | Meaning                                                       | Comment                                                  |
|:-----------------|:---------------------------|:--------------------------------------------------------------|:---------------------------------------------------------|
| field = @param1  | string, integer, reference | A field is equal to the value of the specified parameter.     |                                                          |
| field != @param2 | string, integer, reference | A field is not equal to the value of the specified parameter. |                                                          |
| field %= @param3 | string                     | A field is like the value of the specified parameter.         | A % sign in the parameter value denotes *any character*. |

### Range expressions

The following table shows the supported range expressions:

| Expression                | Supported field data types | Meaning                                                                             | Comment                                                                  |
|:--------------------------|:---------------------------|:------------------------------------------------------------------------------------|:-------------------------------------------------------------------------|
| field = [@param1:@param2] | date, number               | The value of a field is within the (inclusive) range defined by the two parameters. | A left/right square bracket defines the start/end of an inclusive range. |
| field = {@param1:@param2} | date, number               | The value of a field is within the (exclusive) range defined by the two parameters. | A left/right curly bracket defines the start/end of an exclusive range.  |
| field = [@param1:@param2} | date, number               | The value of a field is within the range defined by the two parameters.             | Start is inclusive, end is exclusive.                                    |
| field = {@param1:@param2] | date, number               | The value of a field is within the range defined by the two parameters.             | Start is exclusive, end is inclusive.                                    |

Note that a ***** character in the parameter value denotes *any value*.


## Grouping expressions

Expressions can be grouped using boolean operators, which are listed in the following table:

| Operator     | Meaning     | Precedence | Example                              |
|:-------------|:------------|:-----------|:-------------------------------------|
| &amp;&amp;   | Logical AND | 1st        | expression1 &amp;&amp; expression2   |
| &vert;&vert; | Logical OR  | 2nd        | expression1 &vert;&vert; expression2 |

The order of precedence can be changed using parentheses:

| Operator | Meaning                                             | Example                                               |
|:---------|:----------------------------------------------------|:------------------------------------------------------|
| ( )      | Change the order of precedence of boolean operators | (expression1 &vert;&vert; expression2) && expression3 |

## Examples

| Search for                                                | Query                                  | Parameter value   | Comment                                        |
|:----------------------------------------------------------|:---------------------------------------|:------------------|:-----------------------------------------------|
| **Document** with a given **id**                          | id = @id                               | 100               | 100 is the **id** of the **Document**          |
| **Document** with an exact **title**                      | title = @title                         | "Document"        |                                                |
| **Document** with a **title** not equal to                | title != @title                        | "Document"        |                                                |
| **Document** with a **title** that starts with            | title %= @title                        | "Docu%"           |                                                |
| **Document** with a **title** that ends with              | title %= @title                        | "%ment"           |                                                |
| **Document** with a **title** that contains a string      | title %= @title                        | "%ocu%"           |                                                |
| **Document**s with a **number** within a particular range | number=[@start:@end]                   | <p>3</p><p>5</p>  | **Document**s with document numbers  3,4, or 5 |
| **Document**s with a **number** within a particular range | number={@start:@end}                   | <p>3</p><p>5</p>  | **Document** document with number 4            |
| **Document**s with a **number** within a particular range | number=[@start:@end}                   | <p>3</p><p>5</p>  | **Document**s with document number 3 or 4      |
| **Document**s with a **number** within a particular range | number={@start:@end]                   | <p>3</p> <p>5</p> | **Document** with document number 4 or 5       |
| **Folder** in a specific **Series**                       | refSeries.id = @refSection             | 10                | 10 is the **id** of the **Section**            |
| **Folder** in a specific **Fonds**                        | refSeries.refFonds.id = @refArchive    | 5                 | 5 is the **id** of the **Archive**             |
| **Folder** in a **Fonds** with a specific **title**       | refSeries.refFonds.title = @arkivTitle | "Fonds"           | &nbsp;                                         |
