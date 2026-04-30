---
description: Connect Formbox to an EHR via SMART on FHIR so it can launch from the chart, use patient context, and keep the default UI behavior aligned with the SMART launch state.
---

# EHR integration via SMART on FHIR

Formbox can be launched from an external EHR as a SMART on FHIR app. In this mode, the form opens in the EHR context and has access to the current patient and encounter.

In the current implementation, `Questionnaire` and `QuestionnaireResponse` are stored in Formbox, while other patient data stays in the EHR.

## When to use it

Use this integration when you want:

- to open Formbox from an EHR chart or patient workspace
- to prefill forms from the current patient context

## SMART launch flow

The browser entrypoint for a SMART EHR launch is:

```text
https://your-aidbox-domain/ui/sdc/smart/launch?iss=<...>&client-id=d414f88f-232f-4a25-bb22-025e8172ea94&launch=<...>
```

The callback URL uses the same host and path prefix:

```text
https://your-aidbox-domain/ui/sdc/smart/callback?code=<...>&state=<...>
```

The EHR opens this page in the user's browser. Formbox reads the SMART launch context here and starts the OAuth exchange.

The launch URL carries these query parameters:

- `iss` - the EHR FHIR base URL. Required.
- `client-id` - the SMART client id registered in the EHR. Required.
- `launch` - the EHR launch token. Passed through to the OAuth request.

### `launch`

`launch` is the user-facing entrypoint opened by the EHR. It receives the SMART launch parameters, starts the authorization flow, and keeps the browser in the same session while login completes.

### `callback`

`callback` is the OAuth redirect target. After login, the authorization server returns here with the authorization result, typically as `code` or `error` query parameters. Formbox finishes the token exchange and restores the SMART session.

In short:

- `launch` is opened by the EHR
- `callback` is used by the authorization server after login

## Connection setup

Related setup docs:

- `TokenIntrospector` - [Aidbox TokenIntrospector docs](https://www.health-samurai.io/docs/aidbox/access-control/authentication/token-introspector)
- `SDCSmartConnect` - [SMART connection](#smart-connection)
- `SDCConfig` - [Configuration](configuration.md)
- `AccessPolicy` - [Access control in forms](../access-control-in-forms.md) and [Aidbox Access Policies](https://www.health-samurai.io/docs/aidbox/access-control/authorization/access-policies)

The minimal setup is:

1. Register the app in the EHR developer portal.
1. Configure a SMART connection resource in Formbox.
1. Point the default `SDCConfig` to that SMART connection.
1. Allow the SMART client to access the required SDC routes.

### 1. Token validation

If the EHR issues opaque access tokens, configure a `TokenIntrospector` with `type: opaque` and an introspection endpoint.

```yaml
resourceType: TokenIntrospector
id: ehr-opaque
type: opaque
cache_ttl: 300
introspection_endpoint:
  url: https://ehr.example.com/tokeninfo
```

If the EHR issues JWT access tokens, configure a `TokenIntrospector` with `type: jwt` and either a shared secret, local keys, or `jwks_uri`.

```yaml
resourceType: TokenIntrospector
id: ehr-jwt
type: jwt
jwt:
  iss: https://ehr.example.com
jwks_uri: https://ehr.example.com/.well-known/jwks.json
```

### 2. SMART connection

Use `SDCSmartConnect` to describe the EHR connection:

```yaml
resourceType: SDCSmartConnect
id: ehr-smart
name: ehr-smart
client-id: <client-id>
fhir-url: https://ehr.example.com/fhir
auth:
  reference: TokenIntrospector/ehr-opaque
scopes:
  - openid
  - fhirUser
  - launch
  - offline_access
  - user/Patient.rs
```

### 3. Default SDC config

Link the SMART connection from the default `SDCConfig`:

```yaml
resourceType: SDCConfig
name: ehr-smart-config
default: true
smart-connect:
  reference: SDCSmartConnect/ehr-smart
```

### 4. Access policy

The SMART client must be allowed to reach the Formbox endpoints used by the app.
The `client_id` in the policy must match the `client-id` from `SDCSmartConnect`.

```yaml
resourceType: AccessPolicy
id: sdc-smart-provider-policy
engine: matcho
matcho:
  token:
    active: true
    client_id: <client-id>
  uri:
    $one-of:
      - '#^/sdc/.*$'
      - '#^/\$sdc-config$'
      - '#^/\$sdc-resource-types$'
      - '#^/\$sdc-resource-schema$'
      - '#^/\$sdc-file(/.*)?$'
      - '#^/\$extend-sdc-session$'
      - '#^/\$drop-sdc-session$'
      - '#^/QuestionnaireTheme(/.*)?$'
      - '#^/QuestionnaireResponse/[^/]+/\$[a-z-]+$'
      - '#^/fhir/Questionnaire(/[^/]+)?(/\$[a-z-]+)?/?$'
      - '#^/fhir/QuestionnaireResponse(/\$[a-z-]+)?/?$'
  request-method:
    $one-of: [get, post, put, patch, delete]
```

### Sandbox note

EHR sandboxes may require whitelisting or a support request before SMART launch is available in the provider UI.

## UI behavior in SMART mode

The SMART launch changes the default UI in a few places:

- On **Forms / Responses / Library**, a patient header appears in the upper-left corner with the patient name and birth date.
- If patient lookup fails, the header falls back to `Patient/<id>`.
- **Responses** is filtered to the current patient only.
- The **Run** button on **Forms** appears only in SMART mode.
- Clicking **Run** populates the form with patient data and opens the created `QuestionnaireResponse`.
- In **Share** and **Send**, `Subject` and `Encounter` come from the SMART context and are read-only.

## Populate and context parameters

`Questionnaire/$populatelink` and `Questionnaire/$populate` accept a `Parameters` resource. In SMART mode, Formbox passes patient context as EHR-side identifiers instead of internal FHIR references.

- `subject` and `encounter` use the EHR identity namespace
- `patient` is passed as a context parameter and is available to populate expressions

In a regular Formbox session, the same fields are resolved as internal FHIR references.

### Populate examples

Non-SMART `Parameters` request:

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "subject",
      "valueReference": {
        "reference": "Patient/pt1"
      }
    },
    {
      "name": "encounter",
      "valueReference": {
        "reference": "Encounter/enc1"
      }
    }
  ]
}
```

SMART `Parameters` request:

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "subject",
      "valueReference": {
        "identifier": {
          "system": "https://ehr.example.com/fhir",
          "value": "pt1"
        }
      }
    },
    {
      "name": "encounter",
      "valueReference": {
        "identifier": {
          "system": "https://ehr.example.com/fhir",
          "value": "enc1"
        }
      }
    },
    {
      "name": "context",
      "part": [
        {
          "name": "name",
          "valueString": "patient"
        },
        {
          "name": "content",
          "valueReference": {
            "identifier": {
              "system": "https://ehr.example.com/fhir",
              "value": "pt1"
            }
          }
        }
      ]
    }
  ]
}
```

Use `identifier` in SMART mode so Formbox keeps EHR identities separate from internal FHIR references.

### Where the identifier comes from

In SMART mode, Formbox builds the identifier from the launch context provided by the EHR:

- `identifier.system` is the EHR FHIR server base URL
- `identifier.value` is the patient or encounter id from the SMART launch context

As a result, a patient reference is represented as "this patient on this EHR FHIR server", not as an internal Aidbox `Patient/{id}` reference.
