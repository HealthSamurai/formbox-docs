# Release Notes

## June 2026 _`latest, 2606`_

* **Formbox (formerly Aidbox Forms)**
  * Added auto-save support to preserve form data when the form is reloaded while being filled out.
  * Improved form submission handling for text fields and date/time/datetime fields, ensuring the latest entered values are submitted even without a prior blur event.
  * Enhanced the signature item to respect the browser locale when displaying the form signing date and time.
  * Updated the Calculated Expression section to open the Advanced Editor by default, helping prevent accidental data loss.
  * Added support for including SDCConfig when sharing a form.
  * Added support for applying themes included in SDCConfig when sharing a form.
  * Improved SDCConfig loading in the embedded form builder via onFetch.

## May 2026 _`stable, 2605`_

* **Formbox (formerly Aidbox Forms)**
  * Improved date validation messaging in NHS forms to provide clearer and more accurate min/max validation feedback.
  * Enhanced the Entry mode layout to ensure required field indicators are displayed consistently.
  * Improved the organization selection experience in multi-organization environments.
  * Enhanced security by restricting AccessPolicy matching to SDC-issued JWT tokens only.
  * Expanded Group item population capabilities with support for FHIR Query configuration and variables.
  * Improved Azure Blob Storage integration for file attachment uploads.
  * Enhanced multilingual form support by automatically loading the default language from the configuration.

## April 2026 _`stable, 2604`_

* **Formbox (formerly Aidbox Forms)**
  * Implemented [EHR integration via SMART on FHIR](aidbox-ui-builder-alpha/smart-app-integration.md), with Formbox acting as a SMART app (read), tested with Cerner.
  * Introduced a new Formbox UI, now available in beta, with a banner on the main Forms page to switch versions.

## March 2026 _`2603`_

* **Formbox (formerly Aidbox Forms)**
  * Supported the ability to run Formbox in [Sandbox mode](getting-started-formbox.md).
  * Added the ability to configure a [list of terminology servers](aidbox-ui-builder-alpha/integration-with-external-terminology-servers.md).
  * Implemented a new [Form Builder layout](aidbox-ui-builder-alpha/ui-builder-interface.md#ui-builder-interface-overview), which will become the default in the next release.
  * Added the ability to include a [header](aidbox-ui-builder-alpha/form-creation/widgets.md) and [footer](aidbox-ui-builder-alpha/form-creation/widgets.md) in a form.
  * Implemented workflow automation for [sending forms from UI](aidbox-ui-builder-alpha/form-sending.md) to patients via [email](reference/aidbox-sdc-api.md#notify-a-patient-notify-patient).
  * Added support for [connecting AI agents to Formbox Builder](aidbox-ui-builder-alpha/mcp.md) via MCP.
  * Added the ability to replace the loading spinner with a [custom one](aidbox-ui-builder-alpha/embedding.md#step-4-optional-custom-loading-content).
  * Extended customization options for the embedded Form Builder, including the ability to hide the adaptive form feature and custom renderer selection in [Configuration](aidbox-ui-builder-alpha/configuration.md#configuration-resource-structure).
  * Added full [Form Builder translations](aidbox-ui-builder-alpha/configuration.md#translations), including support for Arabic, and enabled Arabic for [multilingual forms](aidbox-ui-builder-alpha/form-creation/multilingual-forms.md).
  * Supported [form sharing from the root level across tenants](aidbox-ui-builder-alpha/forms-multitenancy.md#form-templates) in multi-tenancy mode.
  * Added the ability to configure a [session timeout parameter](aidbox-ui-builder-alpha/form-sharing.md) for automatic logout in forms.
  * Optimized form loading time for the embedded Form Renderer and Form Builder.
