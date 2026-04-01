# Form sending

Formbox (Aidbox Forms) supports sending a form to a patient by email, either manually or as part of an automated workflow. This feature is designed for collecting data before or after a visit.

## Overview

The **Send form** feature lets a practitioner choose a form, enter or confirm patient details, and send the form link by email. It also supports delayed delivery, reminders, and practitioner notifications based on the form completion status.

Typical use cases include:

- sending intake or consent forms before a visit
- sending follow-up questionnaires after surgery
- collecting PROMs after an appointment
- reminding patients to complete a form if they have not responded

## Before you start

To use this feature, an email provider must be configured in Aidbox. Without a provider, forms cannot be delivered by email.

{% hint style="info" %}
You can configure any supported email provider described in the Aidbox documentation:

- [Email providers integration](https://www.health-samurai.io/docs/aidbox/modules/integration-toolkit/email-providers#email-providers-integration)
- [Postmark integration tutorial](https://www.health-samurai.io/docs/aidbox/tutorials/integration-toolkit-tutorials/postmark-integration-tutorial)
- [Mailgun integration tutorial](https://www.health-samurai.io/docs/aidbox/tutorials/integration-toolkit-tutorials/mailgun-integration-tutorial)
- [Sendgrid integration tutorial](https://www.health-samurai.io/docs/aidbox/tutorials/integration-toolkit-tutorials/sendgrid-integration-tutorial)
{% endhint %}

If you are using this feature in a sandbox environment, you can review the email provider settings in the general settings.

## How it works

The feature supports the following trigger types:

- **Manual trigger** - a user selects a form, enters patient contact details, and sends the form immediately or schedules it.
- **Event-based trigger** - for example, when an appointment is created.
- **Time-based trigger** - for example, on a specific date or N days before a visit.

The workflow can perform the following actions:

- send a form by email
- resend the form after N days if it has not been completed
- notify the practitioner when the form is completed
- stop the workflow and notify the practitioner if the form is still not completed after X days
- cancel the workflow if the related appointment is canceled

## Send a form manually

To send a form manually:

1. Open Formbox.
2. Go to the Forms grid where all user-created and imported forms are stored.
3. Find the required form.
4. Click **Send** next to that form.
5. In the **Send form** dialog, fill in the required fields.
6. Click **Send form**.

## Send form dialog

The **Send form** dialog includes the following fields.

### Main fields

- **Form** - the selected form definition.
- **Patient** - choose a patient.
- **Recipient email** - the email address where the form link will be sent. If the selected patient resource contains an email address, this field is populated automatically.
- **Subject** - the email subject. If not specified, the placeholder text is used.
- **Message** - the email body. Template variables such as the patient first name can be used. If not specified, the placeholder text is used.
- **Date and Time** - optionally schedule form delivery. If the send date and time are not specified, the form is sent immediately.
- **Enable follow-up reminders** - send reminders if the form is not completed.
- **Form expiration (days)** - how long the form stays active.
- **Practitioner's email** - email address that receives notifications when the patient completes or misses the form.

### Follow-up reminders

When **Enable follow-up reminders** is turned on, the following reminder settings are available:

- **Enable follow-up reminders** - enables reminder emails for patients who have not completed the form.
- **Reminder after (days)** - the number of days after the initial email when the reminder should be sent.
- **Send at time (24h)** - the time of day when the reminder email should be sent.
- **Reminder message** - the email body used for the reminder. Template variables can be used.

The reminder is sent only if the form is still incomplete at the scheduled time.

### Advanced options

The **Advanced options** section includes optional settings:

- **Encounter** - link the form delivery to a specific encounter.
- **Theme** - choose the visual theme for the form.
- **SDC Config** - choose the SDC configuration used for rendering and behavior.

## Notes on form sending

- The patient receives emails when the form is initially sent, when a follow-up reminder is sent (if reminders are enabled), and when the form expires without being completed.
- The practitioner receives emails if the form was completed, and if the form was not completed and expired.
- If the form is completed, the practitioner email is not sent immediately. It is sent later, when the expiration time is reached and the scheduled task checks whether the response was submitted.
- Sending can be stopped manually, and the related form session can be closed if needed.

## Email templates

Two email templates are used in this workflow:

- **Patient email template** - sent to the patient when the form is initially sent, when a follow-up reminder is sent (if enabled), and when the form expires without completion.
- **Practitioner email template** - sent to the practitioner if the form was completed, or if the form expired without being completed.

The patient email contains a button with a link to open the form.
The practitioner email contains a summary of the form status and a link to open the response.

## Tracking delivery and completion

After sending a form, you can monitor its delivery and completion in **Questionnaire Responses** section in Formbox Grid:

```text
http://localhost:8080/ui/sdc#/responses
```

This section includes delivery and completion information for each response.

### Delivery statuses

The **Delivery** column shows the email delivery status:

- **queued** - sending is scheduled for a future date or time
- **pending** - sending is in progress
- **sent** - the email was sent successfully
- **failed** - the email could not be sent

### Completion statuses

A separate **Completion status** column shows the current response state:

- **Completed** - the patient completed the form
- **Amended** - the form was completed and later edited
- **In progress** - the form has been started but not fully completed
- **stopped** - the form expired without completion, or completion is no longer possible because delivery failed

## Workflow behavior

The form sending workflow follows this general flow:

1. The workflow starts manually, by date, or by event.
2. The form is sent to the patient by email.
3. The system checks whether the form has been completed.
4. If the form is completed, this status is recorded and checked again at expiration time.
5. If the form is not completed, a follow-up email can be sent.
6. At expiration time, the scheduled task checks response status and sends the practitioner notification (completed or expired without completion).

## Summary

Use **Send form** to deliver Formbox forms to patients by email, either immediately or on a schedule. The feature supports reminders, completion tracking, and practitioner notifications, making it suitable for pre-visit and post-visit workflows.