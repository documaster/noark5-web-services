List management
-----------

### **lists**
Gets all lists supported by the system, the lists for a given object type, or the list for a specific field in a given type.

**Request**
``` text
GET /rms/api/public/noark5/v2/lists?type=OBJECT_TYPE&field=FIELD_NAME HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```
**Details**
- **type** (optional)
  - type of object to fetch the lists for
- **field** (optional)
  - field in the specified type to fetch the list for
If **type** and **field** are both not specified, the response will contain all lists supported by the system.

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
          "authority": string
        },
        ...
      ]
    },
    ...
  ]
}
```
**Details**
- **results**
  - array of one or more lists
  - **type**
    - object type that this list applies to
  - **field**
    - field in the object type that this list applies to
    - **code**
      - code of the list value
    - **name**
      - user-friendly name of the list value
    - **description**
      - description of the list value
    - **authority** (optional)
      - authority of the list value

---

### **lists/{listId}/{code}**

#### Create or update a list value

**Request**

``` text
PUT /rms/api/public/noark5/v2/lists/{listId}/{code} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
{
  "name": string,
  "description": string,
  "authority": string
}
```

**Details**
- **listId**
  - ID of the list in which to create or update the list value
- **code**
  - code of the list value to create or update
  - unique for the particular list
- **name**
  - user-friendly name of the list value
- **description**
  - description of the list value
- **authority** (optional)
  - authority of the list value
  - applicable only for the *screening* list

**Response**

``` text
Content-type: application/json
{
  "code": string,
  "name": string,
  "description": string,
  "authority": string
}
```

**Details**
- **code**
  - code of the created or updated list value
- **name**
  - user-friendly name of the list value
- **description**
  - description of the list value
- **authority** (optional)
  - authority of the list value
  - will be returned only for values of the *screening* list

#### Delete a list value

**Request**
``` text
DELETE /rms/api/public/noark5/v2/lists/{listId}/{code} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

**Details**
- **listId**
  - ID of the list from which to delete the list value
- **code**
  - code of the list value to delete

**Response**

No content will be returned upon successful completion.
