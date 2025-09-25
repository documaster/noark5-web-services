Object types
-------

## Overview

This page describes the object types present in the Noark 5 archive models supported by Documaster.

The objects are presented with their corresponding inheritance. For example *Arkiv (Finalizable Object)* introduces the object of type *Arkiv* which inherits all properties of *Finalizable Object*.

### Abstract object types

The following object types in Documaster represent folders:
- Mappe
- Moetemappe
- Saksmappe

These types are referred to collectively as **AbstraktMappe**. This name can be used e.g. to search for folders of all types.

The following object types in Documaster represent records:
- Basisregistrering
- Moeteregistrering
- Journalpost
- Arkivnotat

These types are referred to collectively as **AbstraktRegistrering**. This name can be used e.g. to search for records of all types.

The following object types in Documaster represent national identifiers:
- Adresse
- Bygning
- Eiendom
- Plan

These types are referred to collectively as **NasjonalIdentifikator**. This name can be used e.g. to search for national identifiers of all types.

### Field types

The fields for each object type are separated into three groups:

- **Special fields**
    - **id**
      - A field that uniquely identifies the object **within the system** (an ID)
      - Can be used to query for a specific object
    - **version**
      - A field that contains the version of the object (used for optimistic locking)
- **Regular fields**
    - String, date, timestamp, number, and boolean fields that generally can be saved, viewed, and queried
    - Business-specific metadata (custom metadata, not defined in Noark 5) is stored in object (JSON object) fields
    - Some fields in this specification have different names from the fields in the [Noark 5 version 4 metadata catalog](https://www.arkivverket.no/forvaltning-og-utvikling/noark-standarden/noark-5/noark5-standarden) but field numbers from the metadata catalog (e.g. M001) are retained here for reference purposes. There are also fields in this specification which are not included in the metadata catalog and must be standardized in a future version of Noark.
-   **Reference fields**
    - Fields used to link objects and to query for linked objects

Note that:
- Even though many fields are sortable, it only makes sense to sort on some of them.
- Documaster may restrict access to objects based on its access control model and the permissions defined in the system. How such restrictions work is outside of the scope of this specification and therefore not described here.

### Notation

The following notation is used in the tables below:

- **V**
    - denotes **true** (a checkmark)
- **sm**
    - denotes **system-managed fields**, i.e. fields that you are not allowed to ever write to (*Read Only*)
- **cw**
    - denotes **conditionally-writable fields**, i.e. fields that are only writable if you are granted special permissions for this in Documaster
    - there are good reasons why these fields are typically not writable

### Table columns
- **Field no.**
    - Field number in the Noark 5 standard. Descriptions of the fields can be found in the document "Vedlegg 1: Metadatakatalog" that is available at this page:
    https://www.arkivverket.no/forvaltning-og-utvikling/noark-standarden/noark-5/noark5-standarden
- **Type**
    - Data type
- **Field**
    - Field name as it is used in the API
- **Not null**
    - This field cannot be null. It must have a default value or a value provided via the API.
- **Default**
    - If a value is not provided via the API, a default value will be generated.
- **Code list**
    - If there is a value in the code list column, the value in this field must be the code value from a code list entry.
- **Save**
    - If you can use this field in a Save operation
- **View**
    - You can read a value from this field
- **Query**
    - This field can be used for filtering in queries.
- **Sort**
    - This field can be used for sorting in queries.
- **Link**
    - This field can be used in Link operations. (ref* fields)
- **Unlink**
    - This field can be used in Unlink operations. (ref* fields)

### User permissions
**Permissions** - required user rights/permissions to carry on update operations
- **USM**
    - Update System Managed - allows modification of special system fields
- **EFO**
    - Edit Finalized Objects - allows modification of finalized objects
        - Allows modification of finalized objects or children of finalized objects
        - A finalized object is an object whose avsluttetDato is set
- **JO** - Journal objects
    - Allows the journaling of registry entries (journalpost)
    - "journalling" implies setting the journalstatus to J
- **EJO**
    - Edit Journaled Objects - allows modification of journaled objects
- **PO** - Publish objects
    - Allows the publishing and unpublishing of records
    - "publishing" implies setting the publiseringsstatus or eInnsynStatus of any record type (registrering, arkivnotat, moeteregistrering, journalpost)


## Archives and archive sections

### Basic Archival Object

The **Basic Archival Object** contains the common fields inherited by all archival objects in the system.

Note: **EFO** required if finalized

| Field no. | Type      | Field                  | Not null |     Default     | Code list      |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:-----------------------|:--------:|:---------------:|:---------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
|           | string    | id                     |    V     | Auto generated  |                |  |  sm  |  V   |   V   |  V   |      |        |         |
|           | string    | version                |    V     | Auto generated  |                |  |  sm  |  V   |   V   |      |      |        |         |
|           |           |                        |          |                 |                |  |      |      |       |      |      |        |         |
| M001      | string    | uuid                   |    V     | Random uuid     |                |  |  cw  |  V   |   V   |  V   |      |        | USM     |
| M600      | timestamp | opprettetDato          |    V     | Now             |                |  |  cw  |  V   |   V   |  V   |      |        | USM     |
| M601      | string    | opprettetAv            |    V     | Current user    |                |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; USM </p><p>&bull; if any of the opprettetAv* fields is provided, both have to be provided </p><p>&bull; if none is provided, Documaster will default to the current user.</p> |
| M601      | string    | opprettetAvBrukerIdent |    V     | Current user ID |                |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; USM </p><p>&bull; if any of the opprettetAv* fields is provided, both have to be provided </p><p>&bull; if none is provided, Documaster will default to the current user.</p> |

### Finalizable Object ([Basic Archival Object](#basic-archival-object))

The **Finalizable Object** type contains all the fields required by the NOARK 5 finalization process.

Note: **EFO** required if finalized
| Field no. | Type      | Field                  | Not null |              Default             | Code list      |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                      |
|:----------|:----------|:-----------------------|:--------:|:--------------------------------:|:---------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-----------------------------------------------------------------------------------------------------------------------------|
| M602      | timestamp | avsluttetDato          |          | Now                              |                |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; If finalized M602 and both M603 must not be null</p><p>&bull; If not finalized M602 and both M603 must be null</p> |
| M603      | string    | avsluttetAv            |          | CurentUser if both M603 are null |                |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; If finalized M602 and both M603 must not be null</p><p>&bull; If not finalized M602 and both M603 must be null</p> |
| M603      | string    | avsluttetAvBrukerIdent |          | CurentUser if both M603 are null |                |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; If finalized M602 and both M603 must not be null</p><p>&bull; If not finalized M602 and both M603 must be null</p> |

### Arkiv ([Finalizable Object](#finalizable-object-basic-archival-object))
(Fonds)

Note: **EFO** required if finalized
| Field no. | Type      | Field                  | Not null |   Default   | Code list      |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:---------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
| M020      | string    | tittel                 |    V     |             |                |  |  V   |  V   |   V   |  V   |      |        |         |
| M021      | string    | beskrivelse            |          |             |                |  |  V   |  V   |   V   |  V   |      |        |         |
| M050      | string    | arkivstatus            |    V     |      V      | arkivstatus    |  |  V   |  V   |   V   |      |      |        |         |
| M300      | string    | dokumentmedium         |    V     |      V      | dokumentmedium |  |  V   |  V   |   V   |      |      |        |         |
|           |           |                        |          |             |                |  |      |      |       |      |      |        |         |
|           | ref       | refArkivskaper         |    V     |             |                |  |      |      |   V   |      |  V   |        |         |
|           | ref       | refArkivdel            |          |             |                |  |      |      |   V   |      |  V   |   V    |  &nbsp; |

### Arkivskaper ([Basic Archival Object](#basic-archival-object))
(Fonds Creator)

Note: **EFO** required if finalized
| Field no. | Type      | Field                  | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
| M006      | string    | arkivskaperIdent       |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |         |
| M023      | string    | arkivskaperNavn        |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |         |
| M021      | string    | beskrivelse            |          |             |           |  |  V   |  V   |   V   |  V   |      |        |         |
|           |           |                        |          |             |           |  |      |      |       |      |      |        |         |
|           | ref       | refArkiv               |    V     |             |           |  |      |  V   |   V   |      |  V   |        | &nbsp;  |

### Arkivdel ([Finalizable Object](#finalizable-object-basic-archival-object))
(Series)

Note: **EFO** required if finalized

Note: *Arkivdel* can have either subfolders or records but not both

| Field no. | Type      | Field                             | Not null |   Default   | Code list      |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                       |
|:----------|:----------|:----------------------------------|:--------:|:-----------:|:---------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:----------------------------------------------------------------------------------------------------------------------------------------------|
| M020      | string    | tittel                            |    V     |             |                |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                               |
| M021      | string    | beskrivelse                       |          |             |                |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                               |
| M051      | string    | arkivdelstatus                    |    V     |      V      | arkivdelstatus |  |  V   |  V   |   V   |      |      |        |                                                                                                                                               |
| M300      | string    | dokumentmedium                    |    V     |      V      | dokumentmedium |  |  V   |  V   |   V   |      |      |        |                                                                                                                                               |
| M107      | date      | arkivperiodeStartDato             |    V     | Now         |                |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                               |
| M108      | date      | arkivperiodeSluttDato             |          |             |                |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; Mandatory if arkivdelstatus is set to overlapping period</p>                                                                        |
|           | date      | overlappingsperiodeSluttdato      |          |             |                |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; Mandatory if arkivdelstatus is set to overlapping period</p>                                                                        |
| M500      | string    | skjerming                         |          |             | skjerming      |  |  V   |  V   |   V   |      |      |        |                                                                                                                                               |
|           |           |                                   |          |             |                |  |      |      |       |      |      |        |                                                                                                                                               |
|           | ref       | refArkiv                          |    V     |             |                |  |      |  V   |   V   |      |  V   |   V    |                                                                                                                                               |
|           | ref       | refPrimaerKlassifikasjonssystem   |          |             |                |  |      |  V   |   V   |      |  V   |   V    | <p>Mandatory:</p><p>&bull; If null inherited from a child</p><p>&bull; If not null all childs must have the same primary KS </p>;             |
|           | ref       | refSekundaerKlassifikasjonssystem |          |             |                |  |      |      |   V   |      |  V   |   V    |                                                                                                                                               |
|           | ref       | refMappe                          |          |             |                |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **AbstraktMappe**</p>                                                                                                    |
|           | ref       | refRegistrering                   |          |             |                |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **Basisregistrering**</p>                                                                                                |
|           | ref       | refArkivdelForloeper              |          |             |                |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **Arkivdel**. Cross references are not allowed</p>                                                                       |
|           | ref       | refArkivdelArvtaker               |          |             |                |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **Arkivdel**. Cross references are not allowed</p><p>&bull; Mandatory if arkivdelstatus is set to overlapping period</p> |

## Classification

### Klassifikasjonssystem ([Finalizable Object](#finalizable-object-basic-archival-object))
(Classification System)

Note: **EFO** required if finalized

| Field no. | Type      | Field                   | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:------------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
| M086      | string    | klassifikasjonstype     |          |             |           |  |  V   |  V   |   V   |  V   |      |        |         |
| M020      | string    | tittel                  |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |         |
| M021      | string    | beskrivelse             |          |             |           |  |  V   |  V   |   V   |  V   |      |        |         |
|           |           |                         |          |             |           |  |      |      |       |      |      |        |         |
|           | ref       | refKlasse               |          |             |           |  |      |      |   V   |      |  V   |   V    |         |
|           | ref       | refArkivdelSomPrimaer   |          |             |           |  |      |      |   V   |      |  V   |   V    |         |
|           | ref       | refArkivdelSomSekundaer |          |             |           |  |      |      |   V   |      |  V   |   V    | &nbsp;  |

### Klasse ([Finalizable Object](#finalizable-object-basic-archival-object))
(Class)

Note: **EFO** required if finalized
| Field no. | Type      | Field                         | Not null |   Default   | Code list         |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                                            |
|:----------|:----------|:------------------------------|:--------:|:-----------:|:------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| M002      | string    | klasseIdent                   |    V     |             |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                    |
| M020      | string    | tittel                        |    V     |             |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                    |
| M021      | string    | beskrivelse                   |          |             |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                    |
| M450      | string    | kassasjonsvedtak              |          |             | kassasjonsvedtak  |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                    |
| M453      | string    | kassasjonshjemmel             |          |             | kassasjonshjemmel |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                    |
| M451      | number    | bevaringstid                  |          |             |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                    |
| M500      | string    | skjerming                     |          |             | skjerming         |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                    |
|           | timestamp | forventetKassasjonsDato       |          |             |                   |  |  sm  |  V   |   V   |  V   |      |        |                                                                                                                                                                    |
|           |           |                               |          |             |                   |  |      |      |       |      |      |        |                                                                                                                                                                    |
|           | object    | virksomhetsspesifikkeMetadata |          |             |                   |  |  V   |  V   |       |      |      |        |                                                                                                                                                                    |
|           |           |                               |          |             |                   |  |      |      |       |      |      |        |                                                                                                                                                                    |
|           | ref       | refKlassifikasjonssystem      |    V     |             |                   |  |      |  V   |   V   |      |  V   |   V    |                                                                                                                                                                    |
|           | ref       | refForelderKlasse             |          |             |                   |  |      |  V   |   V   |      |  V   |   V    |                                                                                                                                                                    |
|           | ref       | refBarnKlasse                 |          |             |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; Cannot have both refBarnKlasse and kassasjonsvedtak</p>                                                                                                  |
|           | ref       | refMappeSomPrimaer            |          |             |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **AbstraktMappe** as a primary **Klasse**</p> <p>&bull; **refBarnKlasse** cannot be set if **refMappeSomPrimaer** is set</p>                  |
|           | ref       | refMappeSomSekundaer          |          |             |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **AbstraktMappe** as a secondary **Klasse**</p> <p>&bull; **refBarnKlasse** cannot be set if **refMappeSomSekundaer** is set</p>              |
|           | ref       | refRegistreringSomPrimaer     |          |             |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **AbstraktRegistrering** as a primary **Klasse**</p> <p>&bull; **refBarnKlasse** cannot be set if **refRegistreringSomPrimaer** is set</p>    |
|           | ref       | refRegistreringSomSekundaer   |          |             |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **AbstraktRegistrering** as a secondary **Klasse**</p><p>&bull; **refBarnKlasse** cannot be set if **refRegistreringSomSekundaer** is set</p> |

## Folders

### Abstract mappe ([Finalizable Object](#finalizable-object-basic-archival-object))
(Abstract Folder)

Note: **EFO** required if finalized
| Field no. | Type      | Field                            | Not null |     Default     | Code list         |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                         |
|:----------|:----------|:---------------------------------|:--------:|:---------------:|:------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------------------------------------------------------------------------------------------------------------------------------|
| M003      | string    | mappeIdent                       |    V     | Auto set to ID  |                   |  |  cw  |  V   |   V   |  V   |      |        |                                                                                                                                 |
|           | string    | mappetype                        |          |                 | mappetype         |  |  V   |  V   |   V   |      |      |        |                                                                                                                                 |
| M020      | string    | tittel                           |    V     |                 |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                 |
| M025      | string    | offentligTittel                  |          |                 |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                 |
| M021      | string    | beskrivelse                      |          |                 |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                 |
| M300      | string    | dokumentmedium                   |    V     |      V          | dokumentmedium    |  |  V   |  V   |   V   |      |      |        |                                                                                                                                 |
| M450      | string    | kassasjonsvedtak                 |          |                 | kassasjonsvedtak  |  |  V   |  V   |   V   |      |      |        |                                                                                                                                 |
| M453      | string    | kassasjonshjemmel                |          |                 | kassasjonshjemmel |  |  V   |  V   |   V   |      |      |        |                                                                                                                                 |
| M451      | number    | bevaringstid                     |          |                 |                   |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                 |
| M500      | string    | skjerming                        |          |                 | skjerming         |  |  V   |  V   |   V   |      |      |        |                                                                                                                                 |
|           | timestamp | forventetKassasjonsDato          |          |                 |                   |  |  sm  |  V   |   V   |  V   |      |        |                                                                                                                                 |
|           |           |                                  |          |                 |                   |  |      |      |       |      |      |        |                                                                                                                                 |
| M711      | object    | virksomhetsspesifikkeMetadata    |          |                 |                   |  |  V   |  V   |       |      |      |        |                                                                                                                                 |
|           |           |                                  |          |                 |                   |  |      |      |       |      |      |        |                                                                                                                                 |
|           | ref       | refArkivdel                      |    c     |                 |                   |  |      |  V   |   V   |      |  V   |   V    | <p>&bull; exactly one of **refArkivdel** and **refForelderMappe** must be set</p>                                               |
|           | ref       | refRegistrering                  |          |                 |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **Basisregistrering**</p><p>&bull; exactly one of **refBarnMappe** and **refRegistrering** must be set</p> |
|           | ref       | refSakspart                      |          |                 |                   |  |      |      |   V   |      |  V   |        |                                                                                                                                 |
|           | ref       | refPrimaerKlasse                 |          |                 |                   |  |      |  V   |   V   |      |  V   |   V    | <p>&bull; references a **Klasse** from the primary **Klassifikasjonssystem** of the **Arkivdel**</p>                            |
|           | ref       | refSekundaerKlasse               |          |                 |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; references a **Klasse** from any **Klassifikasjonssystem**</p>                                                        |
|           | ref       | refEksternId                     |          |                 |                   |  |      |      |   V   |      |  V   |        |                                                                                                                                 |
|           | ref       | refNoekkelord                    |          |                 |                   |  |      |      |   V   |      |  V   |        |                                                                                                                                 |
|           | ref       | refMerknad                       |          |                 |                   |  |      |      |   V   |      |  V   |        |                                                                                                                                 |
|           | ref       | refNasjonalIdentifikator         |          |                 |                   |  |      |      |   V   |      |  V   |        |                                                                                                                                 |
|           | ref       | refKryssreferanseTilMappe        |          |                 |                   |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktMappe** that the current **AbstraktMappe** cross references                                                 |
|           | ref       | refKryssreferanseFraMappe        |          |                 |                   |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktMappe** that cross reference the current **AbstraktMappe**                                                  |
|           | ref       | refKryssreferanseTilRegistrering |          |                 |                   |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktRegistrering** that the current **AbstraktMappe** cross references                                          |
|           | ref       | refKryssreferanseFraRegistrering |          |                 |                   |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktRegistrering** that cross reference the current **AbstraktMappe**                                           |

### Mappe ([Abstract mappe](#abstract-mappe-finalizable-object))
(Folder)

Note: **EFO** required if finalized
| Field no. | Type      | Field                            | Not null |   Default   | Code list         |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                                                   |
|:----------|:----------|:---------------------------------|:--------:|:-----------:|:------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|           | string    | mappestatus                      |          |             | mappestatus       |  |  V   |  V   |   V   |      |      |        | _Since 3.6.1_                                                                                                                                                             |
|           |           |                                  |          |             |                   |  |      |      |       |      |      |        |                                                                                                                                                                           |
|           | ref       | refForelderMappe                 |    c     |             |                   |  |      |  V   |   V   |      |  V   |   V    | <p>&bull; references a parent **Mappe**</p><p>&bull; exactly one of **refArkivdel** and **refForelderMappe** must be set</p><p>&bull; Nesting limited to 5 max levels</p> |
|           | ref       | refBarnMappe                     |          |             |                   |  |      |      |   V   |      |  V   |   V    | <p>&bull; references children **Mappe**</p><p>&bull; exactly one of **refBarnMappe** and **refRegistrering** must be set</p><p>&bull; Nesting limited to 5 max levels</p> |

### Saksmappe ([Abstract mappe](#abstract-mappe-finalizable-object)) 
(Case file)

Note: **EFO** required if finalized
| Field no. | Type      | Field                            | Not null |     Default     | Code list          |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                                                                                                                                                                              |
|:----------|:----------|:---------------------------------|:--------:|:---------------:|:-------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|           | string    | prefiks                          |          |                 |                    |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; optional identifier of the system this **Saksmappe** originated from</p><p>&bull; it is strongly advised to populate this field when the target system contains or will contain data from multiple source case management systems</p><p>&bull; once set, this value cannot be modified</p> |
| M011      | number    | saksaar                          |    V     | Current year    |                    |  |  cw  |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                                                      |
| M012      | number    | sakssekvensnummer                |    V     | Auto inc        |                    |  |  cw  |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                                                      |
| M100      | date      | saksdato                         |    V     | Now             |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                                                      |
| M305      | string    | administrativEnhet               |    V     |                 | administrativEnhet |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                                                      |
| M306      | string    | saksansvarlig                    |    V     | Current user    |                    |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; if both saksansvarlig and saksansvarligBrukerIdent are left as null, the defaults apply.</p><p>&bull; Not allowed to supply only one of saksansvarlig or saksansvarligBrukerIdent and leave the other as null</p>                                                                          |
| M306      | string    | saksansvarligBrukerIdent         |    V     | Current user ID |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                                                      |
| M052      | string    | saksstatus                       |    V     |      V          | saksstatus         |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                                                      |
|           |           |                                  |          |                 |                    |  |      |      |       |      |      |        |                                                                                                                                                                                                                                                                                                      |
|           | ref       | refPresedens                     |          |                 |                    |  |      |      |   V   |      |  V   |   V    | refers to **Presedens**                                                                                                                                                                                                                                                                              |

### Sakspart ([Basic Archival Object](#basic-archival-object))
(Case party)

| Field no. | Type      | Field                         | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink |                   Comment                  |
|:----------|:----------|:------------------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------|
| M010      | string    | sakspartIdent                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M302      | string    | sakspartNavn                  |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
|           | string    | foedselsnummer                |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
|           | string    | organisasjonsnummer           |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
|           | string    | dnummer                       |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M406      | string    | postadresse                   |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M407      | string    | postnummer                    |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M408      | string    | poststed                      |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M409      | string    | land                          |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M410      | string    | epostadresse                  |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M411      | string    | telefonnummer                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M412      | string    | kontaktperson                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
| M711      | object    | virksomhetsspesifikkeMetadata |          |             |           |  |  V   |  V   |       |      |      |        |                                            |
| M303      | string    | sakspartRolle                 |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |                                            |
|           | boolean   | erSkjermet                    |    V     | false       |           |  |  V   |  V   |   V   |  V   |      |        | _Since 3.10.0_                             |
|           |           |                               |          |             |           |  |      |      |       |      |      |        |                                            |
|           | ref       | refMappe                      |    V     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p> |

### Moetemappe ([Abstract mappe](#abstract-mappe-finalizable-object))
(Meeting Folder)

Note: **EFO** required if finalized
| Field no. | Type      | Field                            | Not null |   Default   | Code list          |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:---------------------------------|:--------:|:-----------:|:-------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
| M008      | string    | moetenummer                      |    V     |             |                    |  |  V   |  V   |   V   |  V   |      |        |         |
| M370      | string    | utvalg                           |    V     |             |                    |  |  V   |  V   |   V   |  V   |      |        |         |
| M102      | date      | moetedato                        |    V     | Now         |                    |  |  V   |  V   |   V   |  V   |      |        |         |
| M371      | string    | moetested                        |          |             |                    |  |  V   |  V   |   V   |  V   |      |        |         |
|           |           |                                  |          |             |                    |  |      |      |       |      |      |        |         |
|           | ref       | refMoetedeltaker                 |    V     |             |                    |  |      |      |   V   |      |  V   |        |         |

### Moetedeltaker ([Basic Archival Object](#basic-archival-object))
(Meeting Participant)

| Field no. | Type      | Field                  | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink | Comment                                 |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:----------------------------------------|
| M372      | string    | navn                   |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |                                         |
| M373      | string    | funksjon               |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                         |
|           |           |                        |          |             |           |  |      |      |       |      |      |        |                                         |
|           | ref       | refMappe               |    V     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **Moetemappe**</p> |

## Records

### Basisregistrering ([Finalizable Object](#finalizable-object-basic-archival-object))
(Basic Record)

**Basisregistrering** introduces the common fields for all *registrering* objects.

Note: **EFO** required if finalized

| Field no. | Type      | Field                            | Not null |    Default    | Code list          |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                                                                                                                                                  |
|:----------|:----------|:---------------------------------|:--------:|:-------------:|:-------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| M004      | string    | registreringsIdent               |    V     | Auto inc      |                    |  |  cw  |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M020      | string    | tittel                           |    V     |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M025      | string    | offentligTittel                  |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M021      | string    | beskrivelse                      |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M024      | string    | forfatter                        |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
|           | string    | publiseringsstatus               |    V     | Not published |                    |  |  V   |  V   |   V   |  V   |      |        | Allowed values: NOT_PUBLISHED, PUBLISHED_METADATA, PUBLISHED_METADATA_AND_DOCUMENTS. **PO** required for any change of the publishing status                                                                                                                             |
|           | string    | eInnsynStatus                    |    V     | Not published |                    |  |  V   |  V   |   V   |  V   |      |        | Allowed values: NOT_PUBLISHED, PUBLISHED_METADATA, PUBLISHED_METADATA_AND_DOCUMENTS. **PO** required for any change of the publishing status                                                                                                                             |
| M300      | string    | dokumentmedium                   |    V     |      V        | dokumentmedium     |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                          |
|           | string    | journalansvarlig                 |          |               |                    |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; The name of the person responsible for this Basisregistrering (similar to M306 Saksmappe#saksansvarlig)</p><p>&bull; Both journalansvarlig and journalansvarligBrukerIdent must be null or specified</p>                                                       |
|           | string    | journalansvarligBrukerIdent      |          |               |                    |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; The user ID of the person responsible for this Basisregistrering as stored in journalansvarlig (similar to M306 Saksmappe#saksansvarligBrukerIdent)</p><p>&bull; Both journalansvarlig and journalansvarligBrukerIdent must be null or specified</p>           |
| M110      | date      | offentlighetsvurdertDato         |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M101      | date      | registreringsDato                |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M103      | date      | dokumentetsDato                  |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M109      | date      | forfallsdato                     |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M104      | timestamp | mottattDato                      |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M105      | timestamp | sendtDato                        |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M450      | string    | kassasjonsvedtak                 |          |               | kassasjonsvedtak   |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                          |
| M453      | string    | kassasjonshjemmel                |          |               | kassasjonshjemmel  |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                          |
| M451      | number    | bevaringstid                     |          |               |                    |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
| M500      | string    | skjerming                        |          |               | skjerming          |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                          |
|           | timestamp | forventetKassasjonsDato          |          |               |                    |  |  sm  |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                          |
|           |           |                                  |          |               |                    |  |      |      |       |      |      |        |                                                                                                                                                                                                                                                                          |
| M711      | object    | virksomhetsspesifikkeMetadata    |          |               |                    |  |  V   |  V   |       |      |      |        |                                                                                                                                                                                                                                                                          |
|           |           |                                  |          |               |                    |  |      |      |       |      |      |        |                                                                                                                                                                                                                                                                          |
|           | ref       | refArkivdel                      |    c     |               |                    |  |      |  V   |   V   |      |  V   |   V    | <p>&bull; references **Arkivdel**</p><p>&bull; exactly one of **refArkivdel** and **refMappe** must be set</p>                                                                                                                                                           |
|           | ref       | refMappe                         |    c     |               |                    |  |      |  V   |   V   |      |  V   |   V    | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refArkivdel** and **refMappe** must be set</p>                                                                                                                                                      |
|           | ref       | refKorrespondansepart            |          |               |                    |  |      |      |   V   |      |  V   |        |                                                                                                                                                                                                                                                                          |
|           | ref       | refDokument                      |          |               |                    |  |      |      |   V   |      |  V   |   V    |                                                                                                                                                                                                                                                                          |
|           | ref       | refPrimaerKlasse                 |          |               |                    |  |      |  V   |   V   |      |  V   |   V    | <p>&bull; references a **Klasse** from the primary **Klassifikasjonssystem** of the **Arkivdel**</p>                                                                                                                                                                     |
|           | ref       | refSekundaerKlasse               |          |               |                    |  |      |      |   V   |      |  V   |   V    | <p>&bull; references a **Klasse** from any **Klassifikasjonssystem**</p>                                                                                                                                                                                                 |
|           | ref       | refEksternId                     |          |               |                    |  |      |      |   V   |      |  V   |        |                                                                                                                                                                                                                                                                          |
|           | ref       | refNoekkelord                    |          |               |                    |  |      |      |   V   |      |  V   |        |                                                                                                                                                                                                                                                                          |
|           | ref       | refMerknad                       |          |               |                    |  |      |      |   V   |      |  V   |        |                                                                                                                                                                                                                                                                          |
|           | ref       | refNasjonalIdentifikator         |          |               |                    |  |      |      |   V   |      |  V   |        |                                                                                                                                                                                                                                                                          |
|           | ref       | refKryssreferanseTilMappe        |          |               |                    |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktMappe** that the current **AbstraktRegistrering** cross references                                                                                                                                                                                   |
|           | ref       | refKryssreferanseFraMappe        |          |               |                    |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktMappe** that cross reference the current **AbstraktRegistrering**                                                                                                                                                                                    |
|           | ref       | refKryssreferanseTilRegistrering |          |               |                    |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktRegistrering** that the current **AbstraktRegistrering** cross references                                                                                                                                                                            |
|           | ref       | refKryssreferanseFraRegistrering |          |               |                    |  |      |      |   V   |      |  V   |   V    | refers to **AbstraktRegistrering** that cross reference the current **AbstraktRegistrering**                                                                                                                                                                             |

### Moeteregistrering ([Basisregistrering](#basisregistrering-finalizable-object))
(Meeting Record)

Note: **EFO** required if finalized

Note: **EJO** required if journaled
| Field no. | Type      | Field                            | Not null |       Default      | Code list                |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:---------------------------------|:--------:|:------------------:|:-------------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
| M307      | string    | saksbehandler                    |    V     |                    |                          |  |  V   |  V   |   V   |  V   |      |        |         |
|           | string    | saksnummer                       |          |                    |                          |  |  V   |  V   |   V   |  V   |      |        |         |
| M305      | string    | administrativEnhet               |    V     |                    | administrativEnhet       |  |  V   |  V   |   V   |      |      |        |         |
| M085      | string    | moeteregistreringstype           |    V     |                    | moeteregistreringstype   |  |  V   |  V   |   V   |      |      |        |         |
| M088      | string    | moetesakstype                    |          |                    | moetesakstype            |  |  V   |  V   |   V   |      |      |        |         |
| M055      | string    | moeteregistreringsstatus         |          |                    | moeteregistreringsstatus |  |  V   |  V   |   V   |      |      |        |         |

### Arkivnotat ([Basisregistrering](#basisregistrering-finalizable-object))
(Archive Note)

_Since: 3.6.1_

Note: **EFO** required if finalized

Note: **EJO** required if journaled
| Field no. | Type      | Field                            | Not null |   Default   | Code list          |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:---------------------------------|:--------:|:-----------:|:-------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
|           |           | refArkivnotatDokumentflyt        |          |             |                    |  |      |      |   V   |      |  V   |   V    |         |


### Journalpost ([Basisregistrering](#basisregistrering-finalizable-object))

Note: **EFO** required if finalized

Note: **EJO** required if journaled

| Field no. | Type      | Field                             | Not null |   Default   |    Code list       |  | Save | View | Query | Sort | Link | Unlink |Comment                                                                                                                                                                                                                                                                                                                                                                                                          |
|:----------|:----------|:----------------------------------|:--------:|:-----------:|:------------------:|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|           | string    | prefiks                           |          |             |                    |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; optional identifier of the system this **Journalpost** originated from</p><p>&bull; it is strongly advised to populate this field when the target system contains or will contain data from multiple source case management systems</p><p>&bull; once set, this value cannot be modified</p>                                                                                                          |
| M013      | number    | journalaar                        |    V     |      V      |                    |  |  cw  |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
| M014      | number    | journalsekvensnummer              |    V     |      V      |                    |  |  cw  |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
| M015      | number    | journalpostnummer                 |    V     |      V      |                    |  |  cw  |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
|           | string    | *journalansvarlig                 |    V     |      V      |                    |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; The name of the person responsible for this Journalpost (similar to M306 Saksmappe#saksansvarlig)</p>                                                                                                                                                                                                                                                                                                 |
|           | string    | *journalansvarligBrukerIdent      |    V     |      V      |                    |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; The user ID of the person responsible for this Journalpost as stored in journalansvarlig (similar to M306 Saksmappe#saksansvarligBrukerIdent)</p>                                                                                                                                                                                                                                                     |
| M082      | string    | journalposttype                   |    V     |             | journalposttype    |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
| M053      | string    | journalstatus                     |    V     |      V      |  journalstatus     |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
|           | boolean   | skjermKorrespondanseParterEInnsyn |          |      V      |                    |  |  V   |  V   |   V   |  V   |      |        | When **true**, it indicates that **Korrespondansepart** objects of this **Journalpost** referenced by **refKorrespondansepart** will be screened in eInnsyn, even if the **Journalpost** is not effectively screened. When **false** or **null**, it indicates that **Korrespondansepart** objects will not be screened in eInnsyn, even if the **Journalpost** is effectively screened. Defaults to **false**. |
|           |           |                                   |          |             |                    |  |      |      |       |      |      |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
|           | ref       | refDokumentflyt                   |          |             |                    |  |      |      |   V   |      |  V   |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
|           | ref       | refAvskrivning                    |          |             |                    |  |      |      |   V   |      |  V   |        |                                                                                                                                                                                                                                                                                                                                                                                                                 |
|           | ref       | refTilknyttetAvskrivning          |          |             |                    |  |      |      |   V   |      |  V   |   V    |                                                                                                                                                                                                                                                                                                                                                                                                                 |
|           | ref       | refPresedens                      |          |             |                    |  |      |      |   V   |      |  V   |   V    | refers to **Presedens**                                                                                                                                                                                                                                                                                                                                                                                         |

NOTE: journalansvarlig and journalansvarligBrukerIdent are inherited from the Basic Record type and re-introduced in the table above because of the added constraints.

### Korrespondansepart ([Basic Archival Object](#basic-archival-object))
(Correspondence Party)

| Feild no. | Type      | Field                         | Not null |   Default   | Code list              |  | Save | View | Query | Sort | Link | Unlink | Comment                                           |
|:----------|:----------|:------------------------------|:--------:|:-----------:|:-----------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------------------------------------------------|
| M087      | string    | korrespondanseparttype        |    V     |             | korrespondanseparttype |  |  V   |  V   |   V   |      |      |        |                                                   |
| M400      | string    | korrespondansepartNavn        |    V     |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
|           | string    | foedselsnummer                |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
|           | string    | organisasjonsnummer           |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
|           | string    | dnummer                       |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M406      | string    | postadresse                   |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M407      | string    | postnummer                    |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M408      | string    | poststed                      |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M409      | string    | land                          |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M410      | string    | epostadresse                  |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M411      | string    | telefonnummer                 |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M412      | string    | kontaktperson                 |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M307      | string    | saksbehandler                 |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
|           | boolean   | erPerson                      |          |             |                        |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M305      | string    | administrativEnhet            |          |             | administrativEnhet     |  |  V   |  V   |   V   |      |      |        |                                                   |
|           | string    | ekspederingskanal             |          |             | ekspederingskanal      |  |  V   |  V   |   V   |      |      |        |                                                   |
|           | boolean   | skjermEInnsyn                 |          |    false    |                        |  |  V   |  V   |   V   |      |      |        | _Since 3.6.1_                                     |
|           |           |                               |          |             |                        |  |      |      |       |      |      |        |                                                   |
|           | object    | virksomhetsspesifikkeMetadata |          |             |                        |  |  V   |  V   |       |      |      |        |                                                   |
|           |           |                               |          |             |                        |  |      |      |       |      |      |        |                                                   |
|           | ref       | refRegistrering               |    V     |             |                        |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p> |

### Dokumentflyt ([Basic Archival Object](#basic-archival-object))
(Document Flow)

| Field no. | Type      | Field                  | Not null |   Default   | Code list  |  | Save | View | Query | Sort | Link | Unlink | Comment                                                             |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------:|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------------------------------------------------------------------|
| M665      | string    | flytFra                |    V     |             |            |  |  V   |  V   |   V   |  V   |      |        |                                                                     |
| M665      | string    | flytFraBrukerIdent     |    V     |             |            |  |  V   |  V   |   V   |  V   |      |        |                                                                     |
| M660      | string    | flytTil                |    V     |             |            |  |  V   |  V   |   V   |  V   |      |        |                                                                     |
| M660      | string    | flytTilBrukerIdent     |    V     |             |            |  |  V   |  V   |   V   |  V   |      |        |                                                                     |
| M661      | timestamp | flytMottattDato        |    V     |             |            |  |  V   |  V   |   V   |  V   |      |        |                                                                     |
| M662      | timestamp | flytSendtDato          |    V     |             |            |  |  V   |  V   |   V   |  V   |      |        |                                                                     |
| M664      | string    | flytMerknad            |          |             |            |  |  V   |  V   |   V   |  V   |      |        |                                                                     |
| M663      | string    | flytStatus             |    V     |             | flytStatus |  |  V   |  V   |   V   |      |      |        |                                                                     |
|           |           |                        |          |             |            |  |      |      |       |      |      |        |                                                                     |
|           | ref       | refArkivnotat          |    c     |             |            |  |      |  V   |   V   |      |  V   |        | Exactly one of **refArkivnotat** and **refJournalpost** is allowed. |
|           | ref       | refJournalpost         |    c     |             |            |  |      |  V   |   V   |      |  V   |        | Exactly one of **refArkivnotat** and **refJournalpost** is allowed. |

### Avskrivning ([Basic Archival Object](#basic-archival-object))
(Sign Off)

| Field no. | Type      | Field                    | Not null |   Default   |     Code list     |  | Save | View | Query | Sort | Link | Unlink | Comment |
|:----------|:----------|:-------------------------|:--------:|:-----------:|:-----------------:|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------|
| M619      | string    | avskrivningsmaate        |    V     |             | avskrivningsmaate |  |  V   |  V   |   V   |      |      |        |         |
|           |           |                          |          |             |                   |  |      |      |       |      |      |        |         |
|           | ref       | refJournalpost           |    V     |             |                   |  |      |  V   |   V   |      |  V   |        |         |
|           | ref       | refTilknyttetJournalpost |          |             |                   |  |      |      |   V   |      |  V   |   V    | &nbsp;  |


## Documents

### Dokument ([Finalizable Object](#finalizable-object-basic-archival-object))
(Document)

Note: **EFO** required if finalized
| Field no. | Type      | Field                         | Not null |   Default   | Code list                 |  | Save | View | Query | Sort | Link | Unlink | Comment                                           |
|:----------|:----------|:------------------------------|:--------:|:-----------:|:--------------------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:--------------------------------------------------|
| M083      | string    | dokumenttype                  |          |             | dokumenttype              |  |  V   |  V   |   V   |      |      |        |                                                   |
| M054      | string    | dokumentstatus                |    V     |      V      | dokumentstatus            |  |  V   |  V   |   V   |      |      |        |                                                   |
| M020      | string    | tittel                        |    V     |             |                           |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M021      | string    | beskrivelse                   |          |             |                           |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M024      | string    | forfatter                     |          |             |                           |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M300      | string    | dokumentmedium                |    V     |      V      | dokumentmedium            |  |  V   |  V   |   V   |      |      |        |                                                   |
| M217      | string    | tilknyttetRegistreringSom     |    V     |             | tilknyttetRegistreringSom |  |  V   |  V   |   V   |      |      |        |                                                   |
| M007      | number    | dokumentnummer                |    V     | Auto inc    |                           |  |  cw  |  V   |   V   |  V   |      |        |                                                   |
| M450      | string    | kassasjonsvedtak              |          |             | kassasjonsvedtak          |  |  V   |  V   |   V   |      |      |        |                                                   |
| M453      | string    | kassasjonshjemmel             |          |             | kassasjonshjemmel         |  |  V   |  V   |   V   |      |      |        |                                                   |
| M451      | number    | bevaringstid                  |          |             |                           |  |  V   |  V   |   V   |  V   |      |        |                                                   |
| M500      | string    | skjerming                     |          |             | skjerming                 |  |  V   |  V   |   V   |      |      |        |                                                   |
|           | timestamp | forventetKassasjonsDato       |          |             |                           |  |  sm  |  V   |   V   |  V   |      |        |                                                   |
| M630      | timestamp | kassertDato                   |          |             |                           |  |  sm  |  V   |   V   |  V   |      |        |                                                   |
| M631      | string    | kassertAv                     |          |             |                           |  |  sm  |  V   |   V   |  V   |      |        |                                                   |
|           | string    | kassertAvBrukerIdent          |          |             |                           |  |  sm  |  V   |   V   |  V   |      |        |                                                   |
|           |           |                               |          |             |                           |  |      |      |       |      |      |        |                                                   |
|           | object    | virksomhetsspesifikkeMetadata |          |             |                           |  |  V   |  V   |       |      |      |        |                                                   |
|           |           |                               |          |             |                           |  |      |      |       |      |      |        |                                                   |
|           | ref       | refRegistrering               |    V     |             |                           |  |      |  V   |   V   |      |  V   |   V    | <p>&bull; references **AbstraktRegistrering**</p> |
|           | ref       | refDokumentversjon            |          |             |                           |  |      |      |   V   |      |  V   |   V    |                                                   |
|           | ref       | refEksternId                  |          |             |                           |  |      |      |   V   |      |  V   |        |                                                   |
|           | ref       | refMerknad                    |          |             |                           |  |      |      |   V   |      |  V   |        | &nbsp;                                            |

### Dokumentversjon ([Basic Archival Object](#basic-archival-object))
(Document Version)

| Field no. | Type      | Field                  | Not null |   Default   | Code list     |  | Save | View | Query | Sort | Link | Unlink | Comment                                                            |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:--------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------|
| M005      | number    | versjonsnummer         |    V     | Auto inc    |               |  |  cw  |  V   |   V   |  V   |      |        |                                                                    |
| M700      | string    | variantformat          |    V     |             | variantformat |  |  V   |  V   |   V   |      |      |        |                                                                    |
| M701      | string    | format                 |    V     |             |               |  |  V   |  V   |   V   |  V   |      |        |                                                                    |
| M702      | string    | formatDetaljer         |          |             |               |  |  V   |  V   |   V   |  V   |      |        |                                                                    |
| M705      | string    | sjekksum               |    V     | Auto calc   |               |  |  sm  |  V   |   V   |      |      |        |                                                                    |
| M706      | string    | sjekksumAlgoritme      |    V     |             |               |  |  sm  |  V   |   V   |      |      |        |                                                                    |
| M707      | number    | filstoerrelse          |    V     | Auto calc   |               |  |  sm  |  V   |   V   |      |      |        |                                                                    |
|           | boolean   | kryptertDokument       |    V     | false       |               |  |  V   |  V   |   V   |  V   |      |        |                                                                    |
|           | string    | filnavn                |    V     |             |               |  |  sm  |  V   |   V   |      |      |        |                                                                    |
|           | string    | innholdstype           |    V     |             |               |  |  sm  |  V   |   V   |      |      |        |                                                                    |
| M218      | string    | referanseDokumentfil   |    V     |             |               |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; unique identifier of a document stored in the system</p> |
|           |           |                        |          |             |               |  |      |      |       |      |      |        |                                                                    |
|           | ref       | refDokument            |    V     |             |               |  |      |  V   |   V   |      |  V   |   V    |                                                                    |
|           | ref       | refEksternId           |          |             |               |  |      |      |   V   |      |  V   |        | &nbsp;                                                             |

## National Identifiers

### Adresse ([Basic Archival Object](#basic-archival-object))
(Address)

| Field no. | Type      | Field                  | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                        |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------|
| M021      | string    | beskrivelse            |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | adresseKnr             |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        | 4 digits exactly                                                                                                               |
|           | string    | postnr                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        | 4 digits exactly                                                                                                               |
|           | string    | poststed               |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | adressenavn            |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        | Not blank                                                                                                                      |
|           | string    | nummer                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | adressekode            |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only digits allowed                                                                                                            |
|           | string    | alternativAdresse      |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           |           |                        |          |             |           |  |      |      |       |      |      |        |                                                                                                                                |
|           | ref       | refMappe               |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p>        |
|           | ref       | refRegistrering        |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p> |

### Bygning ([Basic Archival Object](#basic-archival-object))
(Building)

| Field no. | Type      | Field                  | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                        |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------|
| M021      | string    | beskrivelse            |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | bygningsnummer         |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        | Only digits                                                                                                                    |
|           | string    | endringsloepenummer    |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        | Only digits allowed                                                                                                            |
|           | string    | bygningsStatus         |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | bygningsType           |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | bygningsendringsType   |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | koordinatsystem        |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | koordX                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only decimal numbers allowed                                                                                                   |
|           | string    | koordY                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only decimal numbers allowed                                                                                                   |
|           | string    | koordZ                 |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only decimal numbers allowed                                                                                                   |
|           | string    | etasjeNummer           |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only digits allowed                                                                                                            |
|           | string    | etasjeplan             |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | bygningsnavn           |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | bruksenhetLoepenr      |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only digits allowed                                                                                                            |
|           |           |                        |          |             |           |  |      |      |       |      |      |        |                                                                                                                                |
|           | ref       | refMappe               |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p>        |
|           | ref       | refRegistrering        |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p> |

### Eiendom ([Basic Archival Object](#basic-archival-object))
(Property)

| Field no. | Type      | Field                  | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                        |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------|
| M021      | string    | beskrivelse            |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | knr                    |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        | Not blank, Exactly 4 digits                                                                                                    |
|           | string    | gnr                    |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        | Not blank, Only digits allowed                                                                                                 |
|           | string    | bnr                    |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        | Not blank, Only digits allowed                                                                                                 |
|           | string    | fnr                    |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only digits allowed                                                                                                            |
|           | string    | snr                    |          |             |           |  |  V   |  V   |   V   |  V   |      |        | Only digits allowed                                                                                                            |
|           | string    | bruksnavn              |          |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           |           |                        |          |             |           |  |      |      |       |      |      |        |                                                                                                                                |
|           | ref       | refMappe               |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p>        |
|           | ref       | refRegistrering        |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p> |

### Plan ([Basic Archival Object](#basic-archival-object))
(Plan)

| Field no. | Type      | Field                  | Not null |   Default   | Code list    |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                        |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:-------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------|
| M021      | string    | beskrivelse            |          |             |              |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | planident              |    V     |             |              |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | planKommunenummer      |    c     |             |              |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; exactly one of **planKommunenummer**, **planLandskode**, or **planFylkesnummer** is required</p>                     |
|           | string    | planLandskode          |    c     |             |              |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; exactly one of **planKommunenummer**, **planLandskode**, or **planFylkesnummer** is required</p>                     |
|           | string    | planFylkesnummer       |    c     |             |              |  |  V   |  V   |   V   |  V   |      |        | <p>&bull; exactly one of **planKommunenummer**, **planLandskode**, or **planFylkesnummer** is required</p>                     |
|           | string    | plannavn               |          |             |              |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           | string    | plantype               |          |             | plantype     |  |  V   |  V   |   V   |      |      |        |                                                                                                                                |
|           | string    | planstatus             |          |             | planstatus   |  |  V   |  V   |   V   |      |      |        |                                                                                                                                |
|           | string    | lovreferanse           |          |             | lovreferanse |  |  V   |  V   |   V   |      |      |        |                                                                                                                                |
|           |           |                        |          |             |              |  |      |      |       |      |      |        |                                                                                                                                |
|           | ref       | refMappe               |    c     |             |              |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p>        |
|           | ref       | refRegistrering        |    c     |             |              |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p> |

### Presedens ([Finalizable Object](#finalizable-object-basic-archival-object))
(Precedent)

Note: **EFO** required if finalized

| Field no. | Type      | Field                          | Not null |   Default   | Code list        |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                                                                                                                                  |
|:----------|:----------|:-------------------------------|:--------:|:-----------:|:-----------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| M020      | string    | title                          |    V     |             |                  |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                          |
| M021      | string    | beskrivelse                    |          |             |                  |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                          |
| M111      | date      | presedensDato                  |    V     |             |                  |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                          |
| M628      | timestamp | presedensGodkjentDato          |    V     |      V      |                  |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                                                                                                          |
| M629      | string    | presedensGodkjentAv            |    V     |      V      |                  |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; if presedensGodkjentDato is null both presedensGodkjentAv and presedensGodkjentAvBrukerIdent must be null</p><p>&bull; if presedensGodkjentDato is *not* null both presedensGodkjentAv and presedensGodkjentAvBrukerIdent must be provided</p> |
|           | string    | presedensGodkjentAvBrukerIdent |    V     |      V      |                  |  |  cw  |  V   |   V   |  V   |      |        | <p>&bull; if presedensGodkjentDato is null both presedensGodkjentAv and presedensGodkjentAvBrukerIdent must be null</p><p>&bull; if presedensGodkjentDato is *not* null both presedensGodkjentAv and presedensGodkjentAvBrukerIdent must be provided</p> |
|           |           |                                |          |             |                  |  |      |      |       |      |      |        |                                                                                                                                                                                                                                                          |
| M056      | string    | presedensStatus                |    V     |      V      | presedensStatus  |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                          |
| M311      | string    | presedensHjemmel               |          |             | presedensHjemmel |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                          |
| M312      | string    | rettskildefaktor               |    V     |             | rettskildefaktor |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                                                                                                                          |
|           |           |                                |          |             |                  |  |      |      |       |      |      |        |                                                                                                                                                                                                                                                          |
|           | ref       | refSaksmappe                   |          |             |                  |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **Saksmappe**</p><p>                                                                                                                                                                                                                |
|           | ref       | refJournalpost                 |          |             |                  |  |      |      |   V   |      |  V   |   V    | <p>&bull; references **Journalpost**</p>                                                                                                                                                                                                                 |

## Other

### EksternId ([Basic Archival Object](#basic-archival-object))
(External ID)

| Field no. | Type      | Field                  | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                                                 |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|           | string    | eksterntSystem         |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                         |
|           | string    | eksternID              |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                                                         |
|           |           |                        |          |             |           |  |      |      |       |      |      |        |                                                                                                                                                                         |
|           | ref       | refMappe               |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refMappe**, **refRegistrering**, **refDokument**, or **refDokumentversjon** is required</p>        |
|           | ref       | refRegistrering        |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p><p>&bull; exactly one of **refMappe**, **refRegistrering**, **refDokument**, or **refDokumentversjon** is required</p> |
|           | ref       | refDokument            |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; exactly one of **refMappe**, **refRegistrering**, **refDokument**, or **refDokumentversjon** is required</p>                                                  |
|           | ref       | refDokumentversjon     |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; exactly one of **refMappe**, **refRegistrering**, **refDokument**, or **refDokumentversjon** is required</p>                                                  |

### Noekkelord ([Basic Archival Object](#basic-archival-object))
(Keyword)

| Field no. | Type      | Field                  | Not null |   Default   | Code list |  | Save | View | Query | Sort | Link | Unlink |Comment                                                                                                                         |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:----------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:-------------------------------------------------------------------------------------------------------------------------------|
| M022      | string    | verdi                  |    V     |             |           |  |  V   |  V   |   V   |  V   |      |        |                                                                                                                                |
|           |           |                        |          |             |           |  |      |      |       |      |      |        |                                                                                                                                |
|           | ref       | refMappe               |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p>        |
|           | ref       | refRegistrering        |    c     |             |           |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p><p>&bull; exactly one of **refMappe** and **refRegistrering** is required</p> |

### Merknad ([Basic Archival Object](#basic-archival-object))
(Note)

| Field no. | Type      | Field                  | Not null |   Default   | Code list    |  | Save | View | Query | Sort | Link | Unlink | Comment                                                                                                                                         |
|:----------|:----------|:-----------------------|:--------:|:-----------:|:-------------|:-|:----:|:----:|:-----:|:----:|:----:|:------:|:------------------------------------------------------------------------------------------------------------------------------------------------|
|   M310    | string    | tekst                  |    V     |             |              |  |  V   |  V   |   V   |   V  |      |        |                                                                                                                                                 |
|   M084    | string    | merknadstype           |    V     |             | merknadstype |  |  V   |  V   |   V   |      |      |        |                                                                                                                                                 |
|           |           |                        |          |             |              |  |      |      |       |      |      |        |                                                                                                                                                 |
|           | ref       | refMappe               |    c     |             |              |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktMappe**</p><p>&bull; exactly one of **refMappe**, **refDokument** and **refRegistrering** is required</p>        |
|           | ref       | refRegistrering        |    c     |             |              |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **AbstraktRegistrering**</p><p>&bull; exactly one of **refMappe**, **refDokument** and **refRegistrering** is required</p> |
|           | ref       | refDokument            |    c     |             |              |  |      |  V   |   V   |      |  V   |        | <p>&bull; references **Dokument**</p><p>&bull; exactly one of **refMappe**, **refDokument** and **refRegistrering** is required</p>             |
