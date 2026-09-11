---
description: >-
  Author HTML Liquid templates with FHIRPath, link them to questionnaires, and
  render filled forms as HTML or PDF with $render.
---

# Liquid print templates

A Liquid print template is an HTML document with Liquid tags. Formbox fills those tags from a `Questionnaire` or a `QuestionnaireResponse` and returns HTML or PDF.

Templates are stored as FHIR `Library` resources. A questionnaire points at the ones it prints with, one link per purpose (`print`, `summary`, `narrative`, or `patient`). The same Library can be `print` on one form and `summary` on another.

Expressions inside the holes are **FHIRPath**, not Liquid's own expression language. There are no Liquid filters: `|` is FHIRPath union.

{% hint style="info" %}
The older `$render` path still works with Selmer templates stored as `SDCPrintTemplate`. See [Template-based PDF generation](template-based-pdf-generation.md). Prefer Liquid templates for new work — they have an editor, live preview, and AI assistance.
{% endhint %}

## Author a template

Open the editor from **Form Settings → Data → Rendering templates**: click **New template**, or the pencil next to an existing one.

The workspace is a toolbar plus three optional panels:

* **Editor** — HTML with Liquid tags (Monaco).
* **Form** — the questionnaire, with no submit button. Fill it so the template has real answers.
* **Preview** — the `$render` result. The preview uses the HTML currently in the editor, including unsaved edits.

Toggle panels from the pane header. At least the editor or the preview stays visible. With preview on, **Download PDF** is available on the right.

An AI chat sits on the right. It can read and rewrite the template, inspect the form structure, and check the last preview.

### Save vs link

**Save** writes only the `Library`. Linking that Library to a questionnaire is a separate action.

A new template starts as:

```html
<div>{{ %resource.id }}</div>
```

### Settings and linking

The gear icon opens settings over the chat panel: template title, purpose, and **Link to questionnaire**.

Search forms, tick or untick them, then **Apply**. Apply is disabled while the template has unsaved changes — save first.

You can also attach an existing template from Form Settings: **Use existing template** searches Libraries tagged as liquid templates, and **New template** opens a blank editor.

Unlinking from Form Settings removes the pointer on the questionnaire. A shared Library stays on the server.

## Template language

### Output

```html
{{ %resource.id }}
{{ %resource.item.count() }}
```

`%resource` is the resource being rendered: a `QuestionnaireResponse` when you render a response, a `Questionnaire` when you render a blank form. FHIRPath calls such as `.first()` and `.not()` work inside `{{ }}`.

Output is HTML-escaped by default (`&`, `<`, `>`, `"`, `'` become entities), so a patient answer cannot inject markup. The exception is a variable created with a `capture` block — that value is emitted raw.

### Conditionals

```html
{% if item.answer.exists() %}
  {{ item.answer.value }}
{% elsif item.text.exists() %}
  {{ item.text }}
{% else %}
  —
{% endif %}
```

### Loops

```html
{% for item in %resource.item %}
  <p>{{ item.text }}: {{ item.answer.value }}</p>
  {% if forloop.last.not() %}<hr/>{% endif %}
{% endfor %}
```

`%resource` stays addressable inside the loop. Nested loops restore the outer `forloop`. Loop helpers include `forloop.last` and `forloop.nextitem`.

### Assign and capture

```html
{% assign n = %resource.item.count() %}
<p>{{ n }} items</p>

{% capture heading %}
  <h1>{{ %resource.questionnaire }}</h1>
{% endcapture %}
{{ heading }}
```

Use `capture` when the block itself contains markup you want in the output. `assign` and `for` variables are escaped like normal output.

### Unknown tags

An unknown tag, an `if` with no expression, or a `for` with no `in` is left in the output as literal text. A broken template shows itself in the result instead of failing the request.

## Link a template from a Questionnaire

The editor writes this extension. You can also add it yourself:

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

### Narrative purpose

A template linked as `narrative` is rendered on every QuestionnaireResponse submit and stored as `QuestionnaireResponse.text`:

```json
{ "status": "generated", "div": "<div xmlns=\"http://www.w3.org/1999/xhtml\">…</div>" }
```

If rendering fails, the response is stored without a narrative. `Narrative.div` must be valid XHTML.

## Library shape

What the editor saves:

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

`$render` reads the first `content[]` entry with `contentType: "text/html"` (base64 UTF-8) and uses `url` for canonical resolution. A `content[].url` or any other content type is ignored.

`topic` is how the editor and Form Settings pickers find templates (`/Library?topic=…|liquid-template`). Stamp it if you create Libraries via the API; otherwise they render but do not appear in the pickers. `name` is the label in the UI. `status` and `type` are not used at render time.

## $render API

These paths are **not** under `/fhir`. `POST /fhir/QuestionnaireResponse/$render` returns 404.

```
POST /Questionnaire/<id>/$render
POST /QuestionnaireResponse/<id>/$render
POST /QuestionnaireResponse/$render
```

Under multi-tenancy the same operations exist as `/Organization/<org-id>/aidbox/…`.

The body is a FHIR `Parameters` resource.

| Parameter | Effect |
| --- | --- |
| `purpose` | Pick the Library the form links for this purpose. Ignored if `template-id` is present. |
| `format` | `"pdf"` → PDF. Anything else or unset → HTML. |
| `liquid-template-id` | Render this `Library` explicitly. Wins over `purpose`. |
| `template-id` | Render this `SDCPrintTemplate` (Selmer). If both engines resolve, Selmer wins. |
| `questionnaire` | Inline form — QuestionnaireResponse operation only. |
| `questionnaire-response` | Inline response — QuestionnaireResponse operation only. |

With no selector, the operation reports a missing template.

```yaml
POST /QuestionnaireResponse/qr-1/$render
Content-Type: text/yaml

resourceType: Parameters
parameter:
  - name: purpose
    valueString: print
  - name: format
    valueString: pdf
```

```yaml
POST /Questionnaire/q-1/$render
Content-Type: text/yaml

resourceType: Parameters
parameter:
  - name: liquid-template-id
    valueString: liquid-template-d0TSpgNl
```

| Status | Meaning |
| --- | --- |
| 200 | Rendered. `format=pdf` → `application/pdf`. Otherwise the HTML string. |
| 404 | Form, response, or template not found. Body is an `OperationOutcome`. |
| 422 | HTML rendered, but PDF conversion failed. |

## PDF

`$render` with `format=pdf` parses the HTML with Jsoup and converts it with openhtmltopdf. The PDF parser is strict XML; Jsoup is used so a lowercase doctype, unclosed void tags, and named entities still convert.

Constraints that matter in templates:

* **No relative URLs.** The converter has no base URI, so images and CSS must be inline or absolute.
* Use **tables**, not flexbox or grid.
* No JavaScript, no webfonts, no external images.
* Size with **points**. Use `@page` and `page-break-*` for pagination.
