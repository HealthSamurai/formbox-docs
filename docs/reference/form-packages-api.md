---
description: Define, launch, share, submit, and amend ordered questionnaire packages through the Formbox API.
---

# Form packages API

Formbox provides custom operations for running a [form package](../form-packages.md) defined by a FHIR `PlanDefinition`. Integrations can launch the built-in package renderer or manage the form-by-form workflow themselves.

## Requirements

* The caller needs access to the package definition, its questionnaires, and the resources created for a run.
* Package operations follow the configured storage routing. `data-store` handles `PlanDefinition`, `RequestGroup`, `Task`, `QuestionnaireResponse`, and extraction results; `form-store` handles `Questionnaire`. Without an external store, the corresponding resources remain local.
* An external data store must accept FHIR transaction bundles. See [external package storage](../aidbox-ui-builder-alpha/external-fhir-servers-as-a-data-backend.md#package-storage-requirements).
* For signed renderer links, [configure the sharing keys](../aidbox-ui-builder-alpha/form-sharing.md). Email delivery also requires an email provider and the async scheduler.

## Operations

All package operations below use `POST`.

| Resource path after `[base]/fhir` | Operation | Result |
| --- | --- | --- |
| `/PlanDefinition/[id]` | `$sdc-package-start` | Create a run and return its reference and form summaries. |
| `/PlanDefinition/[id]` | `$sdc-package-start-link` | Create a run and return its signed renderer link. |
| `/RequestGroup/[id]` | `$sdc-package-generate-link` | Generate another link to an existing run. |
| `/RequestGroup/[id]` | `$sdc-package-open` | Get current form summaries, or open a selected form. |
| `/RequestGroup/[id]` | `$sdc-package-submit` | Validate and submit the enabled forms together, or submit amendments. |
| `/PlanDefinition/[id]` | `$sdc-package-send` | Start the package email delivery workflow. |
| `/RequestGroup/[id]` | `$sdc-package-stop-notification` | Cancel outstanding email notification workflows for a run. |

Save draft answers with the existing [`QuestionnaireResponse/$save`](aidbox-sdc-api.md#save-a-questionnaireresponse-save) operation. Use `$sdc-package-submit` to finish the package.

Package link creation and submission record successful and failed attempts when [audit logging](../audit-logging-in-forms.md#form-packages) is enabled.

For [multitenant deployments](../aidbox-ui-builder-alpha/forms-multitenancy.md), use the same paths after `[base]/Organization/[organization-id]/fhir`. Keep the same organization scope throughout the workflow.

## Example setup

Download the [example transaction bundle](../../assets/form-packages-example.json) and send it to:

```http
POST [base]/fhir
Content-Type: application/json
```

The bundle writes three questionnaires and the `example-intake-package` definition using `PUT` entries. Its resource IDs and canonical URLs are examples; adjust them when importing into your own namespace.

The forms are Intake, conditional Travel arrangements, and always-included Review. Travel arrangements copies the name from Intake and is enabled when `needs-travel` is true.

## Package definition

The example uses this `PlanDefinition`:

```json
{
  "resourceType": "PlanDefinition",
  "id": "example-intake-package",
  "url": "https://example.org/PlanDefinition/intake-package",
  "version": "1",
  "title": "Intake package",
  "status": "active",
  "type": {
    "coding": [
      {
        "system": "http://health-samurai.io/fhir/sdc/CodeSystem/plan-definition-type",
        "code": "sdc-package"
      }
    ]
  },
  "action": [
    {
      "id": "intake",
      "title": "Intake",
      "definitionCanonical": "https://example.org/Questionnaire/package-intake|1"
    },
    {
      "id": "travel",
      "title": "Travel arrangements",
      "definitionCanonical": "https://example.org/Questionnaire/package-travel|1",
      "condition": [
        {
          "kind": "applicability",
          "expression": {
            "language": "text/fhirpath",
            "expression": "%intake.repeat(item).where(linkId = 'needs-travel').answer.value = true"
          }
        }
      ],
      "extension": [
        {
          "url": "http://health-samurai.io/fhir/sdc/StructureDefinition/sdc-package-prefill",
          "extension": [
            { "url": "targetLinkId", "valueString": "traveler-name" },
            {
              "url": "expression",
              "valueExpression": {
                "language": "text/fhirpath",
                "expression": "%intake.repeat(item).where(linkId = 'full-name').answer.value.first()"
              }
            }
          ]
        }
      ]
    },
    {
      "id": "review",
      "title": "Review",
      "definitionCanonical": "https://example.org/Questionnaire/package-review|1"
    }
  ]
}
```

### Supported structure

* `action` is a nonempty, flat, ordered list. Each action refers to one `Questionnaire` through `definitionCanonical`.
* Action IDs must be unique FHIR IDs: 1–64 letters, digits, dots, or hyphens. The names `patient`, `requestGroup`, `responses`, `resource`, and `context` are reserved.
* Conditions use `kind: "applicability"` and `language: "text/fhirpath"`.
* Use `url|version` to identify a questionnaire version. `Questionnaire/[id]` is also supported.
* Nested actions, `definitionUri`, `dynamicValue`, `relatedAction`, and action timing are not supported. If specified, `selectionBehavior` must be `all`, `requiredBehavior` must be `must`, and `cardinalityBehavior` must be `single`.
* Adaptive questionnaires are not supported in packages.

The `sdc-package` type coding identifies the definition as a package in Formbox UI. The [Package Designer](../package-designer.md) writes this structure for you.

Starting a run stores references to the plan and resolved questionnaires, including their versions when available. Keep referenced versions unchanged if existing runs must retain their original behavior. Updating a resource in place also changes what that reference resolves to.

### Expressions

Conditions and prefills share these variables:

| Variable | Value |
| --- | --- |
| `%intake`, `%travel`, `%review` | The response for that action ID in this run. Unopened forms are empty collections. |
| `%patient` | The package's patient, when a subject was provided. |
| `%requestGroup` | The package instance. |
| `%responses` | A collection `Bundle` of available responses. Entries use `fullUrl: "urn:action:[action-id]"`. This bundle is also the expression's root input. |

All action variables are available regardless of action order. Saved responses from disabled forms remain visible to expressions until submission discards excluded drafts. Amendment requests use their supplied response drafts in place of the saved responses.

A condition must return one Boolean or an empty collection. Only `true` enables the form; empty means false. A form without conditions is enabled, and multiple conditions on an action are combined with AND.

For example, test whether another form has any answers:

```fhirpath
%intake.repeat(item).answer.value.exists()
```

Each prefill extension specifies a target `linkId` and an expression returning answer values. The target must be a unique answer-bearing item; groups, display items, and items inside repeating ancestors are not supported. A repeating target accepts multiple values. A non-repeating target accepts at most one, so use `.first()` when selecting one answer from a collection.

On the first opening, ordinary questionnaire population runs before package prefills. A nonempty prefill result supplies the target's initial answers. An empty result leaves its populated answer unchanged. Reopening an existing response preserves its answers.

## Create a run and link

Use this operation when your application launches the built-in renderer:

```http
POST [base]/fhir/PlanDefinition/example-intake-package/$sdc-package-start-link
```

```json
{
  "resourceType": "Parameters",
  "parameter": [
    { "name": "expiration", "valueInteger": 7 },
    { "name": "allow-amend", "valueBoolean": true }
  ]
}
```

The response is a `Parameters` resource with:

* `link.valueUri`: the signed package renderer URL.
* `requestGroup.valueReference`: a reference such as `RequestGroup/[run-id]`.

The example needs no patient. Add `subject` to associate the run with an existing patient. The operation creates a new run on every successful call; keep the returned reference to resume it later.

### Start parameters

These optional parameters apply to both `$sdc-package-start` and `$sdc-package-start-link`:

| Parameter | FHIR representation | Meaning |
| --- | --- | --- |
| `subject` | `valueReference` | Patient reference for the package responses. |
| `encounter` | `valueReference` | Encounter reference for the package responses. |
| `context` | Repeated `part` | Population context, using `name` with `valueString` and `content` with a resource or `valueReference`. Uses the [population API format](fhir-sdc-api.md). |
| `local` | `valueBoolean` | Use local data during ordinary questionnaire population. |

The context is retained for population when forms open. Each new response inherits the package subject and encounter when supplied.

### Link options

`$sdc-package-start-link` and `$sdc-package-generate-link` accept:

| Parameter | FHIR representation | Meaning |
| --- | --- | --- |
| `expiration` | `valueInteger` | Link lifetime in days; default 7. |
| `theme` | `valueString` | Questionnaire theme ID. |
| `config` | `valueString` | `SDCConfig` ID. |
| `allow-amend` | `valueBoolean` | Enable amendment in the renderer after submission. |
| `app-name` | `valueString` | Application label used for the link and audit event. |
| `redirect-on-submit` | `valueString` | URL opened after submission. |
| `redirect-on-save` | `valueString` | URL for the renderer's save-and-exit behavior. |
| `redirect-on-timeout` | `valueString` | URL opened after the session expires. |
| `session-timeout` | `valueInteger` | Inactivity timeout in **milliseconds**. Creates a server-side session. |
| `warning-timeout` | `valueInteger` | Warning lead time in **milliseconds**. |

A generated link is scoped to one package and its organization. Anyone using it accesses that same run. Generate a separate run for each recipient.

`$sdc-package-start-link` creates the run before generating its link and optional session. If link or session creation fails, the created run remains stored.

## Resume or embed a run

To generate a new link without starting another package:

```http
POST [base]/fhir/RequestGroup/[run-id]/$sdc-package-generate-link
```

```json
{ "resourceType": "Parameters" }
```

The result contains `link.valueUri`. Use the returned URL as-is, including its token and organization route. To embed it:

```html
<iframe title="Intake package" src="YOUR_GENERATED_PACKAGE_LINK"></iframe>
```

To embed a run with the renderer web component, pass its RequestGroup ID in `request-group-id` on `<aidbox-form-renderer>`. The component manages navigation, draft saves, and package submission. See [Embedding a package](../aidbox-ui-builder-alpha/embedding.md#embedding-a-package) for examples, progress events, and `submit()` results.

## Manage the workflow in your application

### Start without a link

```http
POST [base]/fhir/PlanDefinition/example-intake-package/$sdc-package-start
```

```json
{ "resourceType": "Parameters" }
```

This creates an active `RequestGroup` with `intent: "order"` and one requested `Task` with `intent: "option"` per action. Each `RequestGroup.action.resource` references its task. A task's `input` identifies the questionnaire; its `output` gains the response reference when opened.

The response contains `requestGroup.valueReference`, `status.valueCode`, and repeated `form` parameters. Each `form.part` contains:

| Name | Value | Meaning |
| --- | --- | --- |
| `actionId` | `valueId` | Action ID from the plan. |
| `title` | `valueString` | Action title, falling back to the questionnaire label. |
| `enabled` | `valueBoolean` | Whether this form is currently included. |
| `ready` | `valueBoolean` | Whether its response is valid and its current version is confirmed. Completed tasks count as confirmed. |
| `issues` | `resource` | An `OperationOutcome` when its existing response fails questionnaire validation. |

`enabled` and `ready` are independent. A valid saved draft is confirmed when advancing from that response version. Saving it again requires another confirmation.

### Open a form

```http
POST [base]/fhir/RequestGroup/[run-id]/$sdc-package-open
```

```json
{
  "resourceType": "Parameters",
  "parameter": [{ "name": "actionId", "valueId": "intake" }]
}
```

The response includes the current `status` and `form` summaries, plus `questionnaire.resource` and `response.resource` for the requested action. A new response is populated and persisted with status `in-progress`; later openings return the existing response. As with ordinary `$populate`, the response can include population issues in `issues.resource`.

To refresh the overview without opening a form, omit `actionId` and send only `{ "resourceType": "Parameters" }`. Opening a disabled form is rejected.

### Save answers

Send the response returned by `$sdc-package-open`, with the changed answers, to:

```http
POST [base]/fhir/QuestionnaireResponse/$save
```

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "response",
      "resource": {
        "resourceType": "QuestionnaireResponse",
        "id": "intake-response-id",
        "status": "in-progress",
        "questionnaire": "https://example.org/Questionnaire/package-intake|1",
        "item": [
          { "linkId": "full-name", "answer": [{ "valueString": "Alex Taylor" }] },
          { "linkId": "needs-travel", "answer": [{ "valueBoolean": true }] }
        ]
      }
    }
  ]
}
```

Replace `intake-response-id` with the returned response ID and preserve its other fields, including subject, encounter, and version metadata when present. Use the saved resource returned by `$save` for subsequent edits.

Saving a draft does not require every questionnaire answer to be complete. Refresh the package overview after a successful save so conditions use the persisted answers. The signed package token permits saving in-progress responses belonging to its active package.

### Advance or go back

To validate and confirm Intake before opening Travel arrangements:

```json
{
  "resourceType": "Parameters",
  "parameter": [
    { "name": "actionId", "valueId": "travel" },
    { "name": "fromActionId", "valueId": "intake" }
  ]
}
```

Send this to `$sdc-package-open` after saving Intake. A source validation failure leaves it unconfirmed. Once confirmed, it stays confirmed even if population of the destination form fails. To go back without confirming the form being left, send only the destination `actionId`.

Use the ordered summaries to choose the next enabled form. `fromActionId` always requires a destination `actionId`.

### Submit

Open and fill every enabled form, including Review in this example, then send:

```http
POST [base]/fhir/RequestGroup/[run-id]/$sdc-package-submit
```

```json
{ "resourceType": "Parameters" }
```

Submission re-evaluates conditions and validates all enabled responses. As with ordinary form submission, it fills in a missing author when the current user is known and renders any configured narrative template before extraction. It then sends one FHIR transaction bundle that:

* marks the `RequestGroup` and included tasks `completed`;
* marks the included draft responses `completed`;
* marks excluded tasks `cancelled` and removes their unsubmitted drafts;
* writes extraction results.

The result is a `Parameters` resource containing `status.valueCode: "completed"`. Extraction issues are returned in `issues.resource` alongside the result, as with ordinary form submission. Failed questionnaire validation or a failed extraction operation prevents submission.

The configured data store commits or rolls back each transaction bundle, for both local and external storage. Package operations do not wrap separate writes in an additional transaction or coordinate rollback across servers. A later failure does not undo a committed bundle. If a failure leaves the result uncertain, reload the package before retrying.

Use `$sdc-package-open` to review a completed run. Its included form set is taken from the completed tasks.

## Amend a completed run

Keep amendment edits in your application until they are submitted. Send them to `$sdc-package-open` as repeated `form` parameters to preview conditions and prefills without changing the stored submission:

```json
{
  "resourceType": "Parameters",
  "parameter": [
    { "name": "actionId", "valueId": "travel" },
    {
      "name": "form",
      "part": [
        { "name": "actionId", "valueId": "intake" },
        {
          "name": "response",
          "resource": {
            "resourceType": "QuestionnaireResponse",
            "id": "intake-response-id",
            "status": "completed",
            "questionnaire": "https://example.org/Questionnaire/package-intake|1",
            "item": [
              { "linkId": "full-name", "answer": [{ "valueString": "Alex Taylor" }] },
              { "linkId": "needs-travel", "answer": [{ "valueBoolean": true }] }
            ]
          }
        }
      ]
    }
  ]
}
```

Use the actual response ID and retain the original subject reference and version metadata. Each supplied form must match its package action, questionnaire, and subject; action and response IDs must be unique.

If an edit enables an unopened form, `$sdc-package-open` returns a temporary populated response for it. Retain and complete that response in the amendment session. Send all edited and newly opened responses as repeated `form` parameters to `$sdc-package-submit`.

The package remains `completed` during editing. Successful submission changes previously submitted included responses to `amended` and newly included responses to `completed`. Existing response IDs are retained, and previous versions remain in history. Validation failures and rejected transaction bundles leave the previous submission intact.

## Send a package by email

```http
POST [base]/fhir/PlanDefinition/example-intake-package/$sdc-package-send
```

This uses the [form sending workflow](../aidbox-ui-builder-alpha/form-sending.md). The path selects the package definition. The `Parameters` body must include `provider` and `email` as `valueString` values. Supported delivery options include:

| Parameter | FHIR representation | Meaning |
| --- | --- | --- |
| `provider` | `valueString` | A configured email provider. Required. |
| `email` | `valueString` | Recipient address. Required. |
| `subject` | `valueReference` | Patient for this run. |
| `send-at` | `valueString` | Future ISO-8601 instant for scheduled delivery. |
| `deadline-days` | `valueInteger` | Workflow expiration in days. |
| `email-subject`, `email-message` | `valueString` | Initial email subject and body. |
| `follow-up-enabled` | `valueBoolean` | Enable reminders. |
| `follow-up-delay-days` | `valueInteger` | Delay before a reminder, in days. |
| `follow-up-time`, `follow-up-message` | `valueString` | Reminder time and body. |
| `clinician-email` | `valueString` | Practitioner notification address. |

The workflow creates the package, generates its link, and tracks delivery and completion against the `RequestGroup`. No email is sent by `$sdc-package-start-link`; `$sdc-package-send` starts delivery.

To cancel outstanding notifications for a run, call:

```http
POST [base]/fhir/RequestGroup/[run-id]/$sdc-package-stop-notification
```

The response is an informational `OperationOutcome` with the number of cancelled notification workflows.

## Find package runs and responses

Use the package canonical URL, without its version, to search:

```http
GET [base]/fhir/RequestGroup?sdc-package=https%3A%2F%2Fexample.org%2FPlanDefinition%2Fintake-package
GET [base]/fhir/QuestionnaireResponse?sdc-package=https%3A%2F%2Fexample.org%2FPlanDefinition%2Fintake-package
```

The searches cover runs of all versions with that canonical URL. To identify the responses in one run, follow its `RequestGroup.action.resource` task references and the tasks' `questionnaire-response` outputs. `QuestionnaireResponse.questionnaire` identifies the individual questionnaire, not the package.

## Errors

Package operations return FHIR `OperationOutcome` resources for expected failures.

| Status | Typical cause |
| --- | --- |
| `400` | A transaction bundle is rejected, for example because an extracted resource is invalid. External stores may return a different error status. |
| `403` | The caller lacks access, or a shared token is used outside its package or organization scope. |
| `404` | A referenced resource cannot be found. |
| `409` | The requested form is disabled, the package or response is in the wrong state, or a completed package is submitted without amendment drafts. |
| `422` | Unsupported package structure, invalid expression or prefill target, failed questionnaire validation, or missing sharing keys. |

Keep the complete `OperationOutcome` when reporting an error; it can contain field-level validation details. Package writes use ordinary PUT semantics without automatic version preconditions, so a later write can overwrite earlier changes. Reload current state before retrying a failed request.
