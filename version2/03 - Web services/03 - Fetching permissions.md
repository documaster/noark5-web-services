Fetching permissions
---------------------

### **permissions/get**
Gets the explicit or effective permissions of a specified object, optionally for a specified group.

**Request**
```
GET /rms/api/public/noark5/v2/permissions/get?type=TYPE&objectId=OBJECT_ID&groupId=GROUP_ID&permissionType=[explicit|effective] HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

**Details**
- **type**
  - object type
- **objectId**
  - object ID
- **groupId** (optional)
  - group ID
  - if not specified, permissions for all groups will be returned
- **permissionType**
  - The type of permissions to fetch (*effective* or *explicit*)
    - *explicit* are the ones that have been explicitly set on the specified node
    - *effective* are the ones that are "in effect" as a result of the the explicit permission set on the specified node and all of its parent nodes

**Response**
```
Content-Type: application/json

{
  "permissions": {
    string: [string],
    ...
  }
}
```

**Details**
- **permissions**
  - group ID: permissions
    - permissions are any of "RT", "R", "U", "I", "M", "D", "G", "USM" (values are case-sensitive; each value can appear only once)
      - note that other, model-specific values, may also be present in the array

---

### **service-permissions/get**
Gets all service permissions that apply to the current user.

**Request**
```
GET /rms/api/public/noark5/v2/service-permissions/get
Authorization: Bearer ACCESS_TOKEN
```

**Response**
```
Content-Type: application/json

{
  "servicePermissions": [string]
}
```

**Details**
- **servicePermissions**
  - a list of one more service permissions that apply to the user associated with the specified ACCESS\_TOKEN
