---
description: This guide explains how to start using Formbox as a standalone product.
---

# Getting started (standalone)

This guide explains how to start using **Formbox as a standalone product**. In this deployment model, Formbox runs independently and is designed specifically for working with **medical forms**. Unlike Formbox as an Aidbox module, the standalone version is not focused on direct work with FHIR resources (such as Encounter, Practitioner, Observation, Patient and etc.).

Use this guide if you want to:

- explore Formbox in a sandbox;
- run Formbox locally as a standalone product;
- sign in and start creating medical forms.

If you want to use Formbox on top of Aidbox and work with FHIR resources stored in Aidbox, see [Getting started (module)](getting-started.md).

## Before you start

{% hint style="info" %}
Formbox was previously known as **Aidbox Forms**. Some UI labels, page names, or older documentation may still use the previous name.
{% endhint %}

As a standalone product, Formbox is intended primarily for:

- creating and managing medical forms;
- designing forms in the visual Form Builder;
- sharing / sending medical forms;

## What you should expect

After completing this guide, you should be able to:

- launch and open Formbox in the browser;
- sign in to your standalone Formbox instance;
- open the Forms section;
- start designing medical forms.

---

## Option 1. Run Formbox in sandbox

This option is useful if you want to explore Formbox without installing Docker or running Formbox locally.

### 1. Open Aidbox Portal

1. Go to [Aidbox Portal](https://aidbox.app).
2. Sign up for a new account or sign in to an existing one.

### 2. Create a Formbox license

1. Click **New License**.
2. Fill in the license details:
   - **Product**: `Formbox`
   - **License type**: `production` or `development`
   - **License name**: any name you prefer
   - **Hosting**: `Sandbox`
3. Select the Aidbox and FHIR versions for your sandbox instance:
 
   **Aidbox version**:

     `Edge` — the newest version, where new features become available first.
   
     `Latest` — the stable version, recommended if you prefer fewer defects and a more predictable setup.

   **FHIR version**:

     Choose the version your company or organization already uses for storing and exchanging healthcare data.

     If you do not have a required version yet, `4.0.1` (FHIR R4) is a common default choice.

5. Click 'Create' to create a new Formbox instance. This may take a few minutes.

After the license is created, wait until the page finishes loading. Once initialization is complete, your Formbox instance will be ready to use.

### 3. Open Formbox

After the product is initialized, you will be redirected to the Product Summary page.

From there, open your Formbox instance using the link shown in the Sandbox window under Instance URL.

Once the new Formbox instance is launched, use the left sidebar to open `Aidbox Forms` and continue working in Formbox.

---

## Option 2. Run Formbox locally

Use this option if you want to run Formbox as a standalone self-hosted product.

### Prerequisites

{% hint style="warning" %}
<img src="../assets/docker.avif" alt="Docker logo" width="240">

Please **make sure** that both [Docker & Docker Compose](https://docs.docker.com/engine/install/) are installed (not required if you're planning to use sandbox).
{% endhint %}

### 1. Open Aidbox Portal

1. Go to [Aidbox Portal](https://aidbox.app).
2. Sign up for a new account or sign in to an existing one.

### 2. Create a Formbox license

1. Click **New License**.
2. Fill in the license details:
   - **Product**: `Formbox`
   - **License type**: choose the type you need
   - **License name**: any name you prefer
   - **Hosting**: `Self-hosted`
3. Click **Create**.

After the license is created, you will be redirected to the Product Summary page.

### 3. Run Formbox locally

On the Product Summary page, copy the command from the **Run me locally** field and run it in your terminal.

After the installation completes successfully, the output will show the local URL where Formbox is running. By default, it is:

`http://localhost:8080`

### 4. Open Formbox in the browser

Copy the local URL from the terminal output and open it in your browser.

### 5. Sign in and start designing forms

1. Enter the login and password shown on the Product Summary page.
2. After a successful sign-in, use the left sidebar to open `Aidbox Forms`.
3. Start creating forms in Formbox.

---

## Alternative local setup

Use this option if you prefer to start Formbox locally with the manual Docker-based flow.

### 1. Create a directory

```bash
mkdir formbox && cd formbox
```

2. Run Formbox on Docker
``` 
curl -JO https://aidbox.app/runme/formbox && docker compose up
```

This command downloads the Formbox startup script and launches Formbox with Docker Compose.

3. Open Formbox by entering the following address in your browser:
```
http://localhost:8080
```

4. Activate your Formbox instance:
  - Click Continue with Aidbox account.
  - Create a free Aidbox account in the user portal if needed.
  - Complete the activation flow for your Formbox instance.

5. Start form designing:
  - Open the Forms section in the browser console.
  - You can also use the direct link:
  ```
  http://localhost:8080/ui/sdc
  ```

## What's next? 

Now that Aidbox is running — either locally or in Sandbox — and you're signed in:

1. In the Aidbox web console on the left, click `Aidbox Forms` or follow the direct link: `http://localhost:8080/ui/sdc` to open Formbox.
2. Click `Create Template`.
3. Click `Create in UI Builder`.

From there, you have two options:

- **Create a form in UI Builder from scratch**
- **Create a form from an existing FHIR Questionnaire** using import functionality.

The next steps are described here: 
[Design form in Aidbox UI Builder](/aidbox-ui-builder-alpha/)
