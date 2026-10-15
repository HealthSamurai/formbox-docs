---
description: Form building and structured data capturing with Formbox
---

# Formbox  (formerly Aidbox Forms)

{% hint style="info" %}
Formbox was previously known as _Aidbox Forms_.\
The product has been renamed to reflect updated deployment options. The functionality remains the same.
{% endhint %}

Formbox is a platform for building, managing, and running digital healthcare forms with FHIR-based structured data capture.

It follows the [**HL7 FHIR Structured Data Capture (SDC) Implementation Guide**](https://build.fhir.org/ig/HL7/sdc/en/), supporting standard Questionnaire and QuestionnaireResponse workflows, including structured data extraction and population.

### Formbox as an Aidbox Module

Formbox is deployed as a module of Aidbox.

Formbox uses Aidbox as the underlying FHIR server and provides access to full FHIR server capabilities, including:

* Native FHIR storage
* [Form packages](plan-definitions.md) with ordered questionnaires, conditional forms, shared answers, and one submission
* Pre-fill and data extraction workflows (data is read from and written directly to Aidbox)
* SQL-on-FHIR and advanced querying across FHIR resources
* Full FHIR API support

SQL-on-FHIR analytics can be performed across all FHIR resources stored in Aidbox, not only QuestionnaireResponse data.

[**Getting Started (Aidbox Module)**](getting-started.md)

### Additional Resources

Formbox components can also be explored independently.

#### Open-Source Formbox Renderer&#x20;

Formbox includes a headless rendering engine that supports flexible UI customization through themes and design systems.\
It can be used independently of the full platform.

The renderer is available as an open-source project:

[**GitHub repository**](https://github.com/HealthSamurai/formbox-renderer)

[**Renderer documentation**](https://healthsamurai.github.io/formbox-renderer/)

#### Public Form Builder

If you want to explore the Form Builder without installing Formbox or deploying an instance, you can use the public environment:

[**Try the Public Builder**](https://form-builder.aidbox.app/)

#### Voice Agents

Design a voice agent in Formbox, test it by typing or talking to it, then phone patients with `$call` and fill Questionnaires from the conversation.

[**Voice Agents**](voice-agents.md) · [**API**](reference/voice-agents-api.md)

\
<br>
