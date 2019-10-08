Query language
-------

## Overview

The web services support a simple but powerful query language that allows one to query for objects of a particular type, specifying **criteria** on the **object fields** as well as **criteria on the fields of linked objects**. Note that a *linked object* may be linked directly or indirectly (via another object).

## Fields

The metadata model exposed by the web services defines three groups of fields (special, regular, and reference fields) and specifies which fields can be queried. The following table shows examples of how you can use fields when querying for objects:

| Query for                                               | What field to use           | Comment                                                                                                         |
|:--------------------------------------------------------|:----------------------------|:----------------------------------------------------------------------------------------------------------------|
| **Mappe** with a specific **id**                        | id                          | Only exact matching is supported for special fields.                                                            |
| **Mappe** with a specific **tittel**                    | tittel                      | Different types of criteria can be specified on regular fields.                                                 |
| **Mappe** in a specific **Arkivdel**                    | refArkivdel                 | The **id** of a linked object is the only type of criterion one can specify on a reference field.               |
| **Mappe** in an **Arkivdel** with a specific **tittel** | refArkivdel.tittel          | Chaining via reference fields allows us to specify criteria on regular fields in linked objects.                |
| **Mappe** in an **Arkiv** with a specific **tittel**    | refArkivdel.refArkiv.tittel | More that one level of reference field chaining is allowed. Note that systems may decide to define a threshold. |

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

## Advanced filtering

Filtering by fields of direct or indirect references can be achieved in three ways:

1. Reference chaining:
```
refArkivdel.refArkiv.tittel=@param1 && refPrimaerKlasse.tittel=@param2
```

2. Joins:
```
Joins:
    #series --> refArkivdel
    #fonds --> #series.refArkiv
    #pKlasse --> refPrimaerKlasse
Query:
    #fonds.tittel=@param1 && #pKlasse.tittel=@param2
```

3. A mix between joins and reference chains
```
Joins:
    #series --> refArkivdel
    #fonds --> #series.refArkiv
Query:
    #fonds.tittel=@param1 && refPrimaerKlasse.tittel=@param2
```

All of the examples above will serve the same purpose, but with varying degrees of flexibility and complexity.
* The first (reference chaining) should be the choice for most integrations;
* The second (joins) should be used when reference chaining cannot serve the business requirements (see more in the Joins section)
* Despite being allowed, the third (mixed) should be used rarely to avoid the complexity introduced by it

### Reference chaining

Reference chaining is simple, easy to read, and will satisfy the business requirements of most applications. As such, it is the recommended approach when filtering by reference fields.

A reference chain, regardless of how many times it occurs in a query, is always expected to denote a **single path** to the specified reference. The closest example would be the equivalent of a single JOIN statement in SQL.

In the following example:
```
refSekundaerKlasse.klasseIdent=@klasseIdent && refSekundaerKlasse.tittel=@tittel
```

_refSekundaerKlasse_ is expected to refer to a single path to the secondary _Klasse_.

As a result, only entities which are directly linked to a secondary _Klasse_ with the specified _klasseIdent_ **and** _tittel_ will be returned.

### Joins

Joins give greater flexibility when filtering by direct or indirect fields of references by introducing another level of complexity. Joins behave similarly to their SQL counterpart.

A join definition consists of:

* **alias** - a unique identifier for the join definition
  - the alias must always begins with a '_#_' (hash)
  - the alias may be any combination of small letters, capital letters, and digits
* **reference** - the field on which to base the join
  - the reference represents a reference field as defined in _02 - Object types.md_, optionally prefixed by another alias followed by a '.' (dot)
  - for example: both _refArkivdel_ and _#mappe.refArkivdel_ are valid _references_ as long as the _#mappe_ alias is also defined

#### Joins example

If business requirements dictate that a _Mappe_ linked to two secondary classes must be found, one would be inclined to write the following _incorrect_ query using reference chains:
```
refSekundaerKlasse.klasseIdent=@klasseIdent1 && refSekundaerKlasse.klasseIdent=@klasseIdent2
```

Since _refSekundaerKlasse_ denotes a **single path** to the target reference, however, the query above will never return any results, because a _Klasse_ cannot have two distinct _klasseIdent_ at the same time. The two expressions are mutually-exclusive.

To fulfill the business requirements, the query will have to be rewritten to:
```
Joins:
    #klasse1 --> refSekundaerKlasse
    #klasse2 --> refSekundaerKlasse
Query:
    #klasse1.klasseIdent=@klasseIdent1 && #klasse2.klasseIdent=@klasseIdent2
```

, where:

* #klasse1 is an alias that denotes **one path** to the _Klasse_ reference via _refSekundaerKlasse_
* #klasse2 is an alias that denotes a **different path** to the _Klasse_ reference again via _refSekundaerKlasse_

, making the fulfillment of the business requirements possible.

## Examples

| Search for                                                                  | Query                                      | Joins                                                             | Parameter value         | Comment                                        |
|:----------------------------------------------------------------------------|:-------------------------------------------|:------------------------------------------------------------------|:------------------------|:-----------------------------------------------|
| **Saksmappe** with a given **id**                                           | id = @id                                   | -                                                                 | 100                     | 100 is the **id** of the **Saksmappe**         |
| **Saksmappe** with an exact **tittel**                                      | tittel = @tittel                           | -                                                                 | "Mappe"                 |                                                |
| **Saksmappe** with a **tittel** not equal to                                | tittel != @tittel                          | -                                                                 | "Mappe"                 |                                                |
| **Saksmappe** with a **tittel** that starts with                            | tittel %= @tittel                          | -                                                                 | "Map%"                  |                                                |
| **Saksmappe** with a **tittel** that ends with                              | tittel %= @tittel                          | -                                                                 | "%appe"                 |                                                |
| **Saksmappe** with a **tittel** that contains a string                      | tittel %= @tittel                          | -                                                                 | "%app%"                 |                                                |
| **Saksmappe** with a **saksaar** within a particular range                  | saksaar=[@start:@end]                      | -                                                                 | <p>2015</p><p>2017</p>  | **Saksmapper** created in 2015, 2016, and 2017 |
| **Saksmappe** with a **saksaar** within a particular range                  | saksaar={@start:@end}                      | -                                                                 | <p>2015</p><p>2017</p>  | **Saksmapper** created in 2016                 |
| **Saksmappe** with a **saksaar** within a particular range                  | saksaar=[@start:@end}                      | -                                                                 | <p>2015</p><p>2017</p>  | Saksmapper created in 2015 and 2016            |
| **Saksmappe** with a **saksaar** within a particular range                  | saksaar={@start:@end]                      | -                                                                 | <p>2015</p> <p>2017</p> | **Saksmapper** created in 2016 and 2017        |
| **Saksmappe** in a specific **Arkivdel** (reference chains)                 | refArkivdel.id = @refArkivdel              | -                                                                 | 10                      | 10 is the **id** of the **Arkivdel**           |
| **Saksmappe** in a specific **Arkiv** (reference chains)                    | refArkivdel.refArkiv.id = @refArkiv        | -                                                                 | 5                       | 5 is the **id** of the **Arkiv**               |
| **Saksmappe** in an **Arkiv** with a specific **tittel** (reference chains) | refArkivdel.refArkiv.tittel = @arkivTittel | -                                                                 | "Arkiv"                 |                                                |
| **Saksmappe** in a specific **Arkivdel** (joins)                            | #arkivdel.id = @refArkivdel                | #arkivdel --> refArkivdel                                         | 10                      | 10 is the **id** of the **Arkivdel**           |
| **Saksmappe** in a specific **Arkiv** (joins)                               | #arkiv.id = @refArkiv                      | 1. #arkivdel --> refArkivdel<br/>2. #arkiv --> #arkivdel.refArkiv | 5                       | 5 is the **id** of the **Arkiv**               |
| **Saksmappe** in an **Arkiv** with a specific **tittel** (joins)            | #arkiv.tittel = @arkivTittel               | 1. #arkivdel --> refArkivdel<br/>2. #arkiv --> #arkivdel.refArkiv | "Arkiv"                 | &nbsp;                                         |
