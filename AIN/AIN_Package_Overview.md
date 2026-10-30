# AIN (Asymbl Integration) Package Overview

## Executive Summary

The AIN (Asymbl Integration) package is a Salesforce managed package (namespace `bpats`) that extends ASYMBL-ATS with multi-channel candidate communication, external job board/VMS integrations, and a metadata-driven field-mapping framework. It connects Salesforce to **Indeed** (job publishing, Indeed Apply, screening/demographic questions, disposition sync-back), **Beeline** (VMS job/candidate sync), and **RedDot** (job board posting + webhook source tracking), with a durable webhook request/retry subsystem underpinning all three.

**Verified Package Stats (package.xml, `master` branch, API v66.0)**: 106 Apex classes (43 test classes) | 3 Apex triggers | 2 Visualforce pages | 175 custom fields (explicit CustomField entries) | 296 custom labels | 240 custom metadata records across 10 metadata types | 16 CustomObjects (6 true custom objects/platform event + 10 CMDT types) | 31 Lightning Web Components | 3 Custom Tabs | 2 Flows | 9 Layouts | 3 Quick Actions | 2 Remote Site Settings | 1 Static Resource | 1 External Client Application (Indeed OAuth) | 1 External Credential / Named Credential pair | 1 CSP Trusted Site (RedDot)

**No static `PermissionSet` metadata ships in this package.** Access control is built **programmatically at runtime** by `AIN_PermissionSetController` / `AIN_PostInstallScript`, which upsert four permission sets (`AIN_Admin_Clone`, `AIN_User_Clone`, `AIN_Guest_Clone`, `AIN_Api_User_Clone`) via the Permission Set, Object/Field Permissions, and SetupEntityAccess APIs. See `AIN_Permission_Sets_Documentation.md` for details — this materially corrects earlier documentation that described three static `.permissionset-meta.xml` files, none of which exist in the repository.

## Key Business Capabilities

- **Job Publishing**: Indeed (GraphQL), Beeline (REST/OAuth), RedDot (REST) — all driven by `AIN_Job_Mapping__mdt` / `AIN_Vms_Job_Mapping__mdt` records, no hardcoded field names.
- **Inbound Application Processing**: Indeed Apply webhook → `AIN_IndeedApplyRestResource` → `Job_Board_Application__c` + `Contact`/`ATS_Applicant__c`, with resume JSON flattening (`AIN_JsonFlattener`) and screening/demographic question capture (`AIN_Candidate_Question__c`, `AIN_Candidate_Response__c`, `AIN_Question_Assignment__c`).
- **Candidate Submission to VMS**: Beeline candidate posting (`AIN_BeelineCandidatePostingBatch`, `AIN_BeelineCandidatePostingService`) with attachment sync (`AIN_BeelineJobAttachmentBatch`).
- **Disposition Sync-back to Indeed**: `AIN_DispositionSyncService` / `AIN_DispositionSyncQueueable` / `AIN_DispositionSyncHelper` push ATS Applicant/Stage status changes to Indeed's `partnerDispositionSync` GraphQL API, fully configured via `AIN_Disposition_Sync_Config__mdt` and `AIN_Disposition_Value_Map__mdt` — no hardcoded SF or Indeed field names.
- **Durable Webhook Framework**: Inbound webhooks (Beeline, Indeed, RedDot) are logged to `AIN_Webhook_Request__c`, published via the `AIN_Webhook_Event__e` platform event and `AIN_Webhook_Platform_Event_Flow`, and retried by `AIN_WebhookRetryBatch` / `AIN_RetryWebhookController`. Stale/successful logs are purged by `AIN_WebhookDeleteBatch` on a schedule configured in `AIN_Webhook_Setup__mdt`.
- **Screening/Demographic Question Builder**: Admin-configurable question library (`AIN_Candidate_Question__c`) with per-job assignment (`AIN_Question_Assignment__c`), hierarchical/conditional question support, and default templates seeded from `AIN_Default_Question_Template__mdt` on install.
- **External Custom Fields**: `AIN_External_Custom_Fields__c` captures VMS-supplied custom field definitions per job posting (Beeline "candidate custom fields").

## High-Level Architecture

```
┌───────────────────────────────────────────────────────────────────────┐
│                        Salesforce Platform (bpats namespace)          │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │                     AIN Integration Layer                     │   │
│  │  Auth/Credentials   Field Mapping (CMDT)   Webhook Framework   │   │
│  │  Disposition Sync   Question Builder       Job Publishing      │   │
│  │  ┌───────────────────────────────────────────────────────┐    │   │
│  │  │        31 Lightning Web Components (Admin UI)          │    │   │
│  │  └───────────────────────────────────────────────────────┘    │   │
│  └───────────────────────────────────────────────────────────────┘   │
│  ATS Core Objects: Job__c • ATS_Applicant__c • Job_Board_Application__c │
│                     • Contact • ContentVersion • ATS_Log__c           │
└───────────────────────────────────────────────────────────────────────┘
            │                    │                    │
            ▼                    ▼                    ▼
  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
  │   Beeline VMS    │  │  Indeed.com      │  │  RedDot          │
  │ • Job posting    │  │  • GraphQL jobs  │  │ • Job posting    │
  │ • Candidate sync │  │  • Indeed Apply  │  │ • Webhook source │
  │ • OAuth 2.0      │  │  • Disposition   │  │   tracking       │
  │ • Webhooks       │  │    sync-back     │  │ • Webhooks       │
  └─────────────────┘  └─────────────────┘  └─────────────────┘
```

## Component Breakdown (verified against `manifest/package.xml` on `master`)

| Component Type | Count | Notes |
|---|---|---|
| ApexClass | 106 | Includes 43 test classes (`*Test.cls`) |
| ApexTrigger | 3 | `AIN_ApplicantObjectTrigger`, `AIN_JBAObjectTrigger`, `AIN_JobObjectTrigger` |
| ApexPage | 2 | `AIN_IndeedOAuthCallback`, `AIN_ScreeningQuestionURLPage` |
| CustomField | 175 | Explicit field members (see `AIN_Custom_Fields_Documentation.md`) |
| CustomLabel | 296 | See `AIN_Custom_Labels_Documentation.md` |
| CustomMetadata | 240 | Records across 10 CMDT types — see `AIN_Custom_Metadata_Documentation.md` |
| CustomObject | 16 | 6 true objects/platform event (`AIN_Candidate_Question__c`, `AIN_Candidate_Response__c`, `AIN_Question_Assignment__c`, `AIN_External_Custom_Fields__c`, `AIN_Webhook_Request__c`, `AIN_Webhook_Event__e`) + 10 CMDT type definitions |
| CustomTab | 3 | `AIN_External_Custom_Fields__c`, `AIN_Webhook_Request__c`, `Asymbl_Integration_Admin` |
| CustomNotificationType | 1 | `Beeline_Job` |
| Flow | 2 | `AIN_Webhook_Platform_Event_Flow`, `Flow_For_Update_RedDot_Job_Source` |
| FlowDefinition | 1 | `AIN_Webhook_Platform_Event_Flow` |
| Layout | 9 | CMDT/object layouts (no `Job__c`/`ATS_Applicant__c` layout overrides ship in this package) |
| LightningComponentBundle | 31 | See `AIN_Lightning_Components_Documentation.md` |
| ListView | 6 | 4 on custom objects + 1 on `Job__c` (`AIN_VMS_Derived_Jobs`) |
| QuickAction | 3 | `Job__c.Publish_Job`, `Job__c.Publish_Job_RedDot`, `AIN_Webhook_Request__c.Retry_Webhooks` |
| RemoteSiteSetting | 2 | `IndeedBaseUrl`, `IndeedBaseUrl_Simulated` |
| StaticResource | 1 | `AIN_Resources` |
| ExternalClientApplication / ExtlClntAppOauthSettings | 1 each | `Indeed_Integration` OAuth client app |
| ExternalCredential / NamedCredential | 1 each | `AIN_AWS_Indeed_Integration` |
| CspTrustedSite | 1 | `Reddot_JobSplice` |
| **PermissionSet** | **0** | **No static permission set metadata ships — see note above** |

## Integration with Other Packages

### ASYMBL-ATS (bpats) — base dependency
AIN extends `Job__c`, `ATS_Applicant__c`, `Job_Board_Application__c`, `Contact`, `ContentVersion`, and reads/writes `ATS_Log__c` for error logging (via `bpats.ATS_LogHandler`). All DML goes through `bpats.ATS_SecureDml` / `bpats.ATS_DataUtility`. Disposition sync also reads `bpats__ATS_Stage__c` status for applicant status resolution.

### Cross-package notes
No direct code dependency exists from AIN to ASM, AINTL, ARA, or AST in this codebase as of the reviewed commit; any synergy with those packages is conceptual/future, not implemented integration.

## Post-Install Behavior

`AIN_PostInstallScript` (global `InstallHandler`) runs on package install/upgrade and:
1. Upserts the four permission sets and assigns object/field/Apex class/Apex page/custom metadata/tab/named-credential-principal access.
2. Enqueues `cloneQuestionTemplateQueue` to copy `AIN_Default_Question_Template__mdt` records into `AIN_Candidate_Question__c` (pre-selected, default questions for screening).
3. Calls `AIN_IndeedJobSyncScheduler.scheduleDaily()` to schedule the daily Indeed job sync job.

## Documentation Map

This overview is one of twelve companion documents in `ProductDocumentations/AIN/`:
- `AIN_Apex_Architecture.md` — Apex class inventory and layering
- `AIN_Integration_Architecture.md` — Indeed/Beeline/RedDot integration design
- `AIN_Integration_Workflows_Documentation.md` — end-to-end workflow sequences
- `AIN_Custom_Objects_Documentation.md` — custom objects, platform event, CMDT types
- `AIN_Custom_Fields_Documentation.md` — field inventory by object
- `AIN_Custom_Labels_Documentation.md` — label categories
- `AIN_Custom_Metadata_Documentation.md` — CMDT record inventory
- `AIN_Permission_Sets_Documentation.md` — runtime permission set model
- `AIN_Lightning_Components_Documentation.md` — 31 LWC inventory
- `ERD_AIN_Integration_Objects.md` — entity relationship diagram
- `AIN_ULTRATHINK_Verification_Report.md` — component count verification matrix

---

*Documentation last verified against: `asymbl-integration` branch `master`, commit `3d45738` (2026-10-22).*
