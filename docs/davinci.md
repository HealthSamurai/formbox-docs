---
description: Build Prior Authorization flows (DTR) with `$questionnaire-package`  operation support
---

# Da Vinci DTR

Formbox supports `$questionnaire-package` operation from the [Da Vinci Documentation Templates and Rules (DTR) Implementation Guide](https://build.fhir.org/ig/HL7/davinci-dtr/). 

## Overview

The `$questionnaire-package` operation retrieves a Questionnaire along with all of its dependencies (ValueSets) and a pre-populated draft QuestionnaireResponse, packaged together in a single Bundle. This is useful for Documentation Templates and Rules (DTR) applications that need to collect information for prior authorization or other payer requirements.

**Specification:** [OperationDefinition-questionnaire-package](https://build.fhir.org/ig/HL7/davinci-dtr/OperationDefinition-questionnaire-package.html)


## Current Limitations

The current implementation supports a subset of the full Da Vinci DTR specification:

- **Single Questionnaire flow only** - The `questionnaire` parameter is used to request a specific Questionnaire
- **No CQL Library support** - CQL libraries referenced by the Questionnaire are not included in the bundle
- **No `context` parameter** - The CRD context ID parameter is not yet supported
- **No `changedsince` parameter** - Incremental updates based on timestamp are not supported


## Endpoints

The operation is available at the following endpoints:

| Endpoint | Description |
|----------|-------------|
| `POST /fhir/Questionnaire/$questionnaire-package` | FHIR endpoint |
| `POST /Questionnaire/$questionnaire-package` | Aidbox-format endpoint |
| `POST /Organization/{id}/fhir/Questionnaire/$questionnaire-package` | Multi-tenant FHIR endpoint |
| `POST /Organization/{id}/aidbox/Questionnaire/$questionnaire-package` | Multi-tenant Aidbox-format endpoint |

## Input Parameters

The operation accepts a FHIR Parameters resource with the following parameters:

| Parameter | Type | Cardinality | Description |
|-----------|------|-------------|-------------|
| `questionnaire` | canonical | 1..1 | The canonical URL of the Questionnaire to retrieve (optionally with version, e.g., `http://example.org/Questionnaire/my-form\|1.0`) |
| `coverage` | Coverage | 0..1 | Coverage resource that establishes the member and coverage context. The `beneficiary` reference is used to set `QuestionnaireResponse.subject` |
| `order` | Resource | 0..1 | Order resource (e.g., ServiceRequest, MedicationRequest) that establishes the context for documentation collection |

### Launch Context

The `coverage` and `order` parameters are passed to the populate operation as launch context variables, making them available in FHIRPath expressions:

- `%coverage` - The Coverage resource
- `%order` - The order resource (ServiceRequest, MedicationRequest, etc.)

## Output Parameters

The operation returns a FHIR Parameters resource with:

| Parameter | Type | Cardinality | Description |
|-----------|------|-------------|-------------|
| `packagebundle` | Bundle | 1..1 | A collection Bundle containing the Questionnaire, expanded ValueSets, and a draft QuestionnaireResponse |
| `outcome` | OperationOutcome | 0..1 | Warnings or informational messages about the operation (e.g., ValueSets that couldn't be expanded) |

### Package Bundle Structure

The `packagebundle` is a FHIR Bundle of type `collection` with entries in the following order:

1. **Questionnaire** - The requested Questionnaire resource (first entry)
2. **ValueSets** - Pre-expanded ValueSets referenced by `answerValueSet` (middle entries)
3. **QuestionnaireResponse** - A draft (status: `in-progress`) QuestionnaireResponse (last entry)

### ValueSet Handling

- ValueSets are expanded using the `$expand` operation
- Only ValueSets with **40 or fewer concepts** are included in the bundle
- ValueSets with more than 40 concepts are excluded, and an informational message is added to the `outcome`
- If a ValueSet cannot be expanded, a warning is added to the `outcome`

## Examples

### Basic Request

Retrieve a questionnaire package by canonical URL:

```http
POST /fhir/Questionnaire/$questionnaire-package
content-type: text/yaml
accept: text/yaml

resourceType: Parameters
parameter:
- name: questionnaire
  valueCanonical: http://example.org/Questionnaire/prior-auth-form|1.0
```

### Request with Coverage

Include coverage to set the QuestionnaireResponse subject:

```http
POST /fhir/Questionnaire/$questionnaire-package
content-type: text/yaml
accept: text/yaml

resourceType: Parameters
parameter:
- name: questionnaire
  valueCanonical: http://example.org/Questionnaire/prior-auth-form|1.0
- name: coverage
  resource:
    resourceType: Coverage
    id: coverage-123
    status: active
    beneficiary:
      reference: Patient/patient-456
    payor:
    - reference: Organization/payer-789
```

### Request with Coverage and Order

Include both coverage and order for full context in population expressions:

```http
POST /fhir/Questionnaire/$questionnaire-package
content-type: text/yaml
accept: text/yaml

resourceType: Parameters
parameter:
- name: questionnaire
  valueCanonical: http://example.org/Questionnaire/prior-auth-form|1.0
- name: coverage
  resource:
    resourceType: Coverage
    id: coverage-123
    status: active
    beneficiary:
      reference: Patient/patient-456
- name: order
  resource:
    resourceType: ServiceRequest
    id: order-789
    status: active
    intent: order
    code:
      coding:
      - system: http://example.org/procedures
        code: '12345'
        display: Medical Procedure
    subject:
      reference: Patient/patient-456
```

### Successful Response

```yaml
resourceType: Parameters
parameter:
- name: packagebundle
  resource:
    resourceType: Bundle
    type: collection
    entry:
    - resource:
        resourceType: Questionnaire
        id: prior-auth-form
        url: http://example.org/Questionnaire/prior-auth-form
        version: '1.0'
        status: active
        item:
        - linkId: diagnosis
          type: choice
          text: Primary Diagnosis
          answerValueSet: http://example.org/ValueSet/diagnosis-codes
    - resource:
        resourceType: ValueSet
        url: http://example.org/ValueSet/diagnosis-codes
        expansion:
          contains:
          - system: http://hl7.org/fhir/sid/icd-10
            code: J06.9
            display: Acute upper respiratory infection
    - resource:
        resourceType: QuestionnaireResponse
        questionnaire: http://example.org/Questionnaire/prior-auth-form|1.0
        status: in-progress
        subject:
          reference: Patient/patient-456
        item:
        - linkId: diagnosis
          text: Primary Diagnosis
```

### Response with Warnings

When some ValueSets cannot be included:

```yaml
resourceType: Parameters
parameter:
- name: packagebundle
  resource:
    resourceType: Bundle
    type: collection
    entry:
    - resource:
        resourceType: Questionnaire
        id: prior-auth-form
    - resource:
        resourceType: QuestionnaireResponse
        questionnaire: http://example.org/Questionnaire/prior-auth-form|1.0
        status: in-progress
- name: outcome
  resource:
    resourceType: OperationOutcome
    issue:
    - severity: information
      code: too-costly
      diagnostics: 'ValueSet too large to embed (>40 concepts): http://example.org/ValueSet/large-codes'
    - severity: warning
      code: not-found
      diagnostics: 'ValueSet expansion failed: http://example.org/ValueSet/unknown-codes'
```

### Error Response - Missing Parameter

```yaml
resourceType: OperationOutcome
issue:
- severity: error
  code: required
  diagnostics: "Missing required parameter: 'questionnaire'"
```

### Error Response - Questionnaire Not Found

```yaml
resourceType: OperationOutcome
issue:
- severity: error
  code: not-found
  diagnostics: 'Questionnaire not found: http://example.org/Questionnaire/unknown'
```

## Using Population Expressions

Questionnaires can include population expressions that reference the launch context. For example:

```yaml
resourceType: Questionnaire
url: http://example.org/Questionnaire/with-population
item:
- linkId: coverage-id
  type: string
  text: Coverage ID
  extension:
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-initialExpression
    valueExpression:
      language: text/fhirpath
      expression: '%coverage.id'
- linkId: order-status
  type: string
  text: Order Status
  extension:
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-initialExpression
    valueExpression:
      language: text/fhirpath
      expression: '%order.status'
- linkId: patient-name
  type: string
  text: Patient Name
  extension:
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-initialExpression
    valueExpression:
      language: text/fhirpath
      expression: '%subject.name.first().given.first()'
```

When this questionnaire is requested with coverage and order parameters, the QuestionnaireResponse will have pre-populated answers:

```yaml
resourceType: QuestionnaireResponse
status: in-progress
subject:
  reference: Patient/patient-456
item:
- linkId: coverage-id
  answer:
  - valueString: coverage-123
- linkId: order-status
  answer:
  - valueString: active
- linkId: patient-name
  answer:
  - valueString: John
```


## Using an external DTR service

Payers such as MCG and InterQual run the DTR operations themselves and own the form library behind them. Point Formbox at one of them through `dtr-service` in the **default** `SDCConfig`, and both `$questionnaire-package` and `$next-question` are forwarded to that service:

```yaml
POST /SDCConfig
content-type: text/yaml

id: cfg-1
resourceType: SDCConfig
name: Config with an external DTR service
default: true
dtr-service:
    endpoint: 'https://dtr.payer.example'
    auth:
        token-endpoint: 'https://dtr.payer.example/oauth2/token'
        client-id: 'formbox'
        client-secret: 'ILUcZS3T...'
```

* `endpoint` - base URL of the service; the operations are called at `<endpoint>/Questionnaire/$questionnaire-package` and `<endpoint>/Questionnaire/$next-question`
* `headers` (optional) - static headers sent with every call
* `auth` (optional) - OAuth2 `client_credentials` credentials, described in [Authentication](aidbox-ui-builder-alpha/external-fhir-servers-as-a-data-backend.md#authentication) together with the places the client secret can live

With `dtr-service` set, nothing runs locally for these operations: Formbox does not look the Questionnaire up, does not expand ValueSets and does not populate a draft QuestionnaireResponse — the service owns all of that. Without it, the built-in implementation described above answers as before.

For `$next-question` a form can still name a service of its own: the `questionnaireAdaptive` extension of the embedded Questionnaire wins over `dtr-service`. See [Adaptive forms](adaptive-forms.md).

The caller's own credentials are never forwarded to the service, and the status and body the service answers with reach the caller unchanged.

### Multitenancy

Every tenant has its own default `SDCConfig`, so each organization reaches its own DTR service with its own credentials. A request made without an organization (`/fhir/Questionnaire/$questionnaire-package`) uses the configuration that belongs to no organization, never a tenant's one.

## Related Resources

- [Da Vinci DTR Implementation Guide](https://build.fhir.org/ig/HL7/davinci-dtr/)
- [SDC $populate Operation](./reference/fhir-sdc-api)
- [FHIR Questionnaire Resource](https://www.hl7.org/fhir/questionnaire.html)
