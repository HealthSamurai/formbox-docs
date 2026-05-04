---
description: Apply NHS Design System styling to FHIR Questionnaires with Formbox for UK healthcare applications.
---

# NHS integration

### Summary

Formbox supports an NHS-branded look and feel designed for applications that use FHIR Questionnaires and need to be embedded into the NHS App or other NHS-aligned services.
This theme follows the official [NHS Design System](https://service-manual.nhs.uk/design-system), including its styling rules, layout patterns, and UI components.

### What is the NHS?

The **National Health Service (NHS)** is the publicly funded healthcare system of the United Kingdom. It provides healthcare services to residents, including general practice, hospital care, mental health services, and public health programs.

The NHS is known for its standardized approach to healthcare delivery, including clear guidelines, consistent patient-facing design, and digital services that follow the [NHS Design System](https://service-manual.nhs.uk/design-system). The design system ensures that digital forms, applications, and interfaces are accessible, user-friendly, and consistent across N


### Description

#### Implementation

Formbox introduces the [`QuestionnaireTheme.design-system`](../configuration.md) property, which allows selecting from predefined design systems:

- `aidbox-desktop`
- `aidbox-mobile`
- `NHS`

Choosing the `NHS` design system applies NHS-compliant styles and components across the rendered Questionnaire.

#### Important Notes

- The NHS design system currently supports only the following [entry mode](./entry-mode.md):
  - `sequential w. edit` 

Support for additional entry modes may be added in the future.

### Usage

To enable the NHS look and feel in Formbox:

1. Create a `Questionnaire`
   Set its `entry-mode` to the following mode supported by the NHS design system:
   - `sequential w. edit`

2. Create a `QuestionnaireTheme`
   Set the `design-system` property to `NHS`.

{% hint style="warning" %}
If you are creating a form with the NHS design system for the first time, select `Add new theme`. In the `Theme Editor` under `General`, choose `NHS (National Health System)` and provide a theme name. Click `Save`, and then select the newly created theme from the list of available themes.
{% endhint %}

3. Choose how the theme is applied
   You can apply the NHS theme in two ways:

   System-wide:
   Configure your `SDCConfig` to use this theme as the default.
   All Questionnaires without an explicitly assigned theme will automatically use the NHS design system.

   Per questionnaire (when sharing forms):
   When generating patient-facing links using the [populatelink](../../reference/fhir-sdc-api#populate-questionnaire-and-generate-a-link-populatelink) or [generate-link](../../reference/aidbox-sdc-api#generate-a-link-to-a-questionnaireresponse-generate-link) operations,
   include the theme in the `theme` property to apply the NHS design only to that specific form.

This setup allows you to use the NHS design system globally or only for selected Questionnaires, depending on your needs.
