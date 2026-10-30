# AIN Integration Workflows Documentation

## Overview

This document describes the verified, code-level workflows implemented in the AIN package across Beeline, Indeed, Indeed Apply, and RedDot integrations, plus the webhook durability and disposition-sync-back subsystems added since the package was last documented.

## Workflow Categories

| Workflow | Systems | Direction | Trigger |
|---|---|---|---|
| Job Publishing | Salesforce → Indeed / Beeline / RedDot | Outbound | Quick Action (`Publish_Job`, `Publish_Job_RedDot`) or scheduled batch (`AIN_IndeedPostingBatch`, `AIN_SyncActiveJob`) |
| Indeed Apply Application Processing | Indeed Apply → Salesforce | Inbound | Webhook (`AIN_IndeedApplyRestResource`) |
| Beeline Job/Candidate Webhook Processing | Beeline → Salesforce | Inbound | Webhook (`AIN_WebhookController`) |
| RedDot Source Tracking | RedDot → Salesforce | Inbound | Webhook (`AIN_RedDotJobController`) |
| Candidate Submission to VMS | Salesforce → Beeline | Outbound | Batch (`AIN_BeelineCandidatePostingBatch`) triggered from `ATS_Applicant__c` changes |
| Webhook Retry & Cleanup | Internal | N/A | Scheduled batch (`AIN_WebhookRetryBatch`, `AIN_WebhookDeleteBatch`) or manual Quick Action (`Retry_Webhooks`) |
| Disposition Sync-back | Salesforce → Indeed | Outbound | Queueable (`AIN_DispositionSyncQueueable`), metadata-driven |
| Screening/Demographic Question Assignment | Admin UI → Job | Internal | LWC (`ainQuestionForm`, `ainQuestionTemplateManager`) via `AIN_ScreeningQuestionController` |

## 1. Job Publishing Workflow

### Sequence
```mermaid
sequenceDiagram
    participant U as User
    participant QA as Quick Action
    participant C as Platform Controller
    participant CMDT as AIN_*_Mapping__mdt
    participant E as External System

    U->>QA: Click Publish_Job / Publish_Job_RedDot
    QA->>C: AIN_PublishJobQuickActionController / AIN_ReddotJobPublishController
    C->>CMDT: Query active field mappings
    CMDT-->>C: Field mapping records
    C->>C: Build payload (GraphQL for Indeed, REST JSON for Beeline/RedDot)
    C->>E: Callout (OAuth2-authenticated)
    E-->>C: External ID / confirmation
    C->>C: Update Job__c (AIN_Source_Posting_Id__c / AIN_BE_Job_Id__c, AIN_Is_Synced__c, AIN_Posting_Channel_History__c)
    C-->>U: Toast success/error (ainJobPortalCard)
```

Jobs can also be bulk-synced via `AIN_SyncActiveJob` (which drives `AIN_IndeedPostingBatch`) rather than only via manual Quick Action.

## 2. Indeed Apply — Inbound Application Processing

```mermaid
sequenceDiagram
    participant I as Indeed Apply
    participant R as AIN_IndeedApplyRestResource
    participant F as AIN_JsonFlattener
    participant WR as AIN_Webhook_Request__c
    participant S as Job_Board_Application__c / Contact

    I->>R: POST application payload
    R->>WR: Log webhook request (status = Pending)
    R->>F: Flatten nested JSON payload
    F-->>R: Flattened field map
    R->>R: Resolve fields via AIN_Job_Board_Mapping__mdt
    R->>S: Create/Update Job_Board_Application__c, Contact
    R->>WR: Update status (Success/Failure) + error message
    R-->>I: HTTP response
```

Screening and demographic question answers captured as part of the application are persisted against `AIN_Candidate_Response__c`, snapshotting the question text/options at time of answer (`AIN_Question_Snapshot__c`) so historical answers remain interpretable even if the question library changes later. Hierarchical/conditional questions are supported via `AIN_Candidate_Question__c.AIN_Parent_Question__c` self-lookup and `AIN_Candidate_Response__c.AIN_Parent_Response__c`/`AIN_Level__c`.

## 3. Beeline Webhook Processing

```mermaid
sequenceDiagram
    participant B as Beeline
    participant W as AIN_WebhookController
    participant WR as AIN_Webhook_Request__c
    participant J as AIN_BeelineJobController
    participant CMDT as AIN_Vms_Job_Mapping__mdt

    B->>W: POST /services/apexrest/webhook/beeline/receive
    W->>WR: Persist request (event type: JOB_POSTING_RELEASED / UPDATED / CANDIDATE_SUBMISSION_*)
    W->>J: Dispatch by event type
    J->>CMDT: Resolve active job field mappings
    J->>J: Upsert Job__c / ATS_Applicant__c via mapped fields
    alt Job posting released
        J->>J: Fetch candidate custom fields → AIN_External_Custom_Fields__c
        J->>J: Queue attachment download (AIN_BeelineJobAttachmentBatch)
    end
    J->>WR: Update status
```

If processing fails or Beeline's attachment payload needs async handling, `AIN_WebhookRetryBatch` re-processes the `AIN_Webhook_Request__c` record later, re-authenticating and re-fetching job details as needed (see `handleJobCreationAndUpdation` in `AIN_WebhookRetryBatch`).

## 4. RedDot Source Tracking Workflow

```mermaid
sequenceDiagram
    participant RD as RedDot
    participant C as AIN_RedDotJobController
    participant WR as AIN_Webhook_Request__c
    participant J as Job__c

    RD->>C: POST /services/apexrest/redDotJob/* (JOB_UPDATE event)
    C->>WR: Log webhook request
    C->>J: Query Job__c by reference (job.reference)
    C->>J: Append "RedDot" to AIN_Posting_Locations__c (dedup)
    C->>J: Append posting event to AIN_Posting_Channel_History__c (JSON array)
    C->>WR: Update status
```

RedDot job publishing (outbound) is a separate flow driven by the `Publish_Job_RedDot` Quick Action and `AIN_ReddotJobPublishController`; `Flow_For_Update_RedDot_Job_Source` is the Flow-based counterpart used for source/channel updates triggered outside of direct Apex.

## 5. Candidate Submission to Beeline VMS

```mermaid
stateDiagram-v2
    [*] --> CandidateSelected
    CandidateSelected --> FieldMapping: AIN_Sync_This_To_VMS__c = true
    FieldMapping --> BeelineSubmission: AIN_BeelineCandidatePostingService builds payload
    BeelineSubmission --> PendingResponse
    PendingResponse --> Submitted: Beeline accepts
    PendingResponse --> SubmissionFailed: Beeline rejects
    Submitted --> UpdateApplicant: AIN_BE_Candidate_Id__c / AIN_BE_Candidate_Status__c set
    SubmissionFailed --> UpdateApplicant: AIN_Failure_Reasons__c set
    UpdateApplicant --> [*]
```

Candidate field mapping uses `AIN_Vms_Job_Board_Mapping__mdt`, which (unlike the job mapping CMDT) also carries candidate-status and worker-id tracker field references (`AIN_Candidate_Status_Field_Api_Name__c`, `AIN_Candidate_Id_Mapped_Field_Api_Name__c`, `AIN_Worker_Field_Api_Name__c`).

## 6. Disposition Sync-back to Indeed

```mermaid
flowchart LR
    A[Scheduled/invoked AIN_DispositionSyncQueueable] --> B[Query ATS_Applicant__c / ATS_Stage__c per CMDT-resolved fields]
    B --> C[AIN_DispositionSyncService.buildPayloads]
    C --> D{Status source}
    D -->|Applicant Rejected + mapped| E[Use Applicant status]
    D -->|Applicant null/empty/Active| F[Use Stage status]
    E --> G[Build nested GraphQL payload]
    F --> G
    G --> H[partnerDispositionSync mutation to Indeed]
    H --> I[Update AIN_Is_Synced__c tracker field]
```

Records with a required field missing a value are skipped and logged with a specific reason string (not silently dropped) via `result.skippedRecordReasons`.

## 7. Webhook Retry & Cleanup

```mermaid
flowchart LR
    A[AIN_Webhook_Request__c status = Failure/Pending] --> B[AIN_WebhookRetryBatch.start — query ordered by CreatedDate]
    B --> C{Platform Name}
    C -->|beeline| D[Re-run Beeline job/candidate handler]
    C -->|reddot| E[Re-run AIN_RedDotJobController.updateSourceOnRedDotJob]
    C -->|indeed| F[Re-run AIN_IndeedApplyRestResource.indeedWebhookHandler]
    D --> G[Upsert resulting records, update retry count]
    E --> G
    F --> G
    G --> H[AIN_WebhookDeleteBatch purges aged Success/Failure logs per AIN_Webhook_Setup__mdt]
```

Manual retry is also available per-record via the `Retry_Webhooks` Quick Action on `AIN_Webhook_Request__c`, backed by `AIN_RetryWebhookController`.

## Corrections vs. prior documentation

- There is no generic "Request-Response / Webhook / Polling / Event-Driven pattern" abstraction layer, nor a `Job_Sync_Event__e` platform event — the actual platform event is `AIN_Webhook_Event__e`, driving `AIN_Webhook_Platform_Event_Flow`, not a custom `JobSyncEventTrigger`.
- There is no `AIN_SyncScheduler implements Schedulable` class with a 24-hour LastModifiedDate filter as previously documented; the real scheduled job for Indeed publishing is `AIN_IndeedJobSyncScheduler` (invoked via `AIN_PostInstallScript.onInstall()` → `scheduleDaily()`), and webhook cleanup/retry are handled by `AIN_WebhookDeleteBatch` / `AIN_WebhookRetryBatch`.
- RedDot, disposition sync, screening-question workflows, and the webhook durability framework (`AIN_Webhook_Request__c`) are all materially new since the package was last documented and were entirely absent from the prior workflow documentation.
