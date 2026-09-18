---
description: Navigate Formbox UI including Form Templates, Responses, and Form Gallery with search, filter, and sharing features.
---

# Formbox UI

Formbox has two interface versions: the new Formbox UI, enabled by default, and the legacy UI, which remains available for users who prefer it.

## New UI

The new Formbox UI is organized around a left sidebar with the following sections:

* **Forms**: Create forms and run them in real clinical workflows to collect consistent, structured data across patients and cases.
* **Library**: Browse a public library of forms shared by organizations and users. Import any form to your workspace and use it as a template for a faster start.
* **Responses**: Review submitted responses by form, version, patient, and date, inspect collected answers, follow each patient's response history, and export data when needed.
* **Voice Agents**: Design and test voice agents that phone patients through your questionnaires and file the answers as FHIR data.
* **Embeds** _(in development)_: Embed configuration, launch links, and external integration entry points will live here.
* **Configurations**: Configure Formbox builder and renderer with language, themes, URL prefixes, redirects, and audit logging.
* **Admin Console**: Get administrative access to the underlying API and database.

### Forms

The **Forms** tab is the default view when you navigate to the Forms page, showing all form templates in a table with Template Name, Version, Status, Sharing Scope, Type, Updated date, and Author columns.

* **Search**: Use the search field to find a template by name or URL.
* **Add filters**: Narrow the list of templates by additional criteria.
* **Pin**: Pin forms you are currently working on to keep them at the top of the list.
* **New Form**: Create a form using one of the following options:
  * **From scratch**: Start with a blank form.
  * **Select from library**: Start from an existing template in the Library.
  * **Import**: Upload a form as JSON (a FHIR `Questionnaire`) or PDF to convert it into a `Questionnaire`.
  * **New Package**: Combine questionnaires into a [form package](form-packages.md) in the [Package Designer](package-designer.md). Select questionnaires first to use **New Package with Selected**.
* **Actions**:
  * **Preview**: Open the form in a separate popup to view its details.
  * **Share**: Generate a shareable link to the form.
  * **Send**: Send the form to a patient by email.
  * **Duplicate**: Create a copy of the form with a unique title and URL.
  * **Delete**: Delete the form template.

Packages appear alongside questionnaires with type **Package**. Expand a package row to see its forms, or click its title to open the Designer. Package rows support preview, sharing, sending, duplication, and deletion.

### Library

The **Library** is a public catalog of 4000+ form templates that you can use as-is or import and edit to fit your needs.

* **Search**: Find a template by name or URL.
* **Publisher**: Filter templates by publisher.
* **Actions**:
  * **Preview**: Open the template in a separate popup to view its details.
  * **Import**: Import the template into your current instance.

### Responses

The **Responses** tab lists submitted form responses in a table with Form, Version, Delivery, Delivery Date, Completion Status, Completion Date, and Subject columns.

* **Search**: Find a response by Response ID, Subject, or Form.
* **Add filters**: Narrow the list of responses by additional criteria.
* **Actions**:
  * **JSON**: View the response as a FHIR `QuestionnaireResponse` in JSON format.
  * **PDF**: Download the response as a PDF.
  * **Delivery Details**: View delivery information for the response.
  * **Cancel Sending**: Cancel a response that has not yet been delivered.
  * **Delete**: Delete the response.

Package runs appear as expandable rows in **Responses**, including runs whose forms have not yet been opened. The parent row shows package status and delivery information; expanding it shows the individual `QuestionnaireResponse` resources. Click the package title to open the complete run. See [reviewing and amending packages](form-packages.md#reviewing-and-amending-responses).

### Voice Agents

The **Voice Agents** tab is the designer for [Voice Agents](voice-agents.md#design-and-test-an-agent): it writes the same `SDCVoiceAgent` and `SDCVoicePrompt` resources an integration would create itself.

Give the agent a name and a status (Draft or Active), then click **Save changes** once a prompt and a questionnaire have been added.

Design, in the editor tabs:

* **Prompt**: Pick or create an `SDCVoicePrompt` lineage and edit its text. Save as a new version or replace the current one; calls resolve the latest active revision.
* **Questionnaires**: Search and tick the questionnaires the agent should cover. Order is interview order.
* **Context**: Declare the slots the agent needs (name, resource type, optional label).
* **Voice**: Choose the conversation model, speech-to-text, voice, and the extraction model.

Test, from the right-hand panel:

* **Text**: Type at the agent. No speech provider is opened, so it costs only LLM tokens.
* **Browser call**: Talk to the agent through your microphone.
* **Phone call**: Place a real call (asks for confirmation first).

### Configurations

The **Configurations** tab lets you create and edit a [Configuration resource](aidbox-ui-builder-alpha/configuration.md). Use the search field to jump to a specific setting, or navigate the settings grouped into categories in the left-hand sidebar:

* **General**: Basics (name, description, default configuration flag), Presentation (language, theme), and Translations (custom UI translation overrides).
* **Storage**: Account, Container, and Attachment URLs settings for file uploads.
* **External services**: Form store, Terminology, and Data store settings.
* **Builder**: Shell, Flags, and Custom renderers settings for the form builder.
* **Renderer**: Redirects, Display, and Flags settings for the form renderer.

Click **Save** to apply your changes, or **Reset** to discard them.

## Switching to the Legacy UI

Use the **New UI** toggle in the top-right corner of the Forms page to switch back to the legacy UI, or return to the new UI, at any time.

## Legacy UI

The Forms UI consists of three main sections, accessible through tabs at the left side of the page:

* Form Templates
* Responses
* Form Gallery

### **Form Templates**

The **Form Templates** tab is the default view when you navigate to the Forms page. Initially, this page will be empty. Once you create a form or select one from the form gallery, it will appear in the list of templates.

**Actions Available for Form Templates:**

* **Search**: You can search through the list of form templates to quickly find a specific form.
* **Filter**: You can filter the form templates by category, with the following options:
  * **All**: Displays all available forms and components.
  * **Forms**: Displays forms categorized as questionnaires in FHIR Format.
  * **Components:** Displays custom components that have been created and reused in other forms.
* **Preview**: Click to open a form in a separate popup to view its details.
* **Share**: Generate a shareable link to the form that can be sent to others.

**Additional Actions (Accessible via the Three Dots Menu):**

* **Duplicate Form**: Duplicating a form creates a new form with a unique title and URL.
* **Delete Form or Component**: You can delete a form template or component (sub-form) from Form Templates list. At the same time it will be deleted in the database too.
  * When attempting to delete a form or component, the system checks if the form is in use or if there are any responses associated with it. If applicable, you will be prompted to confirm whether you want to proceed with deletion.

### **Responses**

The **Responses** tab displays user responses to your forms. This tab shows the responses in view mode, allowing you to review completed forms without editing the original submission.

All responses are stored in the database in the QuestionnaireResponse resources.

### **Form Gallery**

The **Form Gallery** allows you to browse through available templates. Once selected, the forms from the gallery will be added to your **Form Templates** tab for use.

There, you will discover over 3000 form templates.

These templates can be:

* viewed in preview mode
* imported into your Aidbox Instance

Once you click the import button, the form is saved to the database and opens in the Form Builder.
