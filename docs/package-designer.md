---
description: Create, preview, and share form packages with the graph-based Package Designer.
---

# Package Designer

The Package Designer in the new [Formbox UI](aidbox-forms-interface.md#new-ui) lets you arrange questionnaires into a [form package](form-packages.md), add enable-when rules, and prefill answers between forms.

## Create a package

1. Create or import the questionnaires you want to use.
2. Open **Forms**, then open the menu beside **New Form** and choose **New Package**. To start with existing forms, select their checkboxes first and choose **New Package with Selected**.
3. Select the **Package** node at the top of the graph. Enter the package name and review its version, status, canonical URL, description, and author in the bottom panel.
4. Click the round **+** button below the last form to add questionnaires.
5. Drag forms into the required order, configure their rules, and test the flow in **Preview**.
6. Click **Save**.

The [example definition bundle](../assets/form-packages-example.json) contains an Intake questionnaire, a conditional Travel arrangements questionnaire, an independent Review questionnaire, and their package. To load it through the API, follow the [example setup](reference/form-packages-api.md#example-setup).

## Graph

Forms appear on one vertical track. Their numbers show the order used by the renderer. Conditional forms have a **Conditional form** label and a distinct number indicator.

* Drag any part of a form node to change its position.
* Select the package node to edit package settings, or a form node to open **Settings**, **Enable when**, and **Prefill** in the bottom panel.
* Click empty graph space to clear the selection and close the bottom panel. The last form settings tab is remembered when you select another form.
* Use the node's menu to duplicate or remove a form from the package. Duplicating an entry reuses its questionnaire and creates a separate action.
* Pan and zoom to inspect the graph. The controls at the bottom left include fit-to-view and 100% scale.

### Edges and question boxes

The graph draws two kinds of connections:

| Connection | Source | Destination |
| --- | --- | --- |
| Enable when | The source question, or the form header for a condition about any answers in that form | The dependent form |
| Prefill | The source question | The target question in the form being prefilled |

Only questions involved in visible connections appear inside nodes. Use the **Enable when** and **Prefill** legend toggles to hide or show each connection type and its question boxes.

Edges are gray by default. Selecting a form highlights its connected edges. Selecting an edge highlights that edge, opens the matching settings tab on the target form, and briefly highlights the corresponding rule. In advanced mode, the highlight identifies the referenced part of the FHIRPath expression.

Zoom, pan, and edge visibility are saved in the browser for each package.

### Quick look

Click the eye button on a form node to preview that questionnaire in the right panel. The eye button on the package node previews the package. Quick look stays open when the graph selection changes; use its close button to dismiss it.

Package Quick look and the **Preview** tab keep separate answer state.

## Form settings

Select a form, then open **Settings**:

* **Title** is the form's label within the package.
* **ID** is the action ID used by package expressions, such as `%intake`.

The questionnaire attached to an existing entry cannot be changed here. Add another form entry to use a different questionnaire. Edit the questionnaire's questions in the [Form Builder](aidbox-ui-builder-alpha/README.md).

Changing an action ID changes how rules refer to that form. Update any expressions that use the previous ID.

## Enable when

Select the dependent form and open **Enable when**. A form with no condition is always included.

Click **Add condition** and choose:

* **When a question has an answer** — test whether a selected question has an answer or no answer.
* **When a question matches an answer** — compare an answer with a value. Available comparisons depend on the question type.
* **When a form has answers** — test whether another form has any answers or no answers.
* **When every condition is met** — create an AND group.
* **When any condition is met** — create an OR group.

A group surrounds its conditions with a bracket. Click **AND** or **OR** to switch how they are combined. New groups start with two unfilled condition slots; complete those slots before adding more conditions. There is one condition or group at the root, with additional conditions combined inside a group.

Question selectors preserve the questionnaire's item hierarchy, including questions nested inside other questions. Group items show the structure but cannot be selected. Options are grouped by the source form's number and action title. The current form's questions are excluded.

For the example package, select **Travel arrangements**, choose **When a question matches an answer**, then select **Travel assistance needed?**, **equals**, and **True** from Intake.

### Advanced mode

Turn on **Advanced mode** in the bottom panel header to edit FHIRPath directly. For the example:

```fhirpath
%intake.repeat(item).where(linkId = 'needs-travel').answer.value = true
```

Combine inputs using `and` and `or` in the expression. If an imported definition has multiple applicability conditions, each has its own editor and all must be true. Existing entries can be deleted down to the final editor; clearing that editor removes the condition. Advanced mode does not add additional condition entries.

The Designer checks for self-dependencies and circular enable-when dependencies. See [expression variables and result types](reference/form-packages-api.md#expressions).

## Prefill

Select the form receiving the answers and open **Prefill**.

1. Click **Add prefill**.
2. Select the target question in this form.
3. Select the source question in another form.

The row reads **Prefill [target] with [source]**. Use the delete button at the start of a row to remove it. You can add several prefill rows.

Source and target selectors show nested questions. The target selector contains only the current questionnaire's eligible questions; groups and display items cannot receive answers. Targets inside repeating ancestors are not supported.

For the example, prefill **Traveler name** in Travel arrangements with **Full name** from Intake. The visual editor handles supported answer-type conversions and selects the first source answer for a target that does not repeat.

Turn on **Advanced mode** to supply a FHIRPath expression instead. This lets a prefill combine several answers or use other [package variables](reference/form-packages-api.md#expressions). An advanced expression must return values compatible with the target question; a non-repeating target accepts at most one value.

Prefills run on the first opening of a form. Existing answers are preserved on later visits. The Designer rejects a prefill from the current form or from a form whose enable-when rules depend on the current form.

## Preview

The **Preview** tab runs the package using the current definition, including unsaved edits. It keeps answers when you switch between **Graph**, **Preview**, and **Code**. Use **Reset form state** to start a fresh preview, and the width controls to test different screen sizes.

Preview does not create a persisted package run or save its responses. It lets you test conditions, prefills, navigation, and validation without adding entries to **Responses**. Submitting a preview does not perform production extraction.

Test both paths in the example: answer **True** to include Travel arrangements, then reset and answer **False** to skip it. Also test going back and changing an answer after a dependent form has already opened.

## Code

Open **Code** to edit the package's `PlanDefinition` directly. It contains the same forms and rules shown in Graph. Use the [API reference](reference/form-packages-api.md#package-definition) for the supported structure and prefill extension format.

## Save the package

Click **Save** to validate and persist the definition. Errors are shown on the affected form and settings tab.

For an active package, saving opens a confirmation dialog. To preserve its existing definition, change the version or canonical URL and choose **Create New**. **Force Save** changes the existing resource and can affect package runs that reference it.

After saving, the package appears in **Forms** with type **Package**. Expand its row to see the included questionnaires. Use **Share** for a link or **Send** for [email delivery](aidbox-ui-builder-alpha/form-sending.md#sending-a-package). Completed runs and their individual responses appear together in **Responses**.
