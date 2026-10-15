---
description: Display Choice and Open Choice answer options in multiple columns using SDC choiceColumn.
---

# How to display choice options in columns

Use **Choice columns** when a drop-down option needs more context than one label can show. For example, a diagnosis picker can show both the code and the description, or a patient picker can show first name, family name, and birth year.

Choice columns are stored as the SDC `sdc-questionnaire-choiceColumn` extension on a `choice`, `open-choice`, or reference-backed item.

## Configure columns in the Builder

1. Add a **Choice** or **Open Choice** widget.
2. Configure the option source: **Static**, **ValueSet**, or **Expression**.
3. In **Choice columns**, add one row for each column.
4. Fill in:
   - **Path** - FHIRPath evaluated against each option, for example `code`, `display`, or `name.first().family`.
   - **Label** - optional column header.
   - **Width** - optional width in pixels or percent.
   - **For display** - use this column as the selected value's display text.
5. Save and preview the form. Open the drop-down to see the columns.

## Example: condition picker

This example is based on the `choice-column-demo` form. The user sees two columns in the diagnosis drop-down: SNOMED CT code and description.

```yaml
resourceType: Questionnaire
id: choice-column-demo
status: active
item:
- linkId: condition
  text: Condition
  type: choice
  answerValueSet: http://hl7.org/fhir/ValueSet/condition-code
  extension:
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-preferredTerminologyServer
    valueUrl: https://tx5.health-samurai.io/fhir
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-choiceColumn
    extension:
    - url: path
      valueString: code
    - url: label
      valueString: SCTID
    - url: width
      valueQuantity:
        value: 20
        system: http://unitsofmeasure.org
        code: '%'
        unit: '%'
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-choiceColumn
    extension:
    - url: path
      valueString: display
    - url: label
      valueString: Description
    - url: forDisplay
      valueBoolean: true
```

The drop-down is displayed like this:

```text
+-----------+-----------------------------+
| SCTID     | Description                 |
+-----------+-----------------------------+
| 4855003   | Retinopathy due to diabe... |
| 237619009 | Maternally inherited dia... |
+-----------+-----------------------------+
```

Because **For display** is enabled on the `display` column, the selected answer keeps the terminology display text:

```yaml
answer:
- valueCoding:
    system: http://snomed.info/sct
    code: '4855003'
    display: Retinopathy due to diabetes mellitus
```

## Example: patient picker

Choice columns also work for reference-backed options. This patient picker shows given name, family name, and birth year.

```yaml
- linkId: patient
  text: Patient
  type: reference
  extension:
  - url: http://hl7.org/fhir/StructureDefinition/questionnaire-referenceResource
    valueCode: Patient
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-choiceColumn
    extension:
    - url: path
      valueString: name.first().given.first()
    - url: label
      valueString: Given
    - url: forDisplay
      valueBoolean: true
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-choiceColumn
    extension:
    - url: path
      valueString: name.first().family
    - url: label
      valueString: Family
    - url: forDisplay
      valueBoolean: true
  - url: http://hl7.org/fhir/uv/sdc/StructureDefinition/sdc-questionnaire-choiceColumn
    extension:
    - url: path
      valueString: birthDate.toString().substring(0, 4)
    - url: label
      valueString: Born
```

When the user selects `Patient/pt-parker`, the answer is still a normal FHIR reference. The columns only control how options are displayed.

```yaml
answer:
- valueReference:
    reference: Patient/pt-parker
    display: Peter Parker
```

## Notes

- Choice columns affect the drop-down display, not the answer type.
- A column without **Path** is ignored.
- **For display** controls the `display` text stored for the selected Coding or Reference.
- This is different from **Column count** on Radio Button and Checkbox List. Column count only changes option layout; Choice columns show fields from each option.
