---
description: Connect Formbox to external FHIR terminology servers for ValueSet expansion and code validation.
---

# Integration with external terminology servers

## Overview

Form Builder supports external terminology servers for `choice` and `open-choice` questionnaire items. This allows form authors to fetch answer options from a remote terminology server by enabling **Use external terminology server** in item properties.

By default, Form Builder includes three built-in terminology servers, but you can now configure additional terminology servers in `SDCConfig` and define a default server for Form Builder. Configured servers are added to the built-in list and become available in the **Terminology Server** dropdown when external terminology servers are used. In addition to that, a server can be marked as default: in this case, URL will be prefilled in the **External terminology server** field.

## Use Case Example

This feature is useful when:

- your organization uses its own terminology server
- you want form authors to reuse the same approved server across forms
- you want to limit available servers to the ones relevant for your environment
- you want a preferred terminology server to be selected by default

This functionality applies to **Choice** and **Open Choice** items.

## How to Configure in Form Builder

In Form Builder:

1. Create a `choice` or `open-choice` item
2. Go to `Item properties`→ `Options`
3. Choose `ValueSet`
4. Enable `Use external terminology server`

Two fields will become available:

- `Terminology Server` — the terminology server URL
- `ValueSet` — the ValueSet URL used to populate the dropdown options

The list shown in **Terminology Server** contains:

- 3 built-in terminology servers
- additional servers configured in `SDCConfig`

## Prerequisites

To configure custom terminology servers, add them to the `builder.external-terminology-servers` section in `SDCConfig`.

Each server entry supports:

- `url` — terminology server base URL
- `name` — display name in the dropdown
- `default` — whether the server should be treated as default

## Step-by-Step Configuration

### Add custom terminology servers

Example:

```yaml
builder:
  external-terminology-servers:
    - url: https://tx.fhirlab.net/fhir
      name: FHIRLab Term Server
      default: true
    - url: https://tx.hl7europe.eu/r4/
      name: HL7 Europe Terminology Server
      default: false
```

### Configuration behavior

- The three built-in servers are always shown by default.
- Servers configured in `builder.external-terminology-servers` are appended to the built-in list.
- Servers marked with `default: true` are sorted first in the dropdown.
- If a default server is present in the list, its URL is prefilled in the `External terminology server` field.
- 

## Example Configuration Used for QA

```json
"external-terminology-servers": [
  {
    "url": "https://tx.fhirlab.net/fhir",
    "name": "FHIRLab Term Server",
    "default": true
  },
  {
    "url": "https://tx.hl7europe.eu/r4/",
    "name": "HL7 Europe Terminology Server",
    "default": false
  },
  {
    "url": "https://termx.kodality.dev/api/fhir",
    "name": "TermX (Kodality)",
    "default": false
  },
  {
    "url": "https://snowstorm.termx.org/fhir",
    "name": "Snowstorm (TermX)",
    "default": false
  },
  {
    "url": "https://echidna.fhir.org/r5/",
    "name": "Echidna (OHDSI / OMOP vocabularies)",
    "default": false
  },
  {
    "url": "https://tx.fhir.ch/r4/",
    "name": "tx.fhir.ch (R4)",
    "default": false
  }
]
```
