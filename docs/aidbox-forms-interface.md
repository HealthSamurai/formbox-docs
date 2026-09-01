---
description: Navigate Formbox UI including Form Templates, Responses, and Form Gallery with search, filter, and sharing features.
---

# Formbox UI

Formbox has two interface versions: the new Formbox UI, enabled by default, and the legacy UI, which remains available for users who prefer it.

## New UI

The new Formbox UI is organized around a left sidebar with the following sections:

* **Forms**: The default view, showing all form templates in a table with Template Name, Version, Status, Sharing Scope, Type, Updated date, and Author columns. Use **Search** or **Add filters** to narrow the list, and the **Actions** column to preview, share, or run a form. A **New Form** button lets you create a form from scratch or from a template.
* **Library**: Browse and import form templates.
* **Responses**: View submitted form responses.
* **Voice Agents**: Manage voice agents for automated patient calls.
* **Embeds**: Manage embedded forms.
* **Configurations**: Manage Formbox configuration resources.
* **Admin Console**: Access administrative settings.

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
