Introduction
-------

## Goals

The goals of this specification are to:

- Define a minimal set of web services that cover the most popular requirements for integrating with Noark 5 systems
- Take into account the fact that the target users of the web services are developers and that they cannot be expected to know all intricacies of the Noark 5 standard
- Allow simple web services clients to be written in various languages to help developers write robust integrations quickly
- Not attempt to standardize things that cannot be standardized by nature

The specification is based on:

- The [Noark 5 version 4 standard](https://www.arkivverket.no/forvaltning-og-utvikling/noark-standarden/noark-5/noark5-standarden) and the [Noark 5 extraction schemas](https://www.arkivverket.no/forvaltning-og-utvikling/noark-standarden/noark-5/xml-skjemaer-for-deponering-og-avlevering-fra-noark-5).
- The [official attempt at defining standard Noark 5 web services](https://www.arkivverket.no/forvaltning-og-utvikling/noark-standarden/noark-5/tjenestegrensesnitt-noark5)

## Guiding principles

- Design a Noark 5 web services data model that is easy to understand and hides some of the complexities of Noark 5
- Allow the metadata model to be updated in a backwards-compatible way:
  - Add new object types
  - Add new fields
- Standardize the API for storing and retrieving business-specific metadata (virksomhetsspesifikke metadata)
- Leave authentication and authorization out of the specification
- Allow integration code to be reused while keeping in mind that systems are different and integration code may not work with a new system perfectly without minor modifications
- Allow all Noark 5 vendors to implement these web services
