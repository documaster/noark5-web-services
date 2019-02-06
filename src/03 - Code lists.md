Code lists
-------

## Overview

Several fields in the Noark 5 metamodel can only contain one of a predefined set of values, i.e. a value from a *code list*.

This section defines a set of standard code lists that every Noark 5 system must support, as well as code lists that are specific to Documaster. Systems are allowed to add new values to any code list, and to add new code lists. However, systems are not allowed to change the meaning of any of the specified codes, as that would have a strong negative impact on interoperability. For the same reason, it is discouraged to remove any of the specified codes, but this is allowed if the system does not support that code (or any system-specific equivalent). Note that code lists for fields that are required (not null) must have at least one entry.

Use the **code-lists** web service to check if the values that a particular field can take are restricted by a code list.

## Noark 5 code lists

### arkivstatus (M050)

| Code | Name      | Comment |
|:-----|:----------|:--------|
| O    | Opprettet |         |
| A    | Avsluttet | &nbsp;  |

### arkivdelstatus (M051)

| Code | Name                | Comment |
|:-----|:--------------------|:--------|
| A    | Aktiv periode       |         |
| O    | Overlappingsperiode |         |
| P    | Avsluttet periode   |         |
| U    | Uaktuelle mapper    | &nbsp;  |

### mappetype (NOARK 5 tjenestegrensesnitt Versjon 1.0 beta, 7.2.2.19)

| Code | Name | Comment |
|:-----|:-----|:--------|
|      |      | &nbsp;  |

No default values are present in this list and must be configured in the system before use.

### saksstatus (M052)

| Code | Name                       | Comment |
|:-----|:---------------------------|:--------|
| B    | Under behandling           |         |
| A    | Avsluttet                  |         |
| U    | Utgår                      |         |
| R    | Opprettet av saksbehandler |         |
| S    | Avsluttet av saksbehandler |         |
| P    | Unntatt prosesstyring      |         |
| F    | Ferdig fra saksbehandler   | &nbsp;  |

### journalstatus (M053)

| Code | Name                                            | Comment |
|:-----|:------------------------------------------------|:--------|
| J    | Journalført                                     |         |
| F    | Ferdigstilt fra saksbehandler                   |         |
| G    | Godkjent av leder                               |         |
| E    | Ekspedert                                       |         |
| A    | Arkivert                                        |         |
| U    | Utgår                                           |         |
| M    | Midlertidig registrering av innkommet dokument  |         |
| S    | Saksbehandler har registrert innkommet dokument |         |
| R    | Reservert dokument                              | &nbsp;  |

### moeteregistreringstype (M085)

| Code | Name                                                                        | Comment |
|:-----|:----------------------------------------------------------------------------|:--------|
| D    | Vedtak i møtesak                                                            |         |
| VL   | Vedlegg til møtesak                                                         |         |
| SL   | Saksliste                                                                   |         |
| M    | Møtereferat                                                                 |         |
| B    | Møtebok                                                                     |         |
| N    | Notat til politisk utvalg                                                   |         |
| MI   | Møteinnkalling                                                              |         |
| O    | Oversikt over deltakere, representanter og medlemmer i utvalg, råd og møter |         |
| MP   | Møteprotokoll                                                               |         |
| SP   | Saksprotokoll                                                               |         |
| SF   | Saksframlegg                                                                | &nbsp;  |

### moetesakstype (M088)

| Code | Name             | Comment |
|:-----|:-----------------|:--------|
| PS   | Politisk sak     |         |
| RS   | Referatsak       |         |
| U    | Uregistrert sak  |         |
| DS   | Delegert møtesak |         |
| FO   | Interpellasjon   | &nbsp;  |


### moeteregistreringsstatus (M055)

| Code | Name | Comment |
|:-----|:-----|:--------|
|      |      | &nbsp;  |

No default values are present in this list and must be configured in the system before use.


### dokumentstatus (M054)

| Code | Name                           | Comment |
|:-----|:-------------------------------|:--------|
| B    | Dokumentet er under redigering |         |
| F    | Dokumentet er ferdigstilt      | &nbsp;  |

### dokumentmedium (M300)

| Code | Name              | Comment |
|:-----|:------------------|:--------|
| E    | Elektronisk arkiv | &nbsp;  |

### dokumenttype (M083)

| Code | Name | Comment |
|:-----|:-----|:--------|
|      |      | &nbsp;  |

No default values are present in this list and must be configured in the system before use.

### korrespondanseparttype (M087)

| Code | Name                | Comment |
|:-----|:--------------------|:--------|
| EA   | Avsender            |         |
| EM   | Mottaker            |         |
| EK   | Kopimottaker        |         |
| GM   | Gruppemottaker      |         |
| IA   | Intern avsender     |         |
| IM   | Intern mottaker     |         |
| IK   | Intern kopimottaker | &nbsp;  |

### flytStatus (M663)

| Code | Name                                            | Comment |
|:-----|:------------------------------------------------|:--------|
| G    | Godkjent                                        |         |
| I    | Ikke godkjent                                   |         |
| S    | Sendt tilbake til saksbehandler med kommentarer | &nbsp;  |

### tilknyttetRegistreringSom (M217)

| Code | Name          | Comment |
|:-----|:--------------|:--------|
| H    | Hoveddokument |         |
| V    | Vedlegg       | &nbsp;  |

### journalposttype (M082)

| Code | Name                                  | Comment |
|:-----|:--------------------------------------|:--------|
| I    | Inngående dokument                    |         |
| U    | Utgående dokument                     |         |
| N    | Organinternt dokument for oppfølging  |         |
| X    | Organinternt dokument uten oppfølging |         |
| S    | Saksframlegg                          | &nbsp;  |

### variantformat (M700)

| Code | Name              | Comment |
|:-----|:------------------|:--------|
| P    | Produksjonsformat |         |
| A    | Arkivformat       | &nbsp;  |

### skjerming (M500)

| Code | Name | Authority | Comment |
|:-----|:-----|:----------|:--------|
|      |      |           | &nbsp;  |

No default values are present in this list and must be configured in the system before use.

### avskrivningsmaate (M619)

| Code | Name                  | Comment |
|:-----|:----------------------|:--------|
| BU   | Besvart med brev      |         |
| BE   | Besvart med e-post    |         |
| TLF  | Besvart på telefon    |         |
| TE   | Tatt til etterretning |         |
| TO   | Tatt til orientering  | &nbsp;  |

## Documaster-specific code lists

### administrativEnhet (M583)

| Code | Name | Comment |
|:-----|:-----|:--------|
|      |      | &nbsp;  |

No default values are present in this list and must be configured in the system before use.

### ekspederingskanal

| Code | Name | Comment |
|:-----|:-----|:--------|
|      |      | &nbsp;  |

No default values are present in this list and must be configured in the system before use.
