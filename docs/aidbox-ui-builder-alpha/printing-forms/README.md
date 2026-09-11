---
description: Print or save forms as PDF using browser print dialog, $print API, or custom templates with $render API.
---

# Printing forms

When filling out a form, you can print it in its current state at any moment by clicking the Print button in the bottom left corner. Once you click it, you'll see the default browser print dialog allowing you to save the form as PDF or print it. The general layout is preserved, but the final look of the form in print or PDF will depend on the options you select in this print dialog (e.g. paper size, margins, background graphics).

Supported browsers: the latest versions of Chrome and Firefox.

## Machine Printing

For machine printing, you can choose between two approaches depending on your needs:

* [**$print**](native-look-form-printing.md) **API** — prints forms in their original appearance, preserving the on-screen layout. Requires a headless browser. Best when you want a faithful replica of the form.
* [**$render**](liquid-templates.md) **API** — prints forms from custom templates, with full control over layout and styling. Best when you need a tailored or standardized PDF.
  * [**Liquid print templates**](liquid-templates.md) — HTML + FHIRPath, authored in the Liquid template editor. Prefer this for new templates.
  * [**Selmer templates**](template-based-pdf-generation.md) — the older `$render` path, stored as `SDCPrintTemplate`.
