---
description: Configure Questionnaire properties in Formbox including title, canonical URL, version, status, and metadata.
---

# Form Settings

Form Settings are organized into four tabs:

- General — main settings that control the appearance and behavior of the medical form.
- Attribution — metadata, company contact information, and details about the form creator.
- Data — settings related to data population and extraction.
- Custom — custom extensions, such as metadata for external systems or renderers.

## General Settings

In the UI builder interface, users can set general settings for the form. These settings can be accessed:

When opening a form in the UI Builder, users can access the general settings for the form by clicking on the name of the form in the form outline.

This action opens a sidebar where users can modify the form's general settings.

These settings include:

#### Form Title

A short, descriptive, user-friendly title for the questionnaire.

#### URL

This is the id that will be used to link a QuestionnaireResponse to the Questionnaire the response is for. It is used to identify this questionnaire when it is referenced in a specification, model, design or an instance; also called its canonical identifier.

{% hint style="info" %}
URL is generated automatically. A custom prefix can be specified in the Formbox module settings.
{% endhint %}

#### Version

The identifier that is used to identify this version of the questionnaire when it is referenced in a specification, model, design or instance. This is an arbitrary value managed by the questionnaire author and is not expected to be globally unique.

There may be different questionnaires that have the same url but different versions. The version can be appended to the url in a reference to allow a reference to a particular business version of the questionnaire with the format.

{% hint style="info" %}
`Url` and `version` are used as `Questionnaire`uniq identifier - they should be unique globally.
{% endhint %}

For more details, go to the page - [Versioning.](versioning.md)

#### Status

Specifies the current state of the questionnaire.

Supported FHIR `Questionnaire.status` values are: `draft`, `active`, `retired` and `unknown`.
The `unknown` status is part of the FHIR specification, but it is not intended for regular user use.

* `draft` - the form is still under development and may be changed freely.
* `active` - the form is ready for normal use in production. Changes to active forms should be made with caution; minor updates may be acceptable.
* `retired`- the form is no longer intended for active use, but may still be retained for historical or reference purposes. Changes to retired forms should also be made carefully.
* `unknown` - the form status is unknown.

<!-- ### Default Language

This is the default language of the form. Since forms can be multilingual, choosing the default language gives you the opportunity to set the translation of the form in other languages.

### Reusable Context

With this setting, you can specify how the form will be used. If you mark the form as `Component` or a `Form and Component`, then the form will be available in the add widget popup in the components tab and can be included in other forms as component.

* `Form` - This form can be used only as an _independent form_
* `Component` - This form can be used only as an _component_ (included in other forms)
* `Form or Component` - This form can be used only as an _independent form_ or be included in other forms as _component_ -->

#### Entry Mode

Entry Mode defines how questions are shown and navigated in your form. For more detailed information, see [this page](entry-mode.md).

Available options:

* `Random` — All questions are visible, and they can be completed in any order.
* `Sequential` — Questions are shown one at a time, with no ability to change previous answers.
* `Sequential w. edit` — Questions are shown one at a time, and all previously shown questions can be reviewed and edited.

#### Adaptive form

Adaptive form enables a dynamic question flow in your form. Based on the provided answers, the form can display only the most relevant next questions instead of showing all questions at once. For more detailed information, please see [this page](../../adaptive-forms.md).

To specify a server for adaptive form logic, switch to `Adaptive w. external server`. This will display an additional field where you can enter the URL of the external server.

### Appearance

In this section, you can configure the appearance of your forms, ensuring they display correctly across different devices.

#### Max Width:

* By default, the form width is set to **960 pixels**.
* You can configure the **maximum width** of the form based on your requirements. The form will be displayed using the specified maximum width, as long as the device supports that size.
* This value can be defined in **SDCconfig**, in which case it will be applied to all forms by default. If a width is specified directly in an individual form, that value overrides the one defined in **SDCconfig**.

To verify the appearance of the form with the configured maximum width, use the **Form Preview** feature.

#### Show outline

You can turn on the outline and check its appearance in **Form Preview**.
When enabled, an outline containing the top-level groups is displayed on the left side of the form.
This feature is useful for large forms, as it makes navigation easier.

### Multilingual

Use the **Multilingual** option to make a form available in multiple languages.
To enable multilingual support, turn on the toggle. The **Default language** field will then be displayed.
The language selected in this field is used as the default language for the form.

You can also add translations for additional languages.
After selecting the default language, click **Edit translations** to manage translations.
Translations for each field can be added manually or generated using the **AI Assistant**.

### Validation

The `Questionnaire` resource includes a `meta` element that stores metadata used to provide technical and workflow context.

**Profiles** are metadata entries that define additional constraints for the resource.
When a profile is added to a `Questionnaire`, Aidbox validates the resource against that profile during CRUD operations.
Each profile is specified as a canonical URL of a FHIR `StructureDefinition`.
The default value, `http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire`, refers to the **SDC Questionnaire** profile.

### Metadata

The **Metadata** section contains the unique identifier of the `Questionnaire` in the Formbox system.

#### Code

An identifier for this collection of questions in a terminology system such as LOINC. It allows the complete `Questionnaire` resource to be linked to a formal terminology.

#### Tags

The `Questionnaire` resource contains a `meta` element, which is a set of metadata that provides technical and workflow context for the resource.

A tag is a metadata item used to relate resources to specific processes and workflows.

For example, you can use tags to define custom form types and then filter forms by those tags.

#### Derived from

**Derived From (URL)** - the URL of the `Questionnaire` that this questionnaire is based on.

### Reusability

#### Form reusability

The **Reusability** setting defines whether a form can be reused as a component in other forms.

When the reusability toggle is **disabled**, the form cannot be embedded or reused as a component.

When the toggle is **enabled**, the following options become available:

- Component

Use this option when the form is intended to be used **only as a reusable component** inside another form.
A form created in this mode is not intended to be used as a standalone form.

- Form & Component

Use this option when the form should remain available as a **standalone form** and also be reusable as a component in other forms.
This mode allows the same form to be used both independently and as an embedded part of another form.

---

## Attribution

In this section, you can provide additional metadata for the form that may be used when publishing it.

**Publisher** - the name of the organization or individual that published the questionnaire.

**Contact** - contact information that helps users reach the publisher, such as a website, email address, or phone number.

**Description** - a free-text, natural language description of the questionnaire. It can be used to provide additional context, such as the purpose of the questionnaire, usage notes, clinical guidance, interpretation instructions, literature references, or paper-based examples.

**Copyright** - a copyright statement related to the questionnaire and/or its contents. Copyright statements typically describe legal restrictions on the use, distribution, or publication of the questionnaire.

---

## Data

### Populate on Load

To enable data population, turn on the toggle.

This feature allows the form to be pre-filled with data stored in the database. To use it, you need to define the incoming form parameters that will be used to populate specific fields.

For detailed instructions and examples, see the [Population page](population.md) and our [How-to guides](how-to-guides/how-to-populate-forms-with-data.md).

### Data Extraction

To enable data extraction, turn on the toggle.

Two data extraction options are supported at the root level of the `Questionnaire`: **Definition-Based** and **Template-Based**. You can extract data either into an existing resource or into a newly created one.

For detailed instructions and examples, see the [Data Extraction page](data-extraction.md) and our [How-to guides](how-to-guides/how-to-extract-data-from-forms.md).

---

## Custom

### Extensions

Form Builder allows you to add and edit custom extensions that are not supported by the standard form configuration fields. You can also edit the raw `Questionnaire` JSON directly.

The **Extensions editor** includes three fields for adding a custom extension. To add an extension, follow these steps:

1. `URL` — enter the URL of the extension.
2. `Type` — select the type of the extension value. To enter a custom FHIRPath or FHIRQuery `valueExpression`, select `Expression`.
3. `Value` — enter or select the value.

To add another extension, click **Add extension**.

{% hint style="info" %}
Known extensions cannot be added.
{% endhint %}