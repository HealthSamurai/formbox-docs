# Formbox data storage and API

Formbox uses an Aidbox server under the hood for storing forms, responses, and related configuration. This server exposes a limited API surface tailored to Formbox use cases.

For general platform details, see the [Aidbox database documentation](https://www.health-samurai.io/docs/aidbox/database/overview) and the [Aidbox API overview](https://www.health-samurai.io/docs/aidbox/api/api-overview). Formbox-specific operations are documented in the [FHIR SDC API](reference/fhir-sdc-api.md) and [Custom SDC API](reference/aidbox-sdc-api.md) reference sections.

## Supported API operations

Formbox supports standard FHIR REST API operations for the resource types listed below, including `create`, `read`, `update`, `delete`, and `search`, where applicable.

Additional custom operations are documented in the [FHIR SDC API](reference/fhir-sdc-api.md) and [Custom SDC API](reference/aidbox-sdc-api.md) reference sections.

Only the following resource types are available through the API.

## FHIR resources

```text
Organization
Questionnaire
QuestionnaireResponse
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
