# AIN Apex Architecture Documentation

## Overview

The AIN package contains **106 Apex classes** (verified against `manifest/package.xml`, `ApexClass` type, on `master`), of which **43 are test classes** (`*Test.cls` naming convention). The remaining 63 classes implement REST/callout services, trigger handlers, batch/queueable jobs, Lightning controllers, and shared utilities. There is **no abstract `AIN_IIntegrationService`/factory/retry-handler framework** — each external system (Beeline, Indeed, RedDot) has its own dedicated controller/service classes; there is no generic polymorphic service layer.

## Apex Class Summary

| Category | Representative Classes | Notes |
|---|---|---|
| **Triggers & Handlers** | `AIN_TriggerHandler` (base), `AIN_ApplicantTriggerHandler`, `AIN_JobTriggerHandler`, `AIN_JBATriggerHandler` | One handler per trigger; trigger files are one line (`new Handler().run();`) |
| **Trigger Helpers** | `AIN_BeelineCandidateTriggerHelper`, `AIN_IndeedPostingTriggerHelper` | Called from handlers; implement the actual sync logic |
| **Beeline Integration** | `AIN_BeelineCalloutServices`, `AIN_BeelineJobController`, `AIN_BeelineCandidateController`, `AIN_BeelineCandidatePostingBatch`, `AIN_BeelineCandidatePostingService`, `AIN_BeelineJobAttachmentBatch`, `AIN_BeelineAuthSettingController`, `AIN_BeelineObjectMappingController` | OAuth2 client-credentials auth, REST callouts |
| **Indeed Integration** | `AIN_IndeedCalloutService`, `AIN_IndeedApplyRestResource`, `AIN_IndeedApplyController`, `AIN_IndeedAuthSettingController`, `AIN_IndeedOAuthController`, `AIN_IndeedOAuthCallbackController`, `AIN_IndeedPostingBatch`, `AIN_IndeedQuestionBuilder`, `AIN_GraphQLGenerator`, `AIN_SyncActiveJob` | GraphQL API, OAuth2 authorization-code flow (`ExternalClientApplication`/`NamedCredential`) |
| **RedDot Integration** | `AIN_RedDotAuthSettingController`, `AIN_RedDotJobController`, `AIN_RedDotJobControllerWrapper`, `AIN_ReddotJobPublishController` | REST endpoint `/redDotJob/*`; webhook-driven posting-location/source tracking |
| **Disposition Sync (Indeed)** | `AIN_DispositionSyncService`, `AIN_DispositionSyncHelper`, `AIN_DispositionSyncQueueable`, `AIN_DispositionMappingController`, `AIN_IndeedDispositionMockGenerator` | Pushes ATS Applicant/Stage status to Indeed `partnerDispositionSync`; fully metadata-driven via `AIN_Disposition_Sync_Config__mdt` / `AIN_Disposition_Value_Map__mdt`, no hardcoded field names |
| **Webhook Framework** | `AIN_WebhookController`, `AIN_WebhookConfigurationController`, `AIN_PlatformEventController`, `AIN_WebhookDeleteBatch`, `AIN_WebhookRetryBatch`, `AIN_RetryWebhookController` | Durable webhook request log (`AIN_Webhook_Request__c`) + platform event (`AIN_Webhook_Event__e`) + scheduled cleanup/retry |
| **Screening/Question Builder** | `AIN_ScreeningQuestionController`, `AIN_ScreeningQuestionURLController` | Manages `AIN_Candidate_Question__c` / `AIN_Question_Assignment__c`; served via Visualforce page `AIN_ScreeningQuestionURLPage` for guest-accessible question URLs |
| **Object/Field Mapping Admin** | `AIN_ObjectMappingController`, `AIN_BeelineObjectMappingController`, `AIN_CandidateController`, `AIN_SourceAndDistributionController` | Backs the field-mapping configuration LWCs |
| **Security / Setup** | `AIN_PermissionSetController`, `AIN_PostInstallScript`, `AIN_SecurityAuditService` | Runtime permission set creation/assignment (no static `.permissionset` metadata exists) |
| **Utilities** | `AIN_Utility`, `AIN_Constants`, `AIN_JsonFlattener`, `AIN_JsonUnflattener`, `AIN_RequestParser`, `AIN_MetadataService`, `AIN_SelectOptionWrapper`, `AIN_GraphQLGenerator` | Shared helpers; `AIN_Constants` holds ~300 named constants (endpoints, field-map keys, error-message maps) |
| **Mocks (test support, shipped)** | `AIN_MockGenerator`, `AIN_BeelineMockGenerator`, `AIN_IndeedDispositionMockGenerator`, `AIN_ExternalCredentialMock`, `AIN_TestFactory` | `HttpCalloutMock` implementations used across test classes |
| **Test Classes** | 43 classes, one per production class following `*Test` suffix | See package.xml `ApexClass` section for the full alphabetical list |

## Architecture Notes (corrections vs. prior documentation)

- There is **no `AIN_FieldMappingService`, `AIN_AuthenticationService`, `AIN_SyncService`, `AIN_ValidationService`, `AIN_RetryHandler`, `AIN_MetadataCache`, `AIN_IntegrationFactory`, `AIN_ActivityLogger`, or `AIN_ErrorHandler` class in this package.** These were fabricated in earlier documentation. Error logging uses `bpats.ATS_LogHandler` (base package); retries are handled by dedicated batch classes (`AIN_WebhookRetryBatch`) rather than a generic retry utility.
- There is **no `AIN_JobTriggerHandler.beforeDelete()`** cleanup method referenced in old docs — `AIN_JobObjectTrigger` fires on `before insert, after insert, before update` only (no delete events).
- `AIN_TriggerHandler` is the real base class (template-method pattern: `run()` dispatches to `beforeInsert()`/`afterInsert()`/etc., overridden per handler) — this part of prior documentation was accurate.
- `AIN_Constants` is a single large constants class (not split into endpoints/error-codes/field-mappings sub-classes as previously described); it also owns the `PERMISSION_SET_MAP` and `ATS_CLASS_PERMISSION` list consumed by `AIN_PermissionSetController`.

## Key Trigger Architecture

| Trigger | Object | Events | Handler |
|---|---|---|---|
| `AIN_JobObjectTrigger` | `Job__c` | before insert, after insert, before update | `AIN_JobTriggerHandler` |
| `AIN_ApplicantObjectTrigger` | `ATS_Applicant__c` | after insert, after update | `AIN_ApplicantTriggerHandler` → delegates to `AIN_BeelineCandidateTriggerHelper` when Beeline sync is enabled |
| `AIN_JBAObjectTrigger` | `Job_Board_Application__c` | after insert, after update | `AIN_JBATriggerHandler` |

## Disposition Sync Design (new since prior docs)

`AIN_DispositionSyncService` is explicitly documented in-code as metadata-driven with **no hardcoded SF or Indeed field names** — all field resolution comes from `AIN_Disposition_Sync_Config__mdt` (which object/SF field maps to which Indeed GraphQL field, semicolon-delimited per-object) and `AIN_Disposition_Value_Map__mdt` (SF picklist value → Indeed disposition status, stored as JSON keyed by `object:field`). It builds nested GraphQL input payloads (`buildNestedPayload`) and supports Stage-vs-Applicant status priority resolution. `AIN_DispositionSyncQueueable` performs the actual dynamic SOQL + GraphQL callout; `AIN_DispositionSyncHelper` is the trigger/invocation entry point.

## Webhook Framework Design (new since prior docs)

- Inbound webhooks from Beeline (`/services/apexrest/webhook/*/receive`), Indeed Apply, and RedDot (`/services/apexrest/redDotJob/*`) are first persisted to `AIN_Webhook_Request__c` (status, retry count, error message, raw reference id) for durability.
- `AIN_PlatformEventController` exposes an `@InvocableMethod` ("Webhook Platform Event") that the `AIN_Webhook_Platform_Event_Flow` flow calls to process `AIN_Webhook_Event__e` platform event messages asynchronously, decoupling webhook receipt from processing.
- `AIN_WebhookRetryBatch` re-attempts `Failure`/`Pending` webhook requests on a schedule, re-dispatching to the Beeline/RedDot/Indeed-specific handler based on `AIN_Platform_Name__c`.
- `AIN_WebhookDeleteBatch` (also `Schedulable`) purges old webhook request records per `AIN_Webhook_Setup__mdt` retention configuration.
- `AIN_RetryWebhookController` backs the `Retry_Webhooks` quick action on `AIN_Webhook_Request__c` for manual retry from the record page.

## Security Patterns (verified in code)

- All DML goes through `bpats.ATS_SecureDml.performSecureDML` / `bpats.ATS_DataUtility.performDML`; no bare `insert`/`update`/`delete` in production classes reviewed.
- `@AuraEnabled` methods wrap logic in try/catch and rethrow via `bpats.ATS_Utility.createAuraHandledException`.
- Dynamic SOQL (used throughout for metadata-driven field mapping) is built with `String.escapeSingleQuotes()` and executed via `bpats.ATS_SecureDml.getSecurityEnforcedQueryResult(Database.query(...))`.
- `AIN_PermissionSetController.getFieldsMapList()` dynamically discovers permissionable fields per object (excluding augmented/person-account/address compound-field suffixes) to build field-level security grants at install/update time rather than relying on static profile/permission-set XML.

## Summary

The AIN Apex architecture is a metadata-driven, multi-integration framework (Beeline, Indeed, RedDot) layered on top of a durable webhook request/retry subsystem, with programmatic (not static) permission set provisioning. The architecture has grown substantially since the package was last documented (2025-07): disposition sync-back, the screening/demographic question builder, and the webhook durability framework are all new, verified additions.
