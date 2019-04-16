Documaster Noark 5 Web Services
----------------------

### Overview

Noark is a Norwegian national standard for electronic records management and archiving which began its history in 1984. The latest major version of the standard, Noark 5, is based on the EU standard MoReq. Even though there are many similarities between the two, Noark 5 is more pragmatic than MoReq in certain areas where MoReq follows core archival principles more strictly.

This repository documents the Documaster Noark 5 web services.

### Guiding principles

We adhere to the following principles while developing our Noark 5 web services:

- Support the development of various types of integrations with Documaster
- Hide much of the complexity of records management and archiving (and the Noark 5 standard in particular) from the developers using the services
- Add new services when new requirements are available
- Improve the services constantly and always create a new major version of the services when it is impossible to maintain backwards compatibility
- Gradually deprecate older versions of the services
- Keep authentication and authorization out of this specification

### Git tags

We need to correlate all releases of this documentation with the corresponding releases of Documaster RMS (Records Management System). To do this, we will tag each documentation release with the applicable RMS version. For example, if a documentation release is tagged with `RMS 2.9.0`, that would mean it describes the web services available in Documaster RMS version 2.9.0. Sometimes we will need to improve the documentation without a new version of Documaster RMS having been released. In such cases we will use tags such as `2.9.0-1`, `2.9.0-2`, and so on for each new documentation release.
