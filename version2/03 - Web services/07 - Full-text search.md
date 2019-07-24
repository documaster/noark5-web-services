Full-text search
-----------------

# Overview

All full-text search services work with an index which stores *index documents*. An index document is a flat list of key-value pairs where the value can be either a single value or an array of values:

```
{
  "field1": ["val1"],
  "field2": ["val2", "val3", "val4"]
}
```

Each index document has a **doctype** which determines which types of objects (and their fields) from the object model are included in the index document.

The following table lists the supported doctypes:

| Doctype         | Included objects from the object model                                                                                                                                           |
|:----------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <p>Series</p>   | <p>Series</p>                                                                                                                                                                    |
| <p>File</p>     | <p>File (including parent Folders)</p> <p>Class (primary/secondary)</p><p>CaseParty (for CaseFiles)</p><p>MeetingParticipant (for MeetingFolders)</p> <p>Keyword</p> <p>Note</p> |
| <p>Record</p>   | <p>Record</p> <p>CorrespondenceParty</p> <p>Class (of Record; primary/secondary)</p> <p>Keyword</p> <p>Note</p>                                                                  |
| <p>Document</p> | <p>Text of the document</p> <p>DocumentVersion</p><p>Document</p><p>Class (of Document; primary/secondary)</p> <p>All fields contained in the other doctypes</p>                 |

# Services

### **search**

Searches the index and returns matching index documents (which contain the IDs of the corresponding objects from the object model).

**Request**
```
POST /rms/api/public/noark5/v2/noark5/search HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "doctype": string,
  "offset": number,
  "limit": number,
  "query": string,
  "fieldQuery": {
    string: string,
    ...
  },
  "dateRanges": [
    {
      "field": string,
      "from": date,
      "to": date
    }
  ],
  "filters: {
    string: [string],
    ...
  }
}
```

**Details**
- **doctype** (optional)
  - search for index documents with the specified doctype
  - defaults to the **Document** doctype if not specified
-  **offset**
  - offset of the first result to retrieve
- **limit**
  - maximum number of results to retrieve
- **query** (conditionally required)
  - free text search string
  - the search is performed across multiple fields
  - required if **fieldQuery** or **dateRange **is not specified
  - cannot be specified together with **fieldQuery**
- **fieldQuery** (conditionally required)
  - field name: search string
  - search in specific fields
  - required if **query** or **dateRange** is not specified
  - cannot be specified together with **query**
- **dateRanges** (optional)
  - filter results in the specified date ranges
  - can be specified without **query** and **fieldQuery** in which case it will apply the filter on all documents with the specified doctype
  - **field**
    - name of a date field
  - **from** (optional)
    - from date
  - **to** (optional)
    - to date
- **filters**
  - field name: one or more strings to match
    - the strings are ORed
  - return only results that have a match for every specified field
  - can be used to filter on facets

**Response**
```
Content-Type: application/json

{
  "total": number,
  "results": [
    {
      "ids": {
        string: [number],
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
  - total number of results (index documents that match the search)
  - each result includes the IDs of objects whose fields are contained in the index document as well as highlighted snippets from matching fields
- **ids**
  - object ID field name: object IDs
- **highlights**
  - matching field: highlighted text snippets from that field
  - the beginning of a highlight is indicated by the string "|=hlstart=|" (quotes excluded)
  - the end of a highlight is indicated by the string "|=hlend=|" (quotes excluded)
- **facets**
  - **field**
    - name of a facet field
  - **values**
    - facet value: number of index documents with that value

---

### **autocomplete**

Performs autocomplete in one or more fields in index documents with the specified doctype.

**Request**
```
POST /rms/api/public/noark5/v2/noark5/autocomplete HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json

{
  "doctype": string,
  "query": {
    string: string,
    ...
  },
  "limit": number
}
```

**Details**
- **doctype**
  - one of the supported doctypes
- **query**
  - field name: string to autocomplete
- **limit**
  - maximum number of suggestions to retrieve

**Response**
```
Content-Type: application/json

{
  "results": {
    string: [string],
    ...
  }
}
```

**Details**
- **results**
  - autocomplete suggestions
  - field name: suggestions
