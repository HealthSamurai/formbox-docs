---
description: Learn how to create consent questions in FHIR SDC forms and ensure users explicitly confirm consent before submitting a form.
---

# How to create consent questions

## Overview

When building consent questions in medical forms based on FHIR R4 and SDC, it is important to understand how required boolean fields behave.

A common example is a consent checkbox such as:

> I confirm that I have read and understood the information provided.

At first glance, it may seem that marking this checkbox as `required = true` should force the user to click it before submitting the form.

However, for a boolean checkbox in FHIR, this is not always the case.

This behavior is not a Formbox-specific decision or a custom implementation choice. It follows the way boolean answers are represented according to the FHIR specification.

## How boolean questions work in FHIR

In FHIR, a boolean question can conceptually have three possible states:

- unanswered
- answered true
- answered false

This is important because `false` is still a valid answer. It does not mean that the question is unanswered.

For example, if a checkbox has the following configuration:

```json
{
  "type": "boolean",
  "required": true,
  "initial": [
    {
      "valueBoolean": false
    }
  ]
}
```

then the item already has an initial answer: `false`.

As a result, the checkbox behaves like a regular two-state checkbox:

`false / true`

There is no separate unanswered state in the checkbox item.

For a boolean item, `required = true` means that the item must have an answer, but it does not necessarily mean that the user must click the checkbox or the value must be true.

So if the user opens the form, leaves the required checkbox untouched, and submits the form, the system may still have a valid answer for that item:

```json
{
  "valueBoolean": false
}
```

From the FHIR/SDC perspective, this is a **valid** answered boolean value.

This means the form can be submitted because the required item is not empty: it contains the answer false.

## Why this matters for consent questions

For consent questions, the expected business behavior is often different.

Usually, when we create a consent checkbox, we do not simply want the user to provide any boolean answer. We want the user to actively confirm the statement.

In other words, the business requirement is not "the question must have an answer".

The real requirement is "the user must explicitly accept the consent".

These are different requirements. A required boolean checkbox with a default value of `false` only guarantees that the question has an answer. It does not guarantee that the user interacted with the checkbox or accepted the consent.

## Recommended workaround

For consent questions where the user must actively confirm acceptance, we recommend using a **Checkbox list** instead of a regular boolean Checkbox item.

The Checkbox list should contain only one answer option.

For example:

> Checkbox list heading:
> Data processing consent
>
> Answer option:
> I consent to the processing of my personal and health information as described in this form.

Then make the Checkbox list required:

```json
{
  "type": "choice",
  "required": true,
  "repeats": true
}
```

With this approach, the user cannot submit the form without selecting the checkbox. This is because a required Checkbox list means that the user must select at least one available answer option. Since the list contains only one option, the user must actively click that option before submitting the form.

## Why this solution works

This approach better matches the expected consent behavior.

A boolean checkbox answers the question: "Is this true or false?"

A required Checkbox list with one option answers the question: "Has the user selected the required confirmation option?".

That makes it a better fit for consent-style questions where explicit user action is required.

Finally, this solution does not conflict with the FHIR SDC specification. It stays aligned with FHIR SDC semantics while providing a simple and effective workaround for cases where the user must explicitly accept a consent statement before submitting the form. 
