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

## Examples

| Search for                                                 | Query                                      | Parameter value         | Comment                                        |
|:-----------------------------------------------------------|:-------------------------------------------|:------------------------|:-----------------------------------------------|
| **Saksmappe** with a given **id**                          | id = @id                                   | 100                     | 100 is the **id** of the **Saksmappe**         |
| **Saksmappe** with an exact **tittel**                     | tittel = @tittel                           | "Mappe"                 |                                                |
| **Saksmappe** with a **tittel** not equal to               | tittel != @tittel                          | "Mappe"                 |                                                |
| **Saksmappe** with a **tittel** that starts with           | tittel %= @tittel                          | "Map%"                  |                                                |
| **Saksmappe** with a **tittel** that ends with             | tittel %= @tittel                          | "%appe"                 |                                                |
| **Saksmappe** with a **tittel** that contains a string     | tittel %= @tittel                          | "%app%"                 |                                                |
| **Saksmappe** with a **saksaar** within a particular range | saksaar=[@start:@end]                      | <p>2015</p><p>2017</p>  | **Saksmapper** created in 2015, 2016, and 2017 |
| **Saksmappe** with a **saksaar** within a particular range | saksaar={@start:@end}                      | <p>2015</p><p>2017</p>  | **Saksmapper** created in 2016                 |
| **Saksmappe** with a **saksaar** within a particular range | saksaar=[@start:@end}                      | <p>2015</p><p>2017</p>  | Saksmapper created in 2015 and 2016            |
| **Saksmappe** with a **saksaar** within a particular range | saksaar={@start:@end]                      | <p>2015</p> <p>2017</p> | **Saksmapper** created in 2016 and 2017        |
| **Saksmappe** in a specific **Arkivdel**                   | refArkivdel.id = @refArkivdel              | 10                      | 10 is the **id** of the **Arkivdel**           |
| **Saksmappe** in a specific **Arkiv**                      | refArkivdel.refArkiv.id = @refArkiv        | 5                       | 5 is the **id** of the **Arkiv**               |
| **Saksmappe** in an **Arkiv** with a specific **tittel**   | refArkivdel.refArkiv.tittel = @arkivTittel | "Arkiv"                 | &nbsp;                                         |
