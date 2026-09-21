# Data storage and API

Formbox uses an Aidbox server under the hood for storing forms, responses, and related configuration. This server exposes a limited API surface tailored to Formbox use cases.

For general platform details, see the [Aidbox database documentation](https://www.health-samurai.io/docs/aidbox/database/overview) and the [Aidbox API overview](https://www.health-samurai.io/docs/aidbox/api/api-overview). Formbox-specific operations are documented in the [FHIR SDC API](reference/fhir-sdc-api.md) and [Custom SDC API](reference/aidbox-sdc-api.md) reference sections.

## Supported API operations

Formbox supports standard FHIR REST API operations for the resource types listed below, including `create`, `read`, `update`, `delete`, and `search`, where applicable.

Additional custom operations are documented in the [FHIR SDC API](reference/fhir-sdc-api.md) and [Custom SDC API](reference/aidbox-sdc-api.md) reference sections.

{% hint style="info" %}
`populate` and `extract` workflows are also supported. To use them, you need either to configure integration with an [external FHIR server](aidbox-ui-builder-alpha/external-fhir-servers-as-a-data-backend.md) where the source or target data is stored, or to upgrade to the full Aidbox platform and use Formbox as an Aidbox module so that the remaining FHIR resources required by these workflows can be stored and managed in Aidbox.
{% endhint %}

The following resource list applies to the standalone Formbox product. Formbox running as an Aidbox module also has access to the underlying Aidbox FHIR API.

## Package storage <a id="plan-definition-storage"></a>

[Form packages](plan-definitions.md) use `PlanDefinition` for the definition, `RequestGroup` for each run, and `Task` to connect each form to its `QuestionnaireResponse`. These resources are supported in both standalone Formbox and Formbox running as an Aidbox module.

Packages honor the configured [external storage](aidbox-ui-builder-alpha/external-fhir-servers-as-a-data-backend.md#plan-definition-storage-requirements). `PlanDefinition`, `RequestGroup`, `Task`, responses, and extraction results use `data-store`; questionnaires use `form-store`. Package writes use FHIR transaction bundles. The configured data store commits or rolls back each bundle, for both local and external storage. See the [Form packages API](reference/plan-definition-api.md).

## FHIR resources

```text
Organization
Questionnaire
QuestionnaireResponse
PlanDefinition
RequestGroup
Task
Library
ValueSet
CodeSystem
AuditEvent
ViewDefinition
```

## Formbox system resources

```text
OpenEHRTemplate
QuestionnaireTheme
SDCConfig
SDCWorkflowVersion
SDCPrintTemplate
SDCDocument
SDCAddendum
SDCFormVersion
SDCWorkflow
SDCFormMetadata
```

## Aidbox system resources

```text
TokenIntrospector
AidboxTrigger
DisabledIndex
SubsSubscription
AzureContainer
LoaderFile
AidboxSubscription
AidboxQuery
App
AidboxSubscriptionTopic
AidboxConfig
Hl7v2Config
GcpServiceAccount
TerminologyBundleFile
Concept
AidboxJobStatus
Module
Hl7v2Message
Operation
User
FtrConfig
BulkExportStatus
Attribute
AccessPolicy
AuthConfig
AidboxTask
Role
Grant
AidboxWorkflow
SearchQuery
Mapping
AidboxTaskLog
AidboxLinkageModel
AidboxArchive
PGSequence
AidboxSubscriptionStatus
Lambda
SubsNotification
AzureAccount
IndexCreationJob
SchedulerRuleStatus
FlatImportStatus
SeedImport
ConceptMapRule
Entity
NotificationTemplate
WebPushSubscription
Registration
AwsAccount
AidboxProfile
BatchValidationRun
AidboxMigration
AidboxJob
Notification
IdentityProvider
ui_history
Session
BulkImportStatus
Notebook
Search
Scope
Client
BatchValidationError
AidboxTopicDestination
```
