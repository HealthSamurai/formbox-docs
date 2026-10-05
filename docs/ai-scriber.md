---
description: Fill and correct forms by speaking, follow the Scriber cursor, and resolve questions in Agent notes.
---

# AI Scriber

Use AI Scriber when completing patient intake, documenting a consultation, or recording follow-up arrangements in Formbox. Speak the details you want to capture as you work through the encounter, then review the answers and clarify any missing information before submitting the form.

The Scriber works quietly. Its cursor shows where it is acting, and **Agent notes** identify updates that need clarification.

## Set up the Scriber

An administrator must enable **Scriber** in **Configurations → Renderer → Flags** for the configuration used by your form. The setting is disabled by default. For resource-based configuration, set `form.enable-scriber` to `true` in an [SDCConfig resource](aidbox-ui-builder-alpha/configuration.md#configuration-resource-structure).

To use a custom configuration in a shared link, select it in **Share → SDC Config** or pass its reference when [generating the link](reference/aidbox-sdc-api.md#generate-a-link-to-a-questionnaireresponse-generate-link).

Before starting, open the arrow beside the microphone, select **AI settings**, and configure:

| Tab | What it controls |
| --- | --- |
| **Text** | The AI provider, model, and credentials used to interpret your words and act on the form. |
| **Speech** | The provider, API key, and model for live transcription. OpenAI Compatible also requires a Base URL. |
| **Classification** | Optional Jev configuration through OpenRouter, used to distinguish corrections from separate instructions while the Scriber is working. |

Click **Save**. The speech providers available in the settings are **OpenAI Compatible**, **Deepgram**, and **ElevenLabs**. Use the credentials and endpoint supplied for your deployment.

<img src="../assets/ai-scriber-settings.avif" alt="AI Settings with Text, Speech, and Classification tabs" width="522">

AI settings, including API keys, are stored in this browser for the Formbox site. They are shared with other Formbox AI features on the same site. Audio goes to the selected speech provider; transcripts and form context go to the configured text provider. If classification is configured, relevant instructions also go to OpenRouter. See [How AI Scriber works](ai-scriber-internals.md#data-and-storage) for details.

## Fill a form by speaking

The following example uses a form with **Patient name**, **Visit date**, and **Follow-up date** fields. Use the field and section names from your own form.

1. **Start listening.** Click the microphone and allow browser microphone access when prompted. The control shows **Connecting…** during startup. A chime and the Scriber cursor appear when listening is ready.
2. **Supply the facts.** Say, for example, “Patient name is Alex Morgan. The visit date is October fifth, 2026.” The transcript appears beside the cursor as speech is recognized. The Scriber moves to the matching fields and enters the answers.
3. **Correct an answer.** Say, “Correction: the visit date is October sixth, 2026.” You can speak a correction while earlier instructions are being processed. Check the resulting answer in the form.
4. **Resolve an unclear update.** If you say “Follow up next week,” the Scriber may need an exact day. Open **Agent notes** beside the microphone and select the question to move to its field. Then supply the clarification, such as “The follow-up date is October thirteenth, 2026.” The note clears when the Scriber supplies the answer. You can also withdraw the original request.
5. **Check the form.** Say “Check this form,” then review the validation messages. Fill any missing required answers and check again. Submit using the form's normal controls, or explicitly ask the Scriber to submit when you are ready.

<img src="../assets/ai-scriber-notes.avif" alt="A synthetic form with an unresolved follow-up question in Agent notes" width="810">

Agent notes belong to the open form session. They stay associated with their fields when you navigate within a package. Editing a field manually can leave its note open until the Scriber resolves or withdraws the original request.

## Follow the work and control listening

While you speak, the cursor's bubble shows the transcript. After a phrase is finalized, the bubble becomes a number showing instructions still being handled, including active work. It counts instructions rather than remaining fields or clicks.

The cursor moves to inputs and controls as updates are applied. When the Scriber is idle and you move your pointer, its cursor merges into yours and fades away. It appears again when speech or work resumes. A hidden cursor does not mean the microphone is muted; check the microphone control.

* **Mute microphone** pauses audio input while queued instructions can continue. **Unmute microphone** resumes listening in the same session.
* **Stop scribing** stops listening and cancels outstanding agent work. Answers already applied remain in the form. After stopping, the arrow menu is available again for changing AI settings.

## Navigate and choose terminology

Ask for a destination using its name: “Open the follow-up section,” “Go to page three,” or “Open Follow-up arrangements.” The Scriber can move between pages and forms in a [form package](plan-definitions.md). Asking to open a section requests navigation; include the facts you want entered when you also need an update.

For terminology fields, the Scriber searches the field's **ValueSet** and selects a returned **Coding**. You can use a clinical synonym rather than the exact display text. If the meaning is ambiguous or no suitable code is found, it can leave a field note for you to resolve. Review the selected concept as you would a manually entered answer.

“Check this form” validates the open form. To check several forms, name them or ask to check both forms in the package, then review each form's validation result. Validation leaves the response open for editing. Saving and submission follow the form's configured behavior; stopping the Scriber is not a save or submit action.

## Troubleshooting

| What you see | What to do |
| --- | --- |
| No microphone control | Check that the configuration used by this form enables Scriber. |
| **Save** is disabled in AI Settings | Check the required provider, model, and credential fields in all configured tabs. Save is also disabled when nothing has changed. |
| A microphone or connection error | Check browser microphone permission and the Speech provider settings, then start listening again. |
| A transcript differs from what you said | Restate the correct fact and review the updated field. |
| Work is complete but a note remains | Open **Agent notes** and supply the missing detail or withdraw the request. |
| An error appears beside the controls | Read the error, check the relevant provider or form connection, and retry the instruction after correcting the cause. |

For the browser architecture, tool behavior, and answer evidence, see [How AI Scriber works](ai-scriber-internals.md).
