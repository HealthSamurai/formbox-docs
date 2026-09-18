---
description: >-
  Embedding the Formbox Builder and Renderer into your application using web
  components.
---

# Embedding

## Embedding Formbox Builder and Renderer

You can embed the **Builder** and **Renderer** into your application or website using web components.
The Renderer can also be embedded directly as an iframe using SDC SMART Web Messaging (SWM).

### Renderer in an iframe (SDC SWM)

The Formbox renderer also supports [SDC SMART Web Messaging](https://github.com/brianpos/sdc-smart-web-messaging). You can embed it directly in an iframe and communicate via `postMessage`, without the web component wrapper.

* **Builder** allows users to create and manage forms directly within your application.
* **Renderer** enables users to fill out forms within your application without leaving your platform.

### Step 1: Include the Script

{% tabs %}
{% tab title="Builder" %}
```html
<script src="{{ YOUR_AIDBOX_INSTANCE_BASE_URL }}/static/aidbox-forms-builder-webcomponent.js"></script>
```
{% endtab %}

{% tab title="Renderer" %}
```html
<script src="{{ YOUR_AIDBOX_INSTANCE_BASE_URL }}/static/aidbox-forms-renderer-webcomponent.js"></script>
```
{% endtab %}
{% endtabs %}

### Step 2: Add the Component Tag

{% tabs %}
{% tab title="Builder" %}
```html
<aidbox-form-builder
  style="width: 100%; border: none; align-self: stretch; display: flex"
  form-id="{{ FORM_ID }}"
  hide-back
/>
```
{% endtab %}

{% tab title="Renderer" %}
```html
<aidbox-form-renderer
  style="width: 100%; border: none; align-self: stretch; display: flex"
  questionnaire-id="{{ ID }}"
/>
```
{% endtab %}
{% endtabs %}

### Step 3: Configure Attributes and Properties

The following attributes are available for the Builder and Renderer components.\
These attributes control various aspects of the form’s behavior and appearance, including the form’s layout, customization options, and integration with external systems.

{% tabs %}
{% tab title="Builder" %}
* `form-id` (optional): The ID of the form to load. If not provided, the builder opens with a blank form.
* `hide-back`, `hide-save`, `hide-publish` (optional): Hides respective buttons in the UI.
* `disable-save`, `disable-publish` (optional): Disables respective actions.
* `hide-population`, `hide-extraction` (optional): Hides respective functionalities.
* `collapse-debugger` (optional): Collapses the debugging panel.
* `hide-add-theme`, `hide-edit-theme`, `hide-save-theme` (optional): Controls theme-related UI elements.
* `show-share` (optional): Shows the share button.
* `language` (optional): Default language for the builder.
* `translation-languages` (optional): Comma-separated list of allowed languages.
* `base-url` (optional): The base URL of your Aidbox instance.
* `style` (optional): Custom styling applied to both iframe and loader container.
* `config` (optional): The [configuration](configuration.md) as a JSON string.
* `theme` (optional): Theme settings as a JSON string.
* `token` (optional): JWT token for authentication.
* `disable-load-sdc-config` (optional): Disables automatic loading of SDC configuration.
{% endtab %}

{% tab title="Renderer" %}
* `questionnaire-id` (optional): ID of an individual questionnaire to load.
* `request-group-id` (optional): ID of an existing [package run](../form-packages.md) to load instead of an individual questionnaire.
* `plan-definition` (optional): A PlanDefinition resource as a JSON string to preview a package without saving a run.
* `questionnaire-response-id` (optional): ID of the questionnaire response to load.
* `hide-footer` (optional): Hides the form footer.
* `hide-language-selector` (optional): Hides language selector.
* `base-url` (optional): The base URL of your Aidbox instance.
* `style` (optional): Custom styling applied to both iframe and loader container.
* `config` (optional): The [configuration](configuration.md) as a JSON string.
* `theme` (optional): Theme settings as a JSON string.
* `token` (optional): JWT token for authentication.
* `disable-load-sdc-config` (optional): Disables automatic loading of SDC configuration.
{% endtab %}
{% endtabs %}

Along with the above attributes, you can also set the following properties.

{% hint style="info" %}
Unlike **attributes**, which can only store string values and are defined in the markup,**properties** can hold any JavaScript value (such as objects, arrays, functions, etc.) and must be set programmatically using JavaScript.
{% endhint %}

{% tabs %}
{% tab title="Builder" %}
* `onFetch` (optional): A custom fetch handler that allows you to intercept and modify network requests. The function receives the URL and request options as arguments.
* `onAlert` (optional): A custom alert handler that allows you to intercept and handle alerts, overriding the visual alert display. The function receives the alert object as an argument.
* `onChange` (optional): A custom callback function invoked when the questionnaire is modified, without affecting the default behavior. The function receives the updated questionnaire as an argument.
* `onBack` (optional): A custom callback function that is invoked when the back button is clicked, allowing you to override the default back button behavior.
{% endtab %}

{% tab title="Renderer" %}
* `onFetch` (optional): A custom fetch handler that allows you to intercept and modify network requests. The function receives the URL and request options as arguments.
* `onAlert` (optional): A custom alert handler that allows you to intercept and handle alerts, overriding the visual alert display. The function receives the alert object as an argument.
* `onChange` (optional): A custom callback function that is invoked when the questionnaire response is modified, without affecting the default behavior. The function receives the updated questionnaire response as an argument.
* `onPackageChange` (optional): Receives [package progress](#track-package-progress) when the current form, availability, readiness, or package status changes. The same payload is available as `event.detail` on the `package-change` event.
* `onPreviewAttachment` (optional): A custom callback function that allows you to handle attachment previews, enabling external editors or viewers. The function receives the attachment object as an argument.
* `submit()` (optional): Triggers the same validation and submit flow as the built-in Submit button and returns a Promise. The Promise resolves with the renderer reply payload when submission succeeds and rejects when submission is rejected or fails.
{% endtab %}
{% endtabs %}

Below are examples of how to set properties programmatically:

**onFetch: Intercept Network Requests**

{% tabs %}
{% tab title="Builder" %}
```html
<aidbox-form-builder id="aidbox-form-builder" />

<script>
  const builder = document.getElementById('aidbox-form-builder');

  builder.onFetch = async (url, init) => {
    console.log('Intercepted request', url, init);
    init.headers = {
      ...init.headers,
      Authorization: 'Bearer YOUR_TOKEN'
    };
    return fetch(url, init);
  };
</script>
```
{% endtab %}

{% tab title="Renderer" %}
```html
<aidbox-form-renderer id="aidbox-form-renderer" />

<script>
  const renderer = document.getElementById('aidbox-form-renderer');

  renderer.onFetch = async (url, init) => {
    console.log('Intercepted request', url, init);
    return fetch(url, init);
  };
</script>
```
{% endtab %}
{% endtabs %}

For more complex use cases, such as attaching authorization headers or storing questionnaires locally, refer to the [detailed interception guide](request-interception.md).

**onAlert: Handle Alerts**

{% tabs %}
{% tab title="Builder" %}
```html
<aidbox-form-builder id="aidbox-form-builder" />

<script>
  const builder = document.getElementById('aidbox-form-builder');

  builder.onAlert = (alert) => {
    console.log('Alert received:', alert);
    // e.g., display toast or log error
  };
</script>
```
{% endtab %}

{% tab title="Renderer" %}
```html
<aidbox-form-renderer id="aidbox-form-renderer" />

<script>
  const renderer = document.getElementById('aidbox-form-renderer');

  renderer.onAlert = (alert) => {
    console.log('Alert received:', alert);
    // e.g., display toast or log error
  };
</script>
```
{% endtab %}
{% endtabs %}

#### onPreviewAttachment: show attachments with external editor

Allows to preview attachments in an external editor or viewer.

Return `false` to use default Formbox attachment preview, or `true` to prevent default behavior and show your previewer.

```html
<aidbox-form-renderer id="aidbox-form-renderer" />

<script>
  const renderer = document.getElementById('aidbox-form-renderer');
  
  renderer.onPreviewAttachment = (attachment) => {
    // attachment is an FHIR attachment object
    // e.g., open attachment in external editor
    if(attachment.contentType === "application/dicom") {
      window.open(attachment.data, '_blank'); // if it's a data URL
      window.open(attachment.url, '_blank'); // if it's stored in S3
      return true;
    }
  
    return false;
    
  };
</script>
```

**onChange: React to Form Updates**

{% tabs %}
{% tab title="Builder" %}
```html
<aidbox-form-builder id="aidbox-form-builder" />

<script>
  const builder = document.getElementById('aidbox-form-builder');

  builder.onChange = (questionnaire) => {
    console.log('Updated questionnaire:', questionnaire);
    // e.g., log or analyze the questionnaire
  };
</script>
```
{% endtab %}

{% tab title="Renderer" %}
```html
<aidbox-form-renderer id="aidbox-form-renderer" />

<script>
  const renderer = document.getElementById('aidbox-form-renderer');

  renderer.onChange = (response) => {
    console.log('Updated questionnaire response:', response);
    // e.g., log or analyze the response
  };
</script>
```
{% endtab %}
{% endtabs %}

**onBack: Customize Navigation**

{% tabs %}
{% tab title="Builder" %}
```html
<aidbox-form-builder id="aidbox-form-builder" />

<script>
  const builder = document.getElementById('aidbox-form-builder');

  builder.onBack = () => {
    console.log('Back button clicked');
    // Perform custom navigation
  };
</script>
```
{% endtab %}
{% endtabs %}

**contentHeight: Adjust Component Height**

To ensure the component fits well within your layout, you can get the current content height of the component and adjust its height accordingly. This is particularly useful when embedding the component in a responsive design.

```html
<aidbox-form-renderer id="aidbox-form-renderer" />

<script>
  const renderer = document.getElementById('aidbox-form-renderer');

  console.log("Current content height:", renderer.contentHeight);
</script>
```

Additionally, you can set the height of the component dynamically based on the content height by settings the `style` attribute to auto:

```html
<aidbox-form-renderer id="aidbox-form-renderer" style="height: auto" /> 
```

### Step 4: Optional Custom Loading Content

Both web components support a `loading` slot. You can place any HTML in this slot, from a simple text message to a complex, fully styled loader (for example: spinners, SVG animations, skeleton screens, or branded layouts). The element you place in `slot="loading"` is fully styleable with your own CSS.
The component `style` attribute is applied to both iframe and loader container.

{% tabs %}
{% tab title="Builder" %}
```html
<aidbox-form-builder
  id="aidbox-form-builder"
  style="width: 100%; height: 640px; border: none; display: flex"
  form-id="{{ FORM_ID }}"
>
  <div slot="loading">Loading builder...</div>
</aidbox-form-builder>
```
{% endtab %}

{% tab title="Renderer" %}
```html
<aidbox-form-renderer
  id="aidbox-form-renderer"
  style="width: 100%; height: 640px; border: none; display: flex"
  questionnaire-id="{{ ID }}"
>
  <div slot="loading">Loading form...</div>
</aidbox-form-renderer>
```
{% endtab %}
{% endtabs %}

If needed, you can also observe loading state with events:

```html
<script>
  const builder = document.getElementById('aidbox-form-builder');

  builder.addEventListener('loading', (event) => {
    console.log('Loading state:', event.detail.loading); // true | false
  });

  builder.addEventListener('ready', () => {
    console.log('Builder is ready');
  });
</script>
```

### Step 5: Optional Custom Submit Button

If you use your own submit button outside the renderer component, call `submit()` on the web component instance.
The method uses the same validation and disabled-state rules as the built-in footer submit action.
It returns a Promise with the submission result.

```html
<button id="custom-submit">Submit</button>
<aidbox-form-renderer
  id="aidbox-form-renderer"
  questionnaire-id="{{ ID }}"
  hide-footer
/>

<script>
  const renderer = document.getElementById('aidbox-form-renderer');
  const customSubmit = document.getElementById('custom-submit');

  customSubmit.addEventListener('click', async () => {
    try {
      const result = await renderer.submit();
      console.log('Form submitted:', result);
    } catch (error) {
      console.error('Submission failed:', error.payload || error);
    }
  });
</script>
```

For message-based integrations, `sdc.aidbox.submit` is also supported as an inbound message type.

## Embedding a package

Use the same `<aidbox-form-renderer>` component for a [form package](../form-packages.md). It handles form order, conditional forms, prefills, drafts, and submission. Authentication, configuration, and [request interception](request-interception.md) work as they do for individual questionnaires.

### Open a package run

[Create a run](../reference/form-packages-api.md#start-without-a-link) and pass its RequestGroup ID in `request-group-id`. For a returned reference of `RequestGroup/abc`, use `abc`:

```html
<script src="{{ YOUR_AIDBOX_INSTANCE_BASE_URL }}/static/aidbox-forms-renderer-webcomponent.js"></script>

<aidbox-form-renderer
  id="package-renderer"
  request-group-id="YOUR_REQUEST_GROUP_ID"
  style="width: 100%; height: 640px; border: none; display: flex"
>
  <div slot="loading">Loading package...</div>
</aidbox-form-renderer>
```

Changing `request-group-id` loads the selected run and reinitializes the renderer. The `loading` and `ready` events work as described above.

### Track package progress

```javascript
const renderer = document.getElementById('package-renderer');

renderer.onPackageChange = (progress) => {
  console.log(progress.requestGroup, progress.status, progress.actionId);
  console.log(progress.forms);
};

renderer.onChange = (questionnaireResponse) => {
  console.log('Current form answers:', questionnaireResponse);
};

renderer.addEventListener('submit', (event) => {
  console.log('Completed package:', event.detail);
});
```

`onPackageChange` and the `package-change` event receive the same progress object:

| Field | Meaning |
| --- | --- |
| `requestGroup` | Reference to the saved run, for example `{ "reference": "RequestGroup/abc" }`. |
| `status` | Package status, such as `active` or `completed`. |
| `actionId` | ID of the currently open action, or `null` when no form is open. |
| `forms` | Ordered form summaries containing `actionId`, `title`, `enabled`, `ready`, and optional validation `issues`. Includes disabled forms. |
| `preview` | `true` for a definition preview; a preview has no `requestGroup` reference. |

The `ready` field describes whether a form is valid and confirmed for submission; it is separate from the component's `ready` event. See the [form summary fields](../reference/form-packages-api.md#start-without-a-link).

Use `onChange` to receive the current QuestionnaireResponse as answers change.

### Submit from your application

Call `submit()` for the same flow as the package's built-in Submit button. Wait for the component's `ready` event before calling it; see the [custom submit button example](#step-5-optional-custom-submit-button).

* If the current form is invalid, the renderer stays on it and rejects the Promise with `error.payload.reason` set to `validation-failed`.
* If another enabled form still needs answers or confirmation, the renderer opens it and rejects the Promise with the same reason.
* On success, the Promise resolves with `{ status: "success", package: progress }`. The `submit` event contains the completed package progress in `event.detail`, whether submission starts from your application or the built-in button.

### Preview a package definition

Use `plan-definition` to supply a PlanDefinition as JSON. For example, to switch the component above from a saved run to a preview:

```javascript
renderer.setAttribute('plan-definition', JSON.stringify(planDefinition));
renderer.removeAttribute('request-group-id');
```

Preview loads the referenced questionnaires and evaluates conditions and prefills without saving a package run or responses. Progress includes `preview: true` instead of a RequestGroup reference.

Switching between a saved run and a preview reinitializes the renderer. Once in preview, updating `plan-definition` preserves answers in retained forms:

```javascript
renderer.setAttribute('plan-definition', JSON.stringify(updatedPlanDefinition));
```

If the preview rejects a definition, it emits an `error` event with an OperationOutcome in `event.detail.issues` and keeps the last working preview:

```javascript
renderer.addEventListener('error', (event) => {
  console.error('Package preview could not be updated:', event.detail.issues);
});
```

### Embed a generated link

You can also [generate a package link](../reference/form-packages-api.md#create-a-run-and-link) and embed the returned URL directly:

```html
<iframe title="Form package" src="YOUR_GENERATED_PACKAGE_LINK"></iframe>
```

Keep the full generated URL, including its token. Use `$sdc-package-generate-link` to resume an existing run. For package lifecycle control from your own UI, use the [Form packages API](../reference/form-packages-api.md#manage-the-workflow-in-your-application).

## Controlled Mode (Deprecated)

Controlled mode gives full manual control over loading, updating, and persisting a Questionnaire and QuestionnaireResponse at the application level. The system does not automatically save changes, so the developer must handle data flow and storage. Depending on the use case, making requests to the intended endpoints, as normal mode does, may still be necessary.

This approach is useful when custom validation is required, such as enforcing business rules that go beyond standard validation mechanisms. It is also beneficial when integrating with external systems, where the Questionnaire and QuestionnaireResponse are stored outside Aidbox or when working with multiple data sources. Additionally, it allows fine-grained control over how and when data is fetched, updated, or persisted, making it suitable for applications that need to manage state independently.

{% hint style="warning" %}
Controlled mode is deprecated in favor of request interception, as the latter provides full control over the component’s interaction with Aidbox, extending beyond just Questionnaire and QuestionnaireResponse.
{% endhint %}

### Step 6: Enable Controlled Mode

{% tabs %}
{% tab title="Builder" %}
To enable controlled mode, remove the `form-id` attribute and specify the Questionnaire resource as a JSON string in the `value` attribute.

```html
<aidbox-form-builder
  id="aidbox-form-builder"
  value="YOUR QUESTIONNAIRE AS JSON STRING"
/>
```
{% endtab %}

{% tab title="Renderer" %}
Enable controlled mode by removing the `questionnaire-id` attribute and specifying the Questionnaire resource as a JSON string in the `questionnaire` attribute. Optionally, you can also specify the `questionnaire-response` if needed.

```html
<aidbox-form-renderer
  questionnaire="YOUR QUESTIONNAIRE AS JSON STRING"
  questionnaire-response="YOUR QUESTIONNAIRE RESPONSE AS JSON STRING"
/>
```
{% endtab %}
{% endtabs %}

The following attributes are available for controlled mode:

{% tabs %}
{% tab title="Builder" %}
* `value`: The Questionnaire resource as a JSON string.
{% endtab %}

{% tab title="Renderer" %}
* `questionnaire`: The Questionnaire resource as a JSON string.
* `questionnaire-response` (optional): The QuestionnaireResponse resource as a JSON string.
{% endtab %}
{% endtabs %}

In controlled mode, handling events is essential, as the system no longer manages updates automatically. Developers are responsible for listening to events such as change, save, and submit to track user interactions and manually persist form data.

Below is a list of events you can listen for:

{% tabs %}
{% tab title="Builder" %}
* `change`: Triggered when the form is modified.
* `back`: Emitted when the back button is clicked.
* `save`: Emitted when the form is saved.
* `select`: Emitted when an item is selected.
* `loading`: Emitted when loading state changes. `event.detail.loading` is `true` during load and `false` when done.
* `ready`: Emitted when the builder is loaded.
{% endtab %}

{% tab title="Renderer" %}
* `change`: Triggered when the questionnaire response is updated.
* `extracted`: Emitted when data extraction occurs.
* `loading`: Emitted when loading state changes. `event.detail.loading` is `true` during load and `false` when done.
* `ready`: Emitted when the renderer is loaded.
{% endtab %}
{% endtabs %}

Below is an example of how to listen for `change` event:

{% tabs %}
{% tab title="Builder" %}
```html
<script>
  const builder = document.getElementById('aidbox-form-builder');

  builder.addEventListener('change', (event) => {
    // event.detail contains update questionnaire as json string 
    console.log('Form changed', event.detail);
    // you might want to update update it and set it back as value
    builder.setAttribute('value', JSON.stringify(event.detail));
  });
</script>
```
{% endtab %}

{% tab title="Renderer" %}
```html
<script>
  const renderer = document.getElementById('aidbox-form-renderer');

  renderer.addEventListener('change', (event) => {
    console.log('Questionnaire Response changed', event.detail);
    renderer.setAttribute('questionnaire-response', JSON.stringify(event.detail));
  });
</script>
```
{% endtab %}
{% endtabs %}
