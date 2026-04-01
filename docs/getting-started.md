---
description: This article outlines the basic steps to get started with Formbox as an Aidbox module.
---

# Getting started (module)

This guide explains how to start using **Formbox as an Aidbox module**. In this deployment model, Formbox runs on top of Aidbox and uses Aidbox as the underlying FHIR server. Form-building and form-rendering capabilities are the same as in the standalone deployment model.

Use this guide if you want to:

- explore Formbox in an Aidbox-hosted sandbox;
- run Formbox locally as part of an Aidbox project;
- open the Form Builder and start creating or editing forms.

If you want to run Formbox as a separate product instead, see [Getting started (standalone)](getting-started-formbox.md).

## Before you start
{% hint style="info" %}
Formbox was previously known as **Aidbox Forms**. Some UI labels, page names, or older documentation may still use the previous name.
{% endhint %}

As a module of Aidbox, Formbox provides access to Aidbox capabilities, including:

- native FHIR storage;
- pre-fill and data extraction workflows, where data is read from and written directly to Aidbox;
- SQL-on-FHIR and advanced queries across FHIR resources;
- full FHIR API support.

## What you should expect

After completing this guide, you should be able to:

- launch and open Formbox in the browser;
- navigate to the Forms section;
- open the Form Builder;

---

## Option 1. Run Formbox in sandbox

This option is useful if you want to explore Formbox without running Aidbox locally. The current documentation describes the sandbox path through the Aidbox Portal. 

### 1. Create a license in Aidbox Portal

1. Go to [Aidbox Portal](https://aidbox.app/ui/portal#/signin).
2. Sign up for a new account or sign in to an existing one. You will be redirected to the Projects page. 
3. Click on **New License** in the right upper corner to create new a license with:
   - **Product**: `Aidbox`
   - **License type**: `production` or `development`
   - **License name**: any name you prefer
   - **Hosting**: `Sandbox`
4. Select the Aidbox and FHIR versions for your sandbox instance:
 
   **Aidbox version**:
   
   `Edge` — the newest version, where new features become available first.
   
   `Latest` — the stable version, recommended if you prefer fewer defects and a more predictable setup.

   **FHIR version**:

   Choose the version your company or organization already uses for storing and exchanging healthcare data.

   If you do not have a required version yet, `4.0.1` (FHIR R4) is a common default choice.

5. Click 'Create' to create a new Formbox instance. This may take a few minutes.

### 2. Open Formbox

After the successful initialization of the new Formbox instance, you will be redirected to the starting page of the Aidbox Portal.

In the Aidbox web console on the left, click `Aidbox Forms` to launch Formbox.

At this point, you can start exploring Formbox and continue to the Form Builder workflow described below.

The next time you log in to the portal, after signing in successfully, follow these steps to access your previously created Formbox instance:
1. Find your license in the Personal project licenses list.
2. Open the license.
3. In the Hosting section, click the URL link.

---

## Option 2. Run Formbox locally

Use this option if you want to run Formbox as part of a local Aidbox setup. 

### 1. Create a license in Aidbox Portal

1. Go to [Aidbox Portal](https://aidbox.app/ui/portal#/signin).
2. Sign up for a new account or sign in to an existing one.
3. Click on **New License** in the right upper corner to create new a license with:
   - **Product**: `Aidbox`
   - **License type**: `production` or `development`
   - **License name**: any name you prefer
   - **Hosting**: `Self-hosted`
4. Select the FHIR version for your sandbox instance:

   **FHIR version**:
   Choose the version your company or organization already uses for storing and exchanging healthcare data.
   If you do not have a required version yet, `4.0.1` (FHIR R4) is a common default choice.

{% hint style="info" %}
In this setup, Aidbox version is set to Edge, as this is currently the only available option.
{% endhint %}

5. Click `Create` to initialize the process of the new Aidbox instance creation. 

### 2. Start Aidbox locally

1. Follow steps 1–3 from the Aidbox guide to run a local Aidbox instance:

- [Run Aidbox locally](https://www.health-samurai.io/docs/aidbox/getting-started/run-aidbox-locally)

2. Open `http://localhost:8080` in your browser and enter your login and password indicated on the Summary page.

{% hint style="info" %}
After the creation of your Aidbox instance complete, you can immediately find the login and password 
for it in the Aidbox Portal on the Product Summary page (you will be redirected to it automatically after clicking on `Create`).  
If you cannot find it, go to the **My Projects** page, open the newly created Aidbox instance, and view the credentials there.
{% endhint %}

### 3. Open Formbox and start designing forms

After Aidbox is running locally and you are signed in:

1. In the Aidbox web console on the left, click `Aidbox Forms` or follow the direct link: `http://localhost:8080/ui/sdc` to open Formbox.
2. Click `Create Template`.
3. Click `Create in UI Builder`.

From there, you have two options:

- **Create a form in UI Builder from scratch**
- **Create a form from an existing FHIR Questionnaire** using import functionality.

 The next steps are described here:
[Design form in Aidbox UI Builder](aidbox-ui-builder-alpha/)

---

## Enable Audit log

Formbox support [Audit logs](https://www.health-samurai.io/docs/aidbox/access-control/audit-and-logging).

To enable Audit logging follow this [guide](https://www.health-samurai.io/docs/aidbox/tutorials/security-access-control-tutorials/how-to-configure-audit-log)

## Disable SDC operations

In case you have conflicts with your own implementation, you can easily disable SDC operations by setting environment variable:

```
AIDBOX_SDC_ENABLED=false
```
