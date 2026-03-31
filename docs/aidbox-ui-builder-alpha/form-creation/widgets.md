# Widgets

Formbox supports multiple widget types for building forms. Widgets define how questions, layout elements, and supporting content are rendered and configured in the builder. The descriptions below follow the previous documentation style, updated to match the new UI.

## Description of widgets

| Widget | Description | FHIR / Custom |
| --- | --- | --- |
| Header | A container-like item with no direct answer that should have child items. It may have an empty label. Supported in sequential mode, in the NHS theme, and works with Pages. | FHIR |
| Footer | A container-like item with no direct answer that should have child items. It may have an empty label. Works the same way as Header, is always displayed, and is supported in sequential mode, in the NHS theme, and with Pages. | FHIR |
| Group | An item with no direct answer but should have at least one child item. | FHIR |
| Group table | Questions within the group are displayed in a table-like structure. | FHIR |
| Grid | Child items of `type='group'` within a Grid are rows, and questions beneath the row groups are organized as columns in the grid. The grid may be fully populated, but could also be sparse. Questions may support different data types and different answer choices. | FHIR |
| Choice matrix | Questions within the group are arranged as a matrix. In the new UI, values are added in a child item, then the item can be duplicated and renamed to build the matrix structure. | FHIR |
| Pages | Indicates that the content within the group should appear as a logical page when rendering the form, such that all enabled items within the page are displayed at once, but items in subsequent groups are not displayed until the user moves to the next page. | FHIR |
| Tab Container | Indicates that the group represents a collection of tabs. Child items are displayed within separate tab panels. | FHIR |
| Display | A non-answer item used to display static or dynamically generated text, instructions, or contextual information in the form. | FHIR |
| Text | A widget for entering short free-text answers, usually a few words or a brief sentence. | FHIR |
| Textarea | A widget for entering long free-text answers, including multi-line or multi-paragraph text. | FHIR |
| URL | A widget for entering a URL value, such as a website or other link. | FHIR |
| Integer | A widget for entering whole-number values. | FHIR |
| Decimal | A widget for entering rational numbers with decimal precision. | FHIR |
| Quantity | A widget for entering a numeric value together with a unit. It can be used for measurements such as temperature, weight, height, and similar values. | FHIR |
| Slider | A widget for selecting a numeric value on a visual axis between a minimum and maximum. | FHIR |
| Date | A widget for entering a date value. | FHIR |
| DateTime | A widget for entering a date and time value. | FHIR |
| Time | A widget for entering a time value independent of a date. | FHIR |
| Choice | A widget for selecting one or more options from a predefined list. It is displayed as a drop-down list. | FHIR |
| Open Choice | A widget for selecting an option from a predefined list or entering a custom answer. It is displayed as a drop-down list. | FHIR |
| Radio Button | A widget for selecting one option from a predefined set of choices. | FHIR |
| Checkbox List | A widget for selecting multiple options from a predefined list of choices. | FHIR |
| Checkbox | A widget for boolean input with checked and unchecked states, optionally supporting an indeterminate state. | FHIR |
| Attachment | A widget for uploading files such as documents, images, audio, video, and other supported file types. | FHIR |
| Signature | A widget for capturing a handwritten signature. | Custom |
| Annotation Pad | A widget for drawing or writing annotations directly in the form, optionally over a background image. | Custom |
| Speech to text | A widget for entering text by speaking. Spoken input is transcribed into text and stored as the answer value. | Custom |
| Reference | A widget for selecting a reference to another FHIR resource, such as Patient, Practitioner, Appointment, or CarePlan. | FHIR |


In the new UI, most widgets are organized into up to four sections:

- `General`
- `Behaviour`
- `Data`
- `Custom`

Many widgets display the same common settings in the builder, even if some of them are not practically relevant for every widget type.

## Common widget settings

Most widgets in the new UI share the same common settings.

### General

#### Description

- **Label** — main question or instruction text displayed to the user.
- **Short name** — shortened label used in narrow layouts, such as on mobile devices.
- **Placeholder** — activated via a toggle. Defines example text shown inside the input.
- **Show Tooltip** — activated via a toggle. Displays an info tooltip for the item.
- **External Link** — activated via a toggle. Displays an external link icon that opens the provided URL in a new tab.
- **Prefix** — activated via a toggle. Adds a short label shown before the item, such as numbering or a short prefix.

#### Appearance

- **Width**
  - Full width
  - 1/2
  - 1/3
  - 1/4
  - 1/6
  - 1/12
- **Media (image or video)** — public URL of an image or video displayed alongside the item.

#### Basic Interaction

These settings are usually activated via toggles:

- **Required**
- **Allow multiple entries**
- **Read-only**
- **Hidden**
- **Collapsible**

When **Collapsible** is enabled, the default state can be set to:

- **Collapsed**
- **Expanded**

#### Metadata

- **Code**
  - System
  - Code
  - Display

Available actions:

- **Add code**
- **Import code**

### Behaviour

Most answer-based widgets support **Enable When**.

Available modes:

- **Conditions are met**
- **Expression is true**

### Data

Most answer-based widgets support the following data settings:

- **Populate on Load**
  - Default value
  - Observation
  - Expression
- **Prefill with calculated expression**
- **Data Extraction**
  - Definition
  - Observation
  - Template

### Custom

Most widgets support **Custom Attributes**:

- **URL**
- **Type**
- **Value**

---

In addition to the general settings described above, each item type provides its own type-specific settings. 
The following sections provide a brief overview of each item type and describe the type-specific settings available for it.

## Header

The Header widget is a container-like widget used for grouping nested items. It can also be described as a widget based on FHIR `type: "group"`.

A Header item must contain child items. It may have an empty label.

Header is supported in standard mode and **sequential mode**, works with all themes including the *mobile* and the **NHS theme**, works with **Pages**, and is always displayed (if added).

## Footer

The Footer widget works the same way as Header and uses the same settings.

A Footer item must contain child items. It may have an empty label.

Footer is supported in standard mode and **sequential mode**, works with all themes including the *mobile* and the **NHS theme**, works with **Pages**, and is always displayed (if added).

## Text

The Text widget is used for capturing short free-text answers, usually a few words or a brief sentence. It is suitable for concise inputs such as names, short labels, or short descriptions. 

### Input

- **Min Length**
- **Max Length**
- **Input constraints (RegEx)**

## Textarea

The Textarea widget is used for long free-text responses, including multi-line or multi-paragraph input. It is suitable for detailed notes, comments, and narrative descriptions. 

### Input

- **Min Length**
- **Max Length**
- **Input constraints (RegEx)**

## URL

The URL widget is used for capturing a URL value, such as a website or other link.

### Input

- **Min Length**
- **Max Length**
- **Input constraints (RegEx)**

## Integer

The Integer widget is used for capturing whole numbers. 

### Input

- **Min Length**
- **Max Length**
- **Input constraints (RegEx)**

### Behaviour

Integer supports **Enable When**.

Available modes:

- **Conditions are met**
- **Expression is true**

### Data

Integer supports:

- **Populate on Load**
  - Default value
  - Observation
  - Expression
- **Prefill with calculated expression**
- **Data Extraction**
  - Definition
  - Observation
  - Template

### Custom

Integer supports **Custom Attributes**.

## Decimal

The Decimal widget is used for capturing rational numbers with decimal precision. It is suitable for measurements, scores, and other numeric values that require a decimal representation. 

### Input

- **Unit**
  - System
  - Code
  - Display
- **Min**
- **Max**
- **Max Decimal Places**

#### Max Decimal Places
Defines the maximum number of digits allowed after the decimal separator.

## Quantity

The Quantity widget is used for capturing a quantity value that combines a numeric value with a unit. It can be used for measurements such as temperature, weight, height, and similar values.

### Input

- **Unit Options**
- **Min**
- **Max**

#### Unit Options
Defines the list of units available for this field.

Each unit option row supports:

- System
- Code
- Display

Available action:

- **Add unit option**

These unit options are available to the user in the input field. For example, the user can choose one of several units such as `F` or `C`.

## Slider

The Slider widget is a control where an axis is displayed between a minimum and maximum value and the user can visually select a value on that axis. 

### Input

- **Unit**
  - System
  - Code
  - Display
- **Min Integer**
- **Max Integer**
- **Step**

#### Step
Defines the slider increment.

For example, if the step is set to `1`, the user can select every integer value in the range. If the step is set to `5` or `10`, intermediate values between those increments are not selectable.

## Date

The Date widget is used for capturing a date answer. 

### Input

- **Min**
- **Max**

Both values are configured using a date picker.

## DateTime

The DateTime widget is used for capturing a date and time answer.

### Input

- **Min**
- **Max**

Both values are configured using a datetime picker.

## Time

The Time widget is used for capturing a time answer independent of a date.

### Input

- **Min**
- **Max**

Both values are configured using a time picker.

## Choice

The Choice widget allows users to select one or more options from a predefined list of options and is displayed on the form as a drop-down list. 

### Basic Interaction

- **Allow multiple entries** controls whether the user can select more than one option.

### Input

Options are configured under **Options**.

Available modes:

- **Static**
- **ValueSet**
- **Expression**

#### Static

A list of values that the user can choose from.

Each option row supports:

- **System**
- **Code**
- **Display**

Additional option columns can be enabled:

- **Prefix**
- **Exclusive**
- **Score**

Available actions:

- **Add option**
- **Import option**

Additional toggles:

- **Include score**
- **Show score**
- **Include prefix**
- **Include exclusiveness**

#### ValueSet

Use predefined options from a ValueSet.

This mode supports:

- **Use external terminology server**
- **Terminology Server**
- **ValueSet**

#### Expression

Use an expression to define answer options dynamically.

## Open Choice

The Open Choice widget allows users to select from a predefined list of options or enter their own custom answer. It is displayed on the form as a drop-down list.

### Basic Interaction

- **Allow multiple entries** controls whether the user can select more than one option.

### Input

Options are configured under **Options**.

Available modes:

- **Static**
- **ValueSet**
- **Expression**

#### Static

A list of values that the user can choose from.

Each option row supports:

- **System**
- **Code**
- **Display**

Additional option columns can be enabled:

- **Prefix**
- **Exclusive**
- **Score**

Available actions:

- **Add option**
- **Import option**

Additional toggles:

- **Include score**
- **Show score**
- **Include prefix**
- **Include exclusiveness**

#### ValueSet

Use predefined options from a ValueSet.

This mode supports:

- **Use external terminology server**
- **Terminology Server**
- **ValueSet**

#### Expression

Use an expression to define answer options dynamically.

#### Open choice label

Defines the label displayed for the custom answer option.

This field is available for **Open Choice** and can be used for labels such as `Specify other...`.

## Radio Button

The Radio Button widget allows users to select one option from a predefined set of choices. It is suitable for single-choice questions.

### Appearance

In addition to standard appearance settings, Radio Button supports:

- **Orientation**
  - Horizontal
  - Vertical

### Input

Options are configured under **Options**.

Available modes:

- **Static**
- **ValueSet**
- **Expression**

Additional setting:

- **Column count**

#### Static

Each option row supports:

- System
- Code
- Display

#### ValueSet

Use predefined options from a ValueSet.

#### Expression

Use an expression to define answer options dynamically.

#### Column count

Defines how many columns are used to render the answer options.

## Checkbox List

The Checkbox List widget allows users to select multiple options from a predefined list. It is suitable for multi-select questions. 

### Appearance

In addition to standard appearance settings, Checkbox List supports:

- **Orientation**
  - Horizontal
  - Vertical

### Input

Options are configured under **Options**.

Available modes:

- **Static**
- **ValueSet**
- **Expression**

Additional setting:

- **Column count**

#### Static

Each option row supports:

- System
- Code
- Display

#### ValueSet

Use predefined options from a ValueSet.

#### Expression

Use an expression to define answer options dynamically.

#### Column count

Defines how many columns are used to render the answer options.

## Checkbox

The Checkbox widget allows users to toggle between checked and unchecked states. It is suitable for simple boolean answers such as yes/no or true/false.

### Appearance

In addition to standard appearance settings, Checkbox supports:

- **Orientation**
  - Horizontal
  - Vertical

### Input

- **Tri-state**

#### Tri-state
Enables a third, indeterminate state in addition to checked and unchecked.

## Display

The Display widget is used to show text in the form without capturing an answer. It is intended for static or dynamically generated content that provides context, explanation, or instructions to the user. 

In the new UI, Display has a reduced set of sections compared to answer-based widgets.

Available sections:

- `General`
- `Behaviour`
- `Custom`

There is no `Data` section for Display.

### General

Display supports:

- Label
- Short name
- External Link
- Prefix
- Dynamic text
- Width
- Media (image or video)
- Hidden
- Metadata → Code

#### Dynamic text

When **Dynamic text** is enabled, the item text can be built dynamically using a **FHIRPath expression**.

The new UI provides:

- **FHIRPath expression** editor
- **Compact mode**
- **Visual mode**

### Behaviour

Display supports **Enable When** with the same two modes:

- Conditions are met
- Expression is true

## Attachment

The Attachment widget allows users to upload files as part of a form response. It can be used for documents, images, audio, video, and other supported file types. 

### Input

- **Allowed file-types**
- **Max file size (in kilobytes)**

#### Allowed file-types

The new UI provides predefined options such as:

- Any image
- Any audio
- Any video
- PDF
- Microsoft Word (.doc)
- Microsoft Word (.docx)
- Microsoft Excel (.xls)
- Microsoft Excel (.xlsx)
- CSV
- Specify other...

These correspond to MIME types such as:

- `image/*`
- `audio/*`
- `video/*`
- `application/pdf`
- `application/msword`
- `application/vnd.openxmlformats-officedocument.wordprocessingml.document`
- `application/vnd.ms-excel`
- `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`
- `text/csv`

## Signature

The Signature widget is used for capturing a handwritten signature as part of the form response.

No additional Signature-specific settings are shown in the new UI.

## Annotation Pad

The Annotation Pad widget allows users to draw or write annotations directly in the form. It can be used for sketches, handwritten notes, markings, and other visual input that cannot be easily captured with standard text fields. 

### Input

- **Background image**
- **Pen Color**
- **Pen Thickness**
- **Erase Option**

#### Background image

Allows uploading an image file to be used as the background of the annotation area.

#### Pen Color

Defines the color used for annotation.

#### Pen Thickness

Defines the stroke thickness used for drawing.

#### Erase Option

Allows erasing annotation content.

## Speech to text

The Speech to text widget allows users to input text by speaking. The spoken input is transcribed into text and stored as the answer value.

### Input

- **Min Length**
- **Max Length**
- **Input constraints (RegEx)**
- **Language**

#### Language

Defines the language used for speech recognition and transcription.

The language is selected from the list of languages supported by Formbox.

## Reference

The Reference widget allows users to select a reference to another resource, such as a Patient, Practitioner, Appointment, CarePlan, and other FHIR resources. 

### Input

Reference-specific settings are grouped under **Options**.

Available modes:

- **Resource Type**
- **Expression**

#### Resource Type

Use this mode to define which FHIR resource type is available for selection.

Only one resource type can be selected.

The field opens a dropdown with supported FHIR resources, such as:

- Patient
- Practitioner
- Appointment
- CarePlan

and other available resource types.

#### Expression

Use an expression when the available references should be determined dynamically.

## Group

The Group widget is an item that does not capture a direct answer and is used to organize child items inside a form. It is intended for grouping related questions or sections together.

Group acts as a container for nested items rather than as a direct input field.

## Group Table

The Group Table widget is used to organize nested groups in a tabular structure. It acts as a container widget for related grouped content displayed in table-like form. 

## Grid

The Grid widget is used to arrange nested group items in a grid layout. It is intended for structured layouts where child groups act as rows and nested items are organized as columns.

## Choice Matrix

The Choice Matrix widget is used to display a matrix of questions and answer choices, where questions can be arranged in rows or columns. It is suitable for tabular choice-based layouts. 

Choice Matrix works through nested items. Values are added in a child item, then the item can be duplicated and renamed to build a matrix structure.

### Appearance

In addition to standard appearance settings, Choice Matrix supports:

- **Display questions**
  - In rows
  - In columns

#### Display questions

Defines how matrix questions are displayed in the table layout.

- **In rows** — questions are displayed as rows.
- **In columns** — questions are displayed as columns.

## Pages

The Pages widget is a container widget used to split a form into multiple pages or sections. It is intended for multi-step form layouts where child items are grouped into separate pages. 

## Tab Container

The Tab Container widget is a container widget used to split nested content into separate tabs. It is intended for tabbed layouts where child items are displayed within different tab panels. 
