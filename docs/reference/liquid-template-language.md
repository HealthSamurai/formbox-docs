---
description: >-
  Liquid tags, FHIRPath expressions, escaping, Library shape, and the
  Questionnaire extension used by print templates.
---

# Liquid template language

How-to: [Liquid print templates](../aidbox-ui-builder-alpha/printing-forms/liquid-templates.md). Render API: [$render](aidbox-sdc-api.md#render-a-questionnaire-or-response-render).

A Liquid print template is HTML with Liquid tags. Expressions inside the tags are **FHIRPath**, not Liquid's own expression language. There are no Liquid filters: `|` is FHIRPath union.

`%resource` is the resource being rendered: a `QuestionnaireResponse` when you render a response, a `Questionnaire` when you render a blank form.

## Output

```html
{{ %resource.id }}
{{ %resource.item.count() }}
```

FHIRPath calls such as `.first()` and `.not()` work inside output tags.

Output is HTML-escaped by default (`&`, `<`, `>`, `"`, `'` become entities), so a patient answer cannot inject markup. The exception is a variable created with a `capture` block — that value is emitted raw.

## Conditionals

```html
{% if item.answer.exists() %}
  {{ item.answer.value }}
{% elsif item.text.exists() %}
  {{ item.text }}
{% else %}
  —
{% endif %}
```

## Loops

```html
{% for item in %resource.item %}
  <p>{{ item.text }}: {{ item.answer.value }}</p>
  {% if forloop.last.not() %}<hr/>{% endif %}
{% endfor %}
```

`%resource` stays addressable inside the loop. Nested loops restore the outer `forloop`. Loop helpers include `forloop.last` and `forloop.nextitem`.

## Assign and capture

```html
{% assign n = %resource.item.count() %}
<p>{{ n }} items</p>

{% capture heading %}
  <h1>{{ %resource.questionnaire }}</h1>
{% endcapture %}
{{ heading }}
```

Use `capture` when the block itself contains markup you want in the output. `assign` and `for` variables are escaped like normal output.

## Unknown tags

An unknown tag, an `if` with no expression, or a `for` with no `in` is left in the output as literal text. A broken template shows itself in the result instead of failing the request.

## Questionnaire extension

A questionnaire points at a template with `http://hl7.org/fhir/StructureDefinition/instance-rendering`:

```json
{
  "url": "http://hl7.org/fhir/StructureDefinition/instance-rendering",
  "extension": [
    {
      "url": "purpose",
      "valueCoding": { "code": "print" }
    },
    {
      "url": "template",
      "valueCanonical": "http://forms.aidbox.io/Library/my-template"
    }
  ]
}
```

* `purpose` — only `valueCoding.code` is read (`print`, `summary`, `narrative`, or `patient`). `$render` compares that code to the `purpose` parameter string.
* `template` — `valueCanonical`, or `valueReference` to `Library/<id>` or a contained `#id`.

A template linked as `narrative` is rendered on QuestionnaireResponse submit into `QuestionnaireResponse.text`. The `div` must be valid XHTML.

## Library resource

What the editor saves, and what `$render` reads:

```json
{
  "resourceType": "Library",
  "id": "liquid-template-d0TSpgNl",
  "url": "http://forms.aidbox.io/Library/liquid-template-d0TSpgNl",
  "status": "active",
  "name": "Discharge summary",
  "type": { "coding": [{ "code": "logic-library" }] },
  "topic": [{
    "coding": [{
      "system": "http://forms.aidbox.io/CodeSystem/library-topic",
      "code": "liquid-template",
      "display": "Liquid template"
    }]
  }],
  "content": [{
    "contentType": "text/html",
    "data": "<base64 of the HTML>"
  }]
}
```

| Field | Used by |
| --- | --- |
| `content[]` with `contentType: "text/html"` | `$render` — first match, base64 UTF-8. A `content[].url` or any other content type is ignored. |
| `url` | Canonical resolution from the questionnaire extension |
| `name` | UI label |
| `topic` | Editor and Form Settings pickers (`/Library?topic=…\|liquid-template`). Stamp it if you create Libraries via the API; otherwise they render but do not appear in the pickers. |
| `status`, `type` | Not read at render time |
