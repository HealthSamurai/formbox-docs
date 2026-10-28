---
description: Mark forms and individual questions with confidentiality levels so that Label-based Access Control restricts who can read the responses and answers.
---

# Security labels in forms

Formbox can mark a form, or individual questions in it, with a confidentiality level. Responses to the form carry these labels, and Aidbox [Label-based Access Control](https://www.health-samurai.io/docs/aidbox/access-control/authorization/label-based-access-control) (LBAC) uses them to decide who can read a response and which answers are masked.

Two levels of protection are available:

* **Form level** — the whole response gets the form's confidentiality label. Only users with access to that level can read the response.
* **Item level** — answers to a specific question get the item's label. Users without access to that level can still read the response, but see these answers masked.

{% hint style="info" %}
Labels have an effect only when LBAC is enabled (`security.lbac.enabled`). Requests made by a superadmin are not restricted. See [Label-based Access Control](https://www.health-samurai.io/docs/aidbox/access-control/authorization/label-based-access-control) for how users and tokens get their labels.
{% endhint %}

## Set confidentiality in the builder

Both settings use the [v3-Confidentiality](https://terminology.hl7.org/CodeSystem-v3-Confidentiality.html) levels: Very restricted (`V`), Restricted (`R`), Normal (`N`), Moderate (`M`), Low (`L`), Unrestricted (`U`).

**Form level.** Select the form, open the **Data** tab, and turn on **Confidentiality**. The default level is Normal. The label is written to `Questionnaire.meta.security`; other security labels of the form are kept.

<img src="../assets/security-labels-builder-form.avif" alt="Form Settings, Data tab, with Confidentiality set to Normal" width="800">

**Item level.** Select a question, open the **Data** tab, and turn on **Confidentiality**. The default level is Restricted. The section is not available for display and group items. Turning the switch off removes the label.

<img src="../assets/security-labels-builder-item.avif" alt="Item settings, Data tab, with Confidentiality set to Restricted for PHQ-9 question 9" width="800">

{% hint style="warning" %}
LBAC denies access to a resource with no security labels at all. When you label an item in a form that has no form-level label, the builder sets the form level to Normal, so the responses stay readable.
{% endhint %}

## How labels look in the Questionnaire

The form-level label is a regular security label. The item-level label is the DS4P [inline security label](http://hl7.org/fhir/uv/security-label-ds4p/STU1/StructureDefinition-extension-inline-sec-label.html) extension on the item:

```yaml
resourceType: Questionnaire
meta:
  security:
    - system: http://terminology.hl7.org/CodeSystem/v3-Confidentiality
      code: N
item:
  - linkId: name
    text: Name
    type: string
  - linkId: hiv-status
    text: HIV status
    type: string
    extension:
      - url: http://hl7.org/fhir/uv/security-label-ds4p/StructureDefinition/extension-inline-sec-label
        valueCoding:
          system: http://terminology.hl7.org/CodeSystem/v3-Confidentiality
          code: R
```

## How labels get to the response

Labels are applied every time a response is written through `$save`, `$submit`, `$process-response` or `$populatelink`. The response is brought in line with the current Questionnaire:

* The response's confidentiality label is the form's. If the form has none, the response keeps its own.
* Each answer carries exactly the inline labels of its item. The item itself carries none, so a masked answer still shows the question's `linkId` and text, and the renderer can tell which question is hidden.
* `PROCESSINLINELABEL` is added to `meta.security` while at least one answer is labelled, and removed otherwise. Without it LBAC ignores inline labels.
* Other security labels of the Questionnaire (not confidentiality) describe the form itself and are not copied.

For the form above, a response looks like this:

```yaml
resourceType: QuestionnaireResponse
meta:
  security:
    - system: http://terminology.hl7.org/CodeSystem/v3-Confidentiality
      code: N
    - system: http://terminology.hl7.org/CodeSystem/v3-ActCode
      code: PROCESSINLINELABEL
item:
  - linkId: name
    answer:
      - valueString: John Smith
  - linkId: hiv-status
    answer:
      - valueString: Negative
        extension:
          - url: http://hl7.org/fhir/uv/security-label-ds4p/StructureDefinition/extension-inline-sec-label
            valueCoding:
              system: http://terminology.hl7.org/CodeSystem/v3-Confidentiality
              code: R
```

{% hint style="info" %}
Changing labels in a form does not touch responses that are already stored. They get the new labels the next time they are written.
{% endhint %}

## What users see

A user with access to the item's level sees every answer. In this PHQ-9 response, questions 6 and 9 are labelled Restricted:

<img src="../assets/security-labels-response-full.avif" alt="PHQ-9 response with all answers visible" width="700">

A user with access to Normal only sees the same response with these answers masked. The questions stay in place and show **Access restricted**. Calculated values, such as the total score, are computed from the visible answers only:

<img src="../assets/security-labels-response-masked.avif" alt="PHQ-9 response with answers to questions 6 and 9 shown as Access restricted" width="700">

In the API, a masked answer loses its value and gets the `data-absent-reason` extension with the `masked` code. The response from the example above, as read by a user with access to Normal only:

```yaml
resourceType: QuestionnaireResponse
meta:
  security:
    - system: http://terminology.hl7.org/CodeSystem/v3-Confidentiality
      code: N
    - system: http://terminology.hl7.org/CodeSystem/v3-ActCode
      code: PROCESSINLINELABEL
item:
  - linkId: name
    answer:
      - valueString: John Smith
  - linkId: hiv-status
    answer:
      - extension:
          - url: http://terminology.hl7.org/CodeSystem/data-absent-reason
            valueCode: masked
```

## Editing a response with hidden answers

A user who sees some answers masked can still edit and save the response:

* Answers hidden from the user are restored from the stored response before it is written, so the masked placeholders the user sends back never overwrite real data.
* The operation result is masked for the user the same way as a regular read.
* Adding or removing repeats of an item that holds hidden answers is rejected with a `forbidden` OperationOutcome: repeats are matched by order, so the hidden answers would shift to other repeats. Removing all repeats of such an item is allowed — the hidden answers come back.

## See also

* [Label-based Access Control](https://www.health-samurai.io/docs/aidbox/access-control/authorization/label-based-access-control)
* [Access control in forms](access-control-in-forms.md)
