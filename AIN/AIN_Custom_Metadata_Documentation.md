# AIN Custom Metadata Documentation

## Overview

The AIN package ships **240 custom metadata records** (verified by counting `.md-meta.xml` files under `force-app/main/default/customMetadata/` on `master`, matching the `CustomMetadata` member count in `manifest/package.xml`) across **10 custom metadata types**. This is up from 6 types / ~184-190 records previously documented — 4 new CMDT types were added (`AIN_Disposition_Sync_Config__mdt`, `AIN_Disposition_Value_Map__mdt`, `AIN_Default_Question_Template__mdt`, `AIN_Webhook_Setup__mdt`), and `AIN_Integration_Credentials__mdt` gained a 4th record (`RedDot`).

## Metadata Record Distribution (verified counts)

| Metadata Type | Record Count | Purpose |
|---|---|---|
| `AIN_Job_Mapping__mdt` | 67 | Salesforce `Job__c` ⇄ Indeed GraphQL job field mapping |
| `AIN_Vms_Job_Mapping__mdt` | 50 | `Job__c` ⇄ Beeline job field mapping |
| `AIN_Vms_Job_Board_Mapping__mdt` | 35 | Candidate submission fields ⇄ Beeline |
| `AIN_Job_Board_Mapping__mdt` | 32 | Inbound application fields ⇄ Salesforce (Indeed Apply / generic job board) |
| `AIN_Disposition_Value_Map__mdt` | 24 | SF picklist value ⇄ Indeed disposition status (e.g. `HIRED`, `WITHDRAWN`, `REVIEW`, `SCREEN`) |
| `AIN_Disposition_Sync_Config__mdt` | 13 | Field-level config for the Indeed `partnerDispositionSync` payload |
| `AIN_Indeed_Apply_Mapping__mdt` | 6 | Indeed Apply form configuration (resume/cover letter/phone requirements, name format) |
| `AIN_Default_Question_Template__mdt` | 6 | Seed data for the screening/demographic question library |
| `AIN_Integration_Credentials__mdt` | 4 | Beeline, Indeed, Indeed_Apply, **RedDot** credentials |
| `AIN_Webhook_Setup__mdt` | 1 | Webhook retry/cleanup batch scheduling configuration |
| **Total** | **240** | |

## 1. `AIN_Integration_Credentials__mdt` (4 records)

| Record | Purpose |
|---|---|
| `Beeline` | OAuth 2.0 client-credentials for Beeline VMS |
| `Indeed` | Server-to-server GraphQL API credentials |
| `Indeed_Apply` | Indeed Apply webhook configuration |
| `RedDot` | RedDot job board credentials (fields present but nil by default — populated per-org via the `ainRedDotAuthenticationSettings` LWC) |

Fields (13): `AIN_Base_URL__c`, `AIN_Client_Id__c`, `AIN_Client_Secret__c`, `AIN_Contact_Email__c`, `AIN_Contact_Type__c`, `AIN_Employer_Id__c`, `AIN_Encryption_Key__c`, `AIN_Indeed_Apply_Id__c`, `AIN_Is_Active__c`, `AIN_Is_Simulated__c`, `AIN_Is_Testing__c`, `AIN_Source_Name__c`, `AIN_Source_Type__c`, `AIN_Webhook_URL__c`.

## 2. `AIN_Job_Mapping__mdt` (67 records)

Maps `Job__c` fields to Indeed GraphQL job-posting input fields (`title`, `description`, `company_name`, `location_city_region_postal`, `minimum_minor`/`maximum_minor`, `job_type`, `remote_type`, `date_published`, `expiration_date`, probationary-period fields, overtime salary fields, RedDot-specific fields `Email_RedDot`/`ExternalUrl_RedDot`/`Location_RedDot`/`Title_RedDot`, and more). Supports semicolon-delimited multi-object mapping via `AIN_Mapped_Object__c`/`AIN_Mapped_Field__c` for records shared across Indeed and RedDot.

## 3. `AIN_Job_Board_Mapping__mdt` (32 records)

Maps inbound application data (from Indeed Apply/job boards) to `Contact`/`ATS_Applicant__c`/`Job_Board_Application__c` fields — applicant name/email/phone, resume JSON sub-fields (`Resume_JSON_First_Name`, `Resume_JSON_Skills`, `Resume_JSON_Summary`, etc.), job reference fields, and now also `Demographic_Question_And_Answer` / `Screener_Question_And_Answer` / `Webhook_Apply_Id` (new since prior documentation, supporting the question/answer capture pipeline).

## 4. `AIN_Indeed_Apply_Mapping__mdt` (6 records)

`Indeed_Apply_Resume_Required`, `Indeed_Apply_Cover_Letter_Required`, `Indeed_Apply_Phone_Required`, `Indeed_Apply_Name_Format`, `Indeed_Apply_Resume_Fields_Required`, `Indeed_Apply_Resume_Fields_Optional`.

## 5. `AIN_Vms_Job_Mapping__mdt` (50 records)

Maps `Job__c` fields to Beeline job-posting fields: client/enterprise identification, classification, physical and tax work-location detail, cost centers, start/end/expiry dates, supplier connections, job attachment ids.

## 6. `AIN_Vms_Job_Board_Mapping__mdt` (35 records)

Maps candidate submission data to Beeline: personal info, address components, rates, availability, former-employee/consultant flags, plus candidate/worker tracking fields (`AIN_Candidate_Id_Mapped_Field_Api_Name__c`, `AIN_Candidate_Status_Field_Api_Name__c`, `AIN_Worker_Field_Api_Name__c`, `AIN_Candidate_Field_Api_Name__c`) used to write back candidate status/worker IDs from Beeline webhook responses.

## 7. `AIN_Disposition_Sync_Config__mdt` (13 records) — **new since prior documentation**

Defines, per Indeed `partnerDispositionSync` GraphQL field, which Salesforce object(s) and field(s) supply the value (`AIN_Object_API_Name__c`, `AIN_Mapped_Sf_Field__c`, both semicolon-delimited and position-matched for multi-object configs), whether the field represents the disposition status itself (`AIN_Is_Status__c`), whether it's required (`AIN_Is_Required__c`), and which SF field tracks sync completion/failure reason (`AIN_Is_Synced__c`, `AIN_Failure_Reason__c`). Sample mapped Indeed fields: `atsApplicationIdentifier`, `atsName`, `dispositionStatus`, `emailAddress`, `employerJobId`, `identifiedBy`, `indeedApplyID`, `indeedJobKey`, `indeedJobSeekerKey`, `ittk`, `rawDispositionDetails`, `rawDispositionStatus`, `statusChangeDateTime`, `universalApplyId`.

## 8. `AIN_Disposition_Value_Map__mdt` (24 records) — **new since prior documentation**

Maps Salesforce picklist values to Indeed disposition statuses. Record names observed: `ASSESS_QUALIFICATIONS`, `BACKGROUND_CHECK`, `CONTACTED`, `DROPPED_DUPLICATE`, `DROPPED_FRAUD_SPAM`, `DROPPED_JOB_EXPIRED`, `DROPPED_OTHER`, `HIRED`, `INCOMPLETE`, `INTERVIEW`, `JOB_CLOSED`, `JOB_INACTIVE`, `LIKED`, `NEW`, `NOT_SELECTED`, `OFFER_DECLINED`, `OFFER_MADE`, `ONBOARDED`, `POSITIVELY_SCREENED`, `REVIEW`, `SCREEN`, `UNABLE_TO_MAP`, `VERIFY_ELIGIBILITY`, `WITHDRAWN`. The `AIN_Mapped_Sf_Field__c` value is stored as a JSON object keyed by `object:field`, listing the SF values that map to that Indeed status (see `AIN_DispositionSyncService.buildValueLookup()`).

## 9. `AIN_Default_Question_Template__mdt` (6 records) — **new since prior documentation**

Seed templates for the screening/demographic question library, cloned into `AIN_Candidate_Question__c` on install (`AIN_PostInstallScript.cloneDefaultQuestionTemplates()`). Record names observed: `disability`, `experience`, `military_veteran`, `region`, `relocation`, `salary_expectations`.

## 10. `AIN_Webhook_Setup__mdt` (1 record) — **new since prior documentation**

Single record (`AIN_Webhook_Setup`) configuring the webhook retry/cleanup batch schedule: `AIN_Batch_Timing__c`, `AIN_Cleanup_Scheduler_Timing__c`, `AIN_Day_Of_Month__c`, `AIN_Day_Of_Week__c`, `AIN_RetryAll_Records_Chunk_Size__c` (verified value: `10`), `Time_Duaration_Days__c` (verified value: `30` — retention window for webhook log cleanup).

## Corrections vs. prior documentation

- **6 metadata types / ~184-190 records is stale.** The package now has **10 types / 240 records** — a 30%+ increase driven entirely by capabilities that didn't exist before (disposition sync, screening questions, webhook scheduling) plus the RedDot credential record.
- `AIN_Integration_Credentials__mdt` has **4** records, not 3 — RedDot was previously omitted entirely.
- Field mapping values can be **semicolon-delimited multi-object lists** (e.g., one `AIN_Job_Mapping__mdt` record mapping to both Indeed and RedDot via position-matched `AIN_Mapped_Object__c`/`AIN_Mapped_Field__c` lists) — this pattern did not exist in, and is not described by, prior documentation, which assumed one-to-one record-to-mapping relationships.
