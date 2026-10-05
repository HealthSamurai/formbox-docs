---
description: This article outlines the the UI Builder Interface
---

# Form Builder Interface

## Layouts

The Form Builder comes in two layouts. Both edit the same questionnaire with the same tools — they differ in what is on screen at once and in how much of the work you are expected to do by hand. The names below are the ones in the **Layout** dropdown.

| Layout | Arrangement | Best for |
| --- | --- | --- |
| **Modern** | Element tree on the left, form preview in the middle, item settings on the right. The default. | Everyday form building and heavy manual editing. |
| **AI first** | Form preview on the left, chat with the AI Assistant on the right. The element tree, the settings and the debug panel stay hidden until they are needed. | Building and changing forms by describing what you want. |

Both layouts share the same item settings and widgets, described on [Form Settings](form-creation/form-settings.md) and [Widgets](form-creation/widgets.md).

### Switching the layout

Open the **…** menu in the top-right corner, choose **Settings**, and pick a **Layout**. The change applies immediately, without a reload, and is stored in your browser — it is a personal preference and does not travel with the form.

Embedders can set the layout for everyone through the `builder.layout` field of [SDCConfig](configuration.md): `v2` for the standard layout, `ai` for AI first.

{% content-ref %}
[AI first layout](ai-first-layout.md)
{% endcontent-ref %}

## UI Builder interface overview

The description below follows the standard layout.

When creating a form in the UI builder, the interface includes the following components:

1. **Work Area ( left Side ):**
   * Displays the form outline, showing the structure of the form and the properties of the form and its widgets and components.
   * Users can add, remove, or modify form widgets and components.
2. **Form Preview ( right Side ):**
   * Shows the form itself, reflecting changes made in the form outline in real-time.
   * Users can test this form by filling it out directly.
3. **Debug Console ( at the bottom** ) **:**
   * Debug console allows users to view the Questionnaire resource. Each form is presented as a Questionnaire resource and saved in a database.
   * Users can view the QuestionnaireResponse resource. Data from the form is saved in a QuestionnaireResponse resource in a database.
   * Pre-filling the form with existing data is possible for testing purposes on Population tab.
   * Users can view how data will be extracted to other FHIR resources on Extraction tab.
   * Named Expressions tab displays all variables that are used in the form.
4. **Toolbar ( at the top** )**:**
   * The toolbar contains buttons for various actions.
   * Users can view a preview of the form, set a theme for the form, save the form, and access additional actions.
   *   The toolbar provides tools to help visualize how your form will be displayed across various devices. You can select one of the **available device options** such as desktop, tablet, or mobile.

       Alternatively, you can manually set the **page width** to preview how the form adapts to different dimensions.

This interface provides users with the tools needed to create, test, and manage forms effectively.

## Multilingual UI Builder Interface

If you need support for another language in the UI builder interface or if you are embedding the UI Builder into your application that is used by users from different countries, Formbox provides support for multilingual interface.

To enable this:

1. Click on the Action bar (three dots) in the UI builder and select the "Settings" option.
2. After appearing the settings page, select the language for the UI builder interface.

## List of supported languages

* Chinese `zh`
* Chinese (Hong Kong) `zh-HK`
* Chinese (Taiwan) `zh-TW`
* Croatian `hr`
* Czech `cs`
* Danish `da`
* Dutch `nl`
* Dutch (Belgium) `nl-BE`
* English `en`
* English (US) `en-US`
* English (UK) `en-GB`
* English (Australia) `en-AU`
* English (Canada) `en-CA`
* Finnish `fi`
* French `fr`
* French (Canada) `fr-CA`
* French (Belgium) `fr-BE`
* French (Switzerland) `fr-CH`
* German `de`
* German (Austria) `de-AT`
* German (Switzerland) `de-CH`
* Hungarian `hu`
* Italian `it`
* Italian (Switzerland) `it-CH`
* Japanese `ja`
* Korean `ko`
* Polish `pl`
* Russian `ru`
* Spanish `es`
* Spanish (Mexico) `es-MX`
* Spanish (Argentina) `es-AR`
* Spanish (Colombia) `es-CO`
* Spanish (Spain) `es-ES`

Additional languages can be added upon request.
