---
description: Voice agent resources and operations — SDCVoiceAgent, SDCVoicePrompt, $call, $hangup, and run results.
---

# Voice Agents API

How-to: [Voice Agents](../voice-agents.md).

`$call` is the integration operation. `$hangup` and `GET /sdc/voice/run/{call-id}` belong on the same path. Agents themselves are created with ordinary FHIR CRUD.

Request and response bodies for `$call` use FHIR [Parameters](https://www.hl7.org/fhir/parameters.html). Validation errors are `OperationOutcome`.

## Resources

### SDCVoicePrompt

Versioned instruction text. Identity is `url` and is stable across revisions: an agent points at the lineage, and the active revision is resolved when the call is placed.

| Element | Cardinality | Type | Description |
| --- | --- | --- | --- |
| `url` | 1..1 | uri | Canonical identity of the lineage, stable across versions |
| `version` | 0..1 | string | Business version of this revision |
| `name` | 0..1 | string | Machine-readable name |
| `title` | 0..1 | string | Human-readable name |
| `status` | 1..1 | code | `draft` \| `active` \| `retired`. Only active revisions are picked up |
| `date` | 0..1 | dateTime | When this revision was authored |
| `publisher` | 0..1 | string | Who authored this revision |
| `description` | 0..1 | markdown | What this prompt is for, and what changed |
| `role` | 0..1 | code | `interviewer` \| `summarizer` \| `extractor` |
| `prompt` | 1..1 | markdown | The instruction text, sent as the system message |

```yaml
PUT /fhir/SDCVoicePrompt/post-discharge-v1
content-type: text/yaml

resourceType: SDCVoicePrompt
url: http://example.org/SDCVoicePrompt/post-discharge
version: '1.0.0'
name: post-discharge-checkin
title: Post-discharge check-in
status: active
role: interviewer
prompt: |
  You are a nurse assistant calling a patient two days after discharge.
  Be warm and brief. Ask one question at a time and confirm what you heard
  before moving on. If the patient reports chest pain or shortness of breath,
  tell them to contact emergency services and end the call.
```

The hang-up instruction is appended by Aidbox to every prompt, so it does not belong in the text.

### SDCVoiceAgent

Binds a prompt to the questionnaires it administers, the context it needs, and the models it speaks with.

| Element | Cardinality | Type | Description |
| --- | --- | --- | --- |
| `url` | 0..1 | uri | Canonical identity of this agent |
| `name` | 0..1 | string | Machine-readable name |
| `title` | 0..1 | string | Human-readable name |
| `status` | 1..1 | code | `draft` \| `active` \| `retired`. Only active agents can be called |
| `description` | 0..1 | markdown | What this agent is for |
| `prompt` | 1..1 | canonical | `SDCVoicePrompt` lineage url. Version-less |
| `questionnaire` | 1..\* | canonical | Questionnaires to administer. Array order is interview order |
| `context` | 0..\* | BackboneElement | Resources the agent needs, validated before the call is placed |
| `context.name` | 1..1 | code | Slot name used in `$call` and in the prompt, e.g. `patient` |
| `context.type` | 1..1 | code | Resource type accepted in this slot |
| `context.label` | 0..1 | string | Human-readable name for the UI picker |
| `llm` | 0..1 | Reference(Device) | Conversation model. Falls back to the deployment default |
| `stt` | 0..1 | Reference(Device) | Speech-to-text model |
| `tts` | 0..1 | Reference(Device) | Text-to-speech voice |
| `extractorLlm` | 0..1 | Reference(Device) | Model that reads the transcript into answers. Falls back to `llm` |
| `maxAttempts` | 0..1 | integer | Redials when nobody picks up. Defaults to a single attempt |
| `retryDelayMinutes` | 0..1 | integer | Gap between redial attempts |
| `maxDurationMinutes` | 0..1 | integer | Hard cap on one conversation; the call is hung up when reached |

```yaml
PUT /fhir/SDCVoiceAgent/post-discharge
content-type: text/yaml

resourceType: SDCVoiceAgent
url: http://example.org/SDCVoiceAgent/post-discharge
name: post-discharge-checkin
title: Post-discharge check-in
status: active
prompt: http://example.org/SDCVoicePrompt/post-discharge
questionnaire:
- http://example.org/Questionnaire/discharge-symptoms
- http://example.org/Questionnaire/medication-adherence
context:
- name: patient
  type: Patient
  label: Patient
- name: encounter
  type: Encounter
  label: Discharge encounter
llm:
  reference: Device/gpt-4o-mini
stt:
  reference: Device/flux-general-en
tts:
  reference: Device/sonic-3-5
extractorLlm:
  reference: Device/gpt-4o
maxAttempts: 3
retryDelayMinutes: 60
maxDurationMinutes: 15
```

A phone call needs one context slot of type `Patient` — that is who gets dialled.

### Device (a model)

Each model is a `Device`, so a `Provenance` can point at the model that produced the answers. The designer creates these on first use; an integration can write them itself.

{% hint style="info" %}
The `Device`, `Provenance` and prompt documents are shaped after the HL7 [AI Transparency on FHIR](https://build.fhir.org/ig/HL7/aitransparency-ig/) IG — `AI-Device`, `AI-Provenance`, `AI-InputPrompt`, and the `AIKind` / `AIconfidence` extensions.

That IG is still in STU1 ballot, so its profiles and codes may change. Aidbox follows its shapes but does not claim conformance.
{% endhint %}

```yaml
PUT /fhir/Device/gpt-4o-mini
content-type: text/yaml

resourceType: Device
meta:
  profile:
  - http://hl7.org/fhir/uv/aitransparency/StructureDefinition/AI-Device
status: active
identifier:
- system: urn:sdc:voice:openai
  value: gpt-4o-mini
type:
  coding:
  - system: http://hl7.org/fhir/uv/aitransparency/CodeSystem/AIdeviceTypeCS
    code: Artificial-Intelligence
extension:
- url: http://hl7.org/fhir/uv/aitransparency/StructureDefinition/aitransparency.AIKind
  valueCodeableConcept:
    coding:
    - system: http://hl7.org/fhir/uv/aitransparency/CodeSystem/AIdeviceTypeCS
      code: Large-Language-Models
manufacturer: openai
modelNumber: gpt-4o-mini
deviceName:
- name: gpt-4o-mini
  type: model-name
```

`AIKind` is `Large-Language-Models` for an LLM and `AI-for-Audio-Data` for speech models. `identifier` is `urn:sdc:voice:{provider}|{model}`, which is how an existing Device is found instead of duplicated.

## Place a call — $call

Creates the `Task` that owns the conversation and places (or schedules) the phone call. Phone is the default. The workflow owns retries (`maxAttempts`, `retryDelayMinutes` on the agent).

Integrations use the default, `phone`.

### URLs

```
POST [base]/sdc/voice/$call
```

### Parameters

| Parameter | Cardinality | Type | Notes |
| --- | --- | --- | --- |
| `agent` | 1..1 | [Reference](http://hl7.org/fhir/R4/references.html#Reference)`(SDCVoiceAgent)` or `SDCVoiceAgent` | A reference runs a stored agent. A whole resource runs a draft without saving |
| `transport` | 0..1 | [string](http://hl7.org/fhir/R4/datatypes.html#string) | `phone` (default). `browser` is used by the Formbox tester |
| `context` | 0..\* | Backbone | One `{name, content}` per slot the agent declares. All required |
| `to` | 0..1 | [string](http://hl7.org/fhir/R4/datatypes.html#string) | Phone only. E.164 or SIP. Defaults to the patient's first phone |
| `send-at` | 0..1 | [instant](http://hl7.org/fhir/R4/datatypes.html#instant) | Phone only. Schedule the dial instead of dialling now |

Passing `to` or `send-at` with `transport: browser` is refused.

#### context

Same shape as `$populate` launch context: repeating parameter of `{name, content}` pairs.

```yaml
- name: context
  part:
  - name: name
    valueString: patient
  - name: content
    valueReference:
      reference: Patient/123
```

### Output

| Parameter | Cardinality | Type | Notes |
| --- | --- | --- | --- |
| `task` | 1..1 | Reference(Task) | Created Task |
| `status` | 1..1 | string | Task status |
| `call-id` | 1..1 | string | Conversation id |
| `operation-id` | 0..1 | string | Scheduled phone calls only |

### Example

```yaml
POST /sdc/voice/$call
content-type: text/yaml
accept: text/yaml

resourceType: Parameters
parameter:
- name: transport
  valueString: phone
- name: agent
  valueReference:
    reference: SDCVoiceAgent/abc
- name: to
  valueString: '+15551234567'
- name: context
  part:
  - name: name
    valueString: patient
  - name: content
    valueReference:
      reference: Patient/123
```

```yaml
resourceType: Parameters
parameter:
- name: task
  valueReference:
    reference: Task/def
- name: status
  valueString: requested
- name: call-id
  valueString: 0c1a2b3c-4d5e-6f70-8899-aabbccddeeff
```

### Errors

`OperationOutcome` when: agent missing or not an `SDCVoiceAgent`, agent not `active`, agent with no questionnaire, bad `send-at`, unsupplied / unresolvable / mistyped context, no Patient slot for a phone call, no phone number, or Twilio misconfiguration.

## End a call — $hangup

Ends a live phone call. Body is JSON, not `Parameters`.

### URLs

```
POST [base]/sdc/voice/$hangup
```

```json
{"call": "<call-id>"}
```

`404` if there is no live call with that id.

## Run results

Returns the transcript, `QuestionnaireResponse`s, `Provenance`, any downstream extracted resources, and the Task.

This reads the Task; it does not wait. Extraction runs after the call ends, so a request made too early returns a Task whose `output` is not filled in yet — poll, or watch the Task.

### URLs

```
GET [base]/sdc/voice/run/{call-id}
```

`404` if there is no call with that id.

## What a call writes

The Task is created first (`status: requested`).

| | Phone call | Test run |
| --- | --- | --- |
| `intent` | `order` | `proposal` |
| `code` | `voice-call` | `voice-rehearsal` |

`identifier` holds the `call-id`. `focus` is the first questionnaire. `for` is the Patient when there is one. A draft agent sent whole is contained on the Task.

After hangup, extraction writes resources and lists them on `Task.output`:

| `type.text` | Resource |
| --- | --- |
| `transcript` | `DocumentReference` |
| `questionnaire-response` | `QuestionnaireResponse` (one per extractable questionnaire) |
| `provenance` | `Provenance` (omitted when there is no extractor `Device`) |
| `extracted` | What SDC `$extract` made of a completed response |

Completed responses go through `$submit`. Partial ones (`in-progress`) are stored as they are. A call with no answers still writes `Task.output`, so it is not retried as a missed call.

### Answers and their evidence

Each `QuestionnaireResponse.item` carries what backs its answer, so an answer can be checked without re-reading the transcript. An item the conversation never settled is still present, saying why it is empty.

| Extension | Type | Meaning |
| --- | --- | --- |
| `.../sdc-voice/answer-status` | code | Whether the person stated it or it was inferred |
| `.../sdc-voice/evidence` | string | The words the answer was read from |
| `.../sdc-voice/source-turn` | string | Which turn they were said in, tagged `[P1]`, `[P2]` in the transcript |
| [`AIconfidence`](http://hl7.org/fhir/uv/aitransparency/StructureDefinition/AIconfidence) | decimal | How sure the model was, 0‑1 |

The first three use the base `http://health-samurai.io/fhir/StructureDefinition/sdc-voice`.

```yaml
resourceType: QuestionnaireResponse
questionnaire: http://example.org/Questionnaire/discharge-symptoms
status: completed
authored: '2026-03-04T10:21:44Z'
subject:
  reference: Patient/123
item:
- linkId: chest-pain
  text: Any chest pain since you went home?
  extension:
  - url: http://health-samurai.io/fhir/StructureDefinition/sdc-voice/answer-status
    valueCode: stated
  - url: http://health-samurai.io/fhir/StructureDefinition/sdc-voice/evidence
    valueString: no, nothing like that
  - url: http://health-samurai.io/fhir/StructureDefinition/sdc-voice/source-turn
    valueString: P2
  - url: http://hl7.org/fhir/uv/aitransparency/StructureDefinition/AIconfidence
    valueDecimal: 0.96
  answer:
  - valueBoolean: false
```

`Provenance` marks the record as AI-asserted (`AIAST`), names the extractor `Device` as author with role `Artificial-Intelligence`, and lists the person who spoke as `informant` — the call has no human author, so the model is the author and the patient is the source of the answers. Its `entity` cites the transcript and carries both prompts — the one that drove the conversation and the one that read it back into answers — as contained `AI-InputPrompt` documents.

## Settings

| ID | Environment variable |
| --- | --- |
| `module.sdc.openai-api-key` | `BOX_MODULE_SDC_OPENAI_API_KEY` / `BOX_SDC_OPENAI_API_KEY` |
| `module.sdc.openai-base-url` | `BOX_MODULE_SDC_OPENAI_BASE_URL` / `BOX_SDC_OPENAI_BASE_URL` |
| `modules.sdc.gemini-api-key` | `BOX_SDC_GEMINI_API_KEY` |
| `module.sdc.deepgram-api-key` | `BOX_MODULE_SDC_DEEPGRAM_API_KEY` / `BOX_SDC_DEEPGRAM_API_KEY` |
| `module.sdc.cartesia-api-key` | `BOX_MODULE_SDC_CARTESIA_API_KEY` / `BOX_SDC_CARTESIA_API_KEY` |
| `module.sdc.elevenlabs-api-key` | `BOX_MODULE_SDC_ELEVENLABS_API_KEY` / `BOX_SDC_ELEVENLABS_API_KEY` |
| `module.sdc.twilio-account-sid` | `BOX_MODULE_SDC_TWILIO_ACCOUNT_SID` / `BOX_SDC_TWILIO_ACCOUNT_SID` |
| `module.sdc.twilio-auth-token` | `BOX_MODULE_SDC_TWILIO_AUTH_TOKEN` / `BOX_SDC_TWILIO_AUTH_TOKEN` |
| `module.sdc.twilio-from-number` | `BOX_MODULE_SDC_TWILIO_FROM_NUMBER` / `BOX_SDC_TWILIO_FROM_NUMBER` |

Gemini's setting id is `modules.sdc.gemini-api-key` (plural `modules`) and has no `BOX_MODULE_SDC_*` alias.
