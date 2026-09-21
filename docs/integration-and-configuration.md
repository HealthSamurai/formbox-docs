# Integration and configuration

This section covers the main integration and configuration options available in Formbox.

It includes:

* embedding the Form Builder and Form Renderer
* request interception and runtime configuration
* multitenancy and terminology integration
* SMART on FHIR / EHR integration
* external renderer and external FHIR server integration
* storage configuration for attachments

For workflows spanning several questionnaires, use the [Form packages API](reference/plan-definition-api.md) to create and resume runs. [Embed the package renderer](aidbox-ui-builder-alpha/embedding.md#embedding-a-plan-definition) with the existing `<aidbox-form-renderer>` web component using `request-group-id`, or use a generated link in an iframe. The component also supports definition previews through `plan-definition`, progress callbacks, and submission from your application. The [package concepts](plan-definitions.md) describe ordering, conditions, prefills, and submission.

The SMART on FHIR page describes the EHR launch scenario only. It covers launching Formbox from an external EHR, using the current patient and encounter context, and adapting the default UI to that SMART session. It does not describe standalone launch or generic OAuth-only flows.
