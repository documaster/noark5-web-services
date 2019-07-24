Custom metadata management
-------------------------------------------------

### **custom-metadata**

Gets all custom metadata fields available in the system, a group of fields, or a single field.

**Request**

``` text
GET /rms/api/public/noark5/v2/custom-metadata?groupId=GROUP_ID&fieldId=FIELD_ID HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

**Details**

- **groupId** (optional)
  - the group ID of the fields to be fetched
- **fieldId** (optional)
  - the field ID of the fields to be fetched

If **groupId** and **fieldId** are both not specified, the response will contain all custom metadata fields.

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

**Details**

- **results**
  - array of zero or more groups of custom metadata fields
  - **groupId**
    - system-unique group ID (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
  - **groupName**
    - group name
  - **groupDescription**
    - group description
  - **fields**
    - array of zero or more custom metadata fields in the particular group
    - **fieldId**
      - system-unique field ID (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
    - **fieldName**
      - field name
    - **fieldDescription**
      - field description
    - **fieldType**
      - field type (possible values are string, long, and double)
    - **fieldValues** (optional)
      - array of field values of the particular field type
      - will be returned only for controlled fields (an array of zero or more values); will not be returned for uncontrolled fields

---

### **custom-metadata/group/{groupId}**

#### **Create or update a custom metadata group**

**Request**

``` text
PUT /rms/api/public/noark5/v2/custom-metadata/group/{groupId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "groupName": string,
  "groupDescription": string
}
```

###### Details

- **groupId**
  - group ID of the group to be created or updated (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
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

**Details**

- **groupId**
  - system unique group ID of the created or updated group (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
- **groupName**
  - group name
- **groupDescription**
  - group description

#### **Delete a custom metadata group**

**Request**

``` text
DELETE /rms/api/public/noark5/v2/custom-metadata/group/{groupId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

**Details**

- **groupId**
  - group ID of the group to be deleted (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)

**Response**

No content will be returned upon successful completion.

---

### **custom-metadata/group/{groupId}/field/{fieldId}**

#### **Create or update a custom metadata field**

**Request**

``` text
PUT /rms/api/public/noark5/v2/custom-metadata/group/{groupId}/field/{fieldId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "fieldName": string,
  "fieldDescription": string,
  "fieldType": string|long|double,
  "fieldValues": [string|long|double]
}
```

**Details**

- **groupId**
  - group ID of the group in which to create or update the field
- **fieldId**
  - field ID of the field to be created or updated
- **fieldName**
  - field name
- **fieldDescription**
  - group description
- **fieldType**
  - field type (possible values are string, long, and double)
- **fieldValues** (optional)
  - array of field values of the particular field type
  - must be supplied only for controlled fields (an array of zero or more values); must not be supplied for uncontrolled fields

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

**Details**

- **fieldId**
  - system-unique field ID of the created or updated field (a string comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit)
- **fieldName**
  - field name
- **fieldDescription**
  - field description
- **fieldType**
  - field type (possible values are string, long, and double)
- **fieldValues** (optional)
  - array of field values of the particular field type
  - will be returned only for controlled fields (an array of zero or more values); will not be returned for uncontrolled fields

#### **Delete a custom metadata field**

**Request**

``` text
DELETE /rms/api/public/noark5/v2/custom-metadata/group/{groupId}/field/{fieldId} HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

**Details**

- **groupId**
  - group ID of the group from which to delete the field
- **fieldId**
  - field ID of the field to delete

Both **groupId** and **fieldId** are strings comprised of lower-case letters, digits, and dashes that must begin with a letter and end with a letter or a digit.

**Response**

No content will be returned upon successful completion.
