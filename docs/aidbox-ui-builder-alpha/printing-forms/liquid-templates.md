---
description: Author HTML print templates in Form Builder and link them to questionnaires by purpose.
---

# Liquid print templates

A Liquid print template is HTML that Formbox fills from a `Questionnaire` or a `QuestionnaireResponse` and returns as HTML or PDF. Templates are stored as FHIR `Library` resources. A questionnaire links one template per purpose (`print`, `summary`, `narrative`, or `patient`); the same Library can be `print` on one form and `summary` on another.

Language reference: [Liquid template language](../../reference/liquid-template-language.md). Render API: [$render](../../reference/aidbox-sdc-api.md#render-a-questionnaire-or-response-render).

## Author a template

Open the editor from **Form Settings → Data → Rendering templates**: click **New template**, or the pencil next to an existing one.

The workspace has three optional panels:

* **Editor** — HTML with Liquid tags.
* **Form** — the questionnaire, with no submit button. Fill it so the template has real answers.
* **Preview** — the live `$render` result, including unsaved edits.

Toggle panels from the pane header. At least the editor or the preview stays visible. With preview on, **Download PDF** is on the right.

An AI chat sits on the right. It can read and rewrite the template, inspect the form, and check the last preview.

A new template starts as:

```html
<div>{{ %resource.id }}</div>
```

`%resource` is the resource being rendered. Expressions inside the tags are FHIRPath. See the [language reference](../../reference/liquid-template-language.md) for `if`, `for`, `assign`, `capture`, and escaping.

**Save** writes only the `Library`. Linking it to a questionnaire is the next step.

{% hint style="info" %}
For PDF output, use tables rather than flexbox or grid. Inline or absolute images and CSS — relative URLs do not resolve. No JavaScript, webfonts, or external images.
{% endhint %}

## Link to a questionnaire

The gear icon opens settings: template title, purpose, and **Link to questionnaire**. Search forms, tick or untick them, then **Apply**. Apply is disabled while the template has unsaved changes — save first.

From Form Settings you can also **Use existing template** (searches Libraries tagged as liquid templates) or **New template**. Unlinking removes the pointer on the questionnaire; a shared Library stays on the server.

A template linked as `narrative` is rendered on every QuestionnaireResponse submit and stored as `QuestionnaireResponse.text`. If rendering fails, the response is stored without a narrative. The output must be valid XHTML.

## Render via API

Pass `purpose` to pick the Library linked on the form, or `liquid-template-id` to point at a Library directly. `format: pdf` returns a PDF; omit it for HTML. Full parameters: [$render](../../reference/aidbox-sdc-api.md#render-a-questionnaire-or-response-render).

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
