Access group management
----------------

### **groups/get**
Gets all access groups or optionally the group with the specified ID.

**Request**
```
GET /rms/api/public/noark5/v2/groups/get?groupId=GROUP_ID HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

**Details**
- **groupId** (optional)
  - the ID of the group to get
  - if not specified, all groups will be returned

**Response**
```
Content-Type: application/json

{
  "groups": [
     {
       "id": number,
       "name": string,
       "description": string,
       "claimExpression": string,
       "rootPermissions": [string],
       "servicePermissions": [string]
     },
     ...
  ]
}
```

**Details**
-  **groups**
  - zero or more access groups
- **id**
  - group ID
- **name**
  - group name
- **description**
  - group description
- **claimExpression**
  - currently, a single claim that maps users to this group
  - more complex expressions may be supported in future versions
- **rootPermissions**
  - root node permissions for the group
- **servicePermissions**
  - service permissions for the group

---

### **groups/create**
Creates an access group and optionally specifies root node permissions for the new group.

**Request**
```
POST /rms/api/public/noark5/v2/groups/create HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "name": string,
  "description": string,
  "claimExpression": string,
  "rootPermissions": [string],
  "servicePermissions": [string]
}
```

**Details**
- **name**
  - user-friendly name of the group
- **description** (optional)
  - description of the group
- **claimExpression**
  - a single claim that maps users to this group
  - more complex expressions may be supported in future versions
- **rootPermissions** (optional)
  - permissions are any of "RT, "R", "U", "I", "M", "D", "G", "USM" (values are case-sensitive; each value can appear only once)
    - note that other, model-specific values, may also be present in the array
  - if not specified, no permissions will be set for the root node
- **servicePermissions**
  - the service permissions to be associated with the given access group, e.g.: *documaster.rms.administrator*, *documaster.rms.create-document*, etc.

**Response**
```
Content-Type: application/json

{
  "id": number,
  "name": string,
  "description": string,
  "claimExpression": string,
  "rootPermissions": [string],
  "servicePermissions": [string]
}
```

**Details**

All fields of the newly created group are returned, including the ID of the group.

---

### **groups/update**
Updates the specified access group.

**Request**
```
POST /rms/api/public/noark5/v2/groups/update HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "id": number,
  "name": string,
  "description": string,
  "claimExpression": string,
  "rootPermissions": [string],
  "servicePermissions": [string]
}
```

**Details**
- **id**
  - ID of the group to update
- **name** (optional)
  - updated group name
- **description** (optional)
  - updated group description
- **claimExpression** (optional)
  - currently, a single claim that maps users to this group
  - more complex expressions may be supported in future versions
- **rootPermissions** (optional)
  - updated root node permissions for the group
  - note that these permissions will overwrite any existing root node permissions
- **servicePermissions** (optional)
  - update service permissions for the group
  - note that these service permissions will overwrite any existing service permissions

**Response**
```
{
  "id": number,
  "name": string,
  "description": string,
  "claimExpression": string,
  "rootPermissions": [string],
  "servicePermissions": [string]
}
```

**Details**

All fields of the updated group are returned.

---

### **groups/delete**
Deletes the specified access group.

**Request**
```
POST /rms/api/public/noark5/v2/groups/delete?groupId=GROUP_ID HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

**Details**
- **groupId**
  - ID of the group to delete
