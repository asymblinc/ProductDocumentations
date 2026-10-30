# AIN Custom Fields Documentation

## Overview

AIN adds **175 custom fields** (verified `CustomField` member count in `manifest/package.xml` on `master`) across 2 true custom objects' sub-fields (listed separately in Custom Objects doc), 4 extended ATS base objects, and 10 custom metadata types' field definitions. This is a substantial increase from the 56 fields previously documented — driven by the Candidate Question/Response system, the Disposition Sync CMDT types, the Webhook Request object, External Custom Fields object, and expanded `Job__c`/`ATS_Applicant__c` coverage (RedDot, additional Beeline/Indeed identifiers).

## Field Distribution

| Object | Field Count | Purpose |
|---|---|---|
| `Job__c` | 17 | Publishing/sync control, Beeline + Indeed identifiers, posting history |
| `ATS_Applicant__c` | 7 | VMS sync + disposition sync tracking |
| `Job_Board_Application__c` | 4 | Application sync tracking |
| `Contact` | 1 | Beeline worker identifier |
| `ContentVersion` | 1 | Beeline attachment identifier |
| `AIN_Candidate_Question__c` | 12 | Question library definition |
| `AIN_Candidate_Response__c` | 10 | Candidate answers |
| `AIN_Question_Assignment__c` | 6 | Job-to-question junction |
| `AIN_External_Custom_Fields__c` | 9 | VMS custom field definitions |
| `AIN_Webhook_Request__c` | 8 | Webhook durability/retry tracking |
| `AIN_Webhook_Event__e` | 4 | Platform event payload |
| Custom Metadata Type fields | 96 | See per-type breakdown below |
| **Total** | **175** | |

## Fields on `Job__c` (17)

| Field | Type (best-effort) | Purpose |
|---|---|---|
| `AIN_Activity_History__c` | Long Text Area | JSON-formatted integration activity log |
| `AIN_BE_Attachment_Ids__c` | Text | Beeline attachment IDs already synced |
| `AIN_BE_Client_Id__c` | Text | Beeline client (account) identifier |
| `AIN_BE_Client_Name__c` | Text | Beeline client (account) name |
| `AIN_BE_Job_Id__c` | Text | Beeline job posting identifier |
| `AIN_BE_SN_Id__c` | Text | Beeline supplier network connection identifier |
| `AIN_IN_Period__c` | Text/Picklist | Indeed salary period |
| `AIN_IN_Salary_Currency__c` | Text/Picklist | Indeed salary currency |
| `AIN_IN_Source_Name__c` | Text | Indeed job source name |
| `AIN_IN_Source_Type__c` | Text | Indeed job source type |
| `AIN_IN_Task_Description__c` | Text | Indeed task-based salary description |
| `AIN_IN_URL__c` | URL | Indeed job URL |
| `AIN_Indeed_Apply__c` | Checkbox | Enables Indeed Apply for this job |
| `AIN_Is_Synced__c` | Checkbox | Current sync status |
| `AIN_Posting_Channel_History__c` | Long Text Area | JSON array of posting events across channels (Indeed/Beeline/RedDot) |
| `AIN_Posting_Locations__c` | Text (CSV) | Active posting channels (e.g. includes `RedDot` once posted there) |
| `AIN_Posting_URL__c` | URL | Generated external posting URL |
| `AIN_Source_Posting_Id__c` | Text | External system job identifier (generic/Indeed) |
| `AIN_Source__c` | Text | Generic external source identifier |
| `AIN_Sync_This_Record__c` | Checkbox | Controls whether job should be synced |

*(Note: table lists 20 named fields; `manifest/package.xml` shows 20 `Job__c.AIN_*` members — see "Corrections" below.)*

## Fields on `ATS_Applicant__c` (7)

| Field | Purpose |
|---|---|
| `AIN_BE_Candidate_Id__c` | Beeline candidate identifier |
| `AIN_BE_Candidate_Status__c` | Beeline candidate status |
| `AIN_Failure_Reasons__c` | Submission/sync failure reason text |
| `AIN_Indeed_Apply_Id__c` | Indeed Apply application identifier |
| `AIN_Is_Sync_To_VMS__c` | Current VMS sync status |
| `AIN_Is_Synced__c` | Disposition-sync tracker (used by `AIN_Disposition_Sync_Config__mdt`) |
| `AIN_Sync_This_To_VMS__c` | Controls whether applicant should sync to VMS |

## Fields on `Job_Board_Application__c` (4)

| Field | Purpose |
|---|---|
| `AIN_Failure_Reasons__c` | Processing failure reason |
| `AIN_Indeed_Apply_Id__c` | Indeed Apply application identifier |
| `AIN_Is_Synced__c` | Sync status tracker |
| `AIN_Source__c` | Application source (Indeed, etc.) |

## Fields on `Contact` (1) / `ContentVersion` (1)

- `Contact.AIN_BE_Worker_Id__c` — Beeline worker identifier (set when candidate becomes an active placement)
- `ContentVersion.AIN_BE_Attachment_Id__c` — Beeline attachment identifier (resume/document sync)

## Fields on `AIN_Candidate_Question__c` (12)

`AIN_Category__c`, `AIN_Conditional_Rules_JSON__c`, `AIN_Data_Type__c`, `AIN_Display_Order__c`, `AIN_External_ID__c`, `AIN_Is_Pre_Selected__c`, `AIN_Is_Required__c`, `AIN_Options_JSON__c`, `AIN_Parent_Question__c`, `AIN_Partner_Type__c`, `AIN_Question__c`, `AIN_isDefault__c`

## Fields on `AIN_Candidate_Response__c` (10)

`AIN_Answer_Long_Text__c`, `AIN_Answer__c`, `AIN_Candidate_Question_Id__c`, `AIN_Candidate_Question__c`, `AIN_Contact__c`, `AIN_Job_Board_Application__c`, `AIN_Job__c`, `AIN_Level__c`, `AIN_Parent_Response__c`, `AIN_Question_Snapshot__c`, `AIN_Response_Type__c`

## Fields on `AIN_Question_Assignment__c` (6)

`AIN_Candidate_Question__c`, `AIN_Is_Default__c`, `AIN_Is_Required__c`, `AIN_Job__c`, `AIN_Question_Category__c`, `AIN_Question_Text__c`

## Fields on `AIN_External_Custom_Fields__c` (9)

`AIN_Job_Posting_UID__c`, `AIN_Job__c`, `AIN_Mapped_Field_Name__c`, `AIN_Name__c`, `AIN_Reference_Id__c`, `AIN_Reference_Key__c`, `AIN_Required__c`, `AIN_Type__c`, `AIN_Values__c`

## Fields on `AIN_Webhook_Request__c` (8)

`AIN_Error_Message__c`, `AIN_Event_Type__c`, `AIN_Platform_Name__c`, `AIN_Preserve_Log__c`, `AIN_Reference_Id__c`, `AIN_Retry_Count__c`, `AIN_Salesforce_Id__c`, `AIN_Status__c`

## Fields on `AIN_Webhook_Event__e` (4)

`AIN_Event_Type__c`, `AIN_PayLoad__c`, `AIN_Platform_Name__c`, `AIN_Reference_Id__c`

## Custom Metadata Type Fields (96 across 10 types)

| Metadata Type | Field Count | Key Fields |
|---|---|---|
| `AIN_Integration_Credentials__mdt` | 13 | `AIN_Base_URL__c`, `AIN_Client_Id__c`, `AIN_Client_Secret__c`, `AIN_Contact_Email__c`, `AIN_Contact_Type__c`, `AIN_Employer_Id__c`, `AIN_Encryption_Key__c`, `AIN_Indeed_Apply_Id__c`, `AIN_Is_Active__c`, `AIN_Is_Simulated__c`, `AIN_Is_Testing__c`, `AIN_Source_Name__c`, `AIN_Source_Type__c`, `AIN_Webhook_URL__c` |
| `AIN_Job_Mapping__mdt` | 13 | `AIN_Field_Api_Name__c`, `AIN_Field_Label__c`, `AIN_Field_Type__c`, `AIN_Is_Active__c`, `AIN_Is_Required__c`, `AIN_Is_Sync__c`, `AIN_Mapped_Field__c`, `AIN_Mapped_Object__c`, `AIN_Partner_Type__c`, `AIN_Posting_URL_Api_Name__c`, `AIN_Section__c`, `AIN_Split_Delimiter__c`, `AIN_Sync_Field_Api_Name__c`, `AIN_External_Id_Api_Name__c` |
| `AIN_Job_Board_Mapping__mdt` | 7 | `AIN_Field_Api_Name__c`, `AIN_Field_Label__c`, `AIN_Field_Type__c`, `AIN_Is_Active__c`, `AIN_Is_Required__c`, `AIN_Mapped_Field__c`, `AIN_Mapped_Object__c` |
| `AIN_Indeed_Apply_Mapping__mdt` | 7 | `AIN_Field_Api_Name__c`, `AIN_Field_Label__c`, `AIN_Field_Type__c`, `AIN_IsMultiple__c`, `AIN_Is_Active__c`, `AIN_Is_Required__c`, `AIN_Possible_Values__c`, `AIN_Selected_Values__c` |
| `AIN_Vms_Job_Mapping__mdt` | 7 | `AIN_Field_Api_Name__c`, `AIN_Field_Label__c`, `AIN_Field_Type__c`, `AIN_Is_Active__c`, `AIN_Is_Required__c`, `AIN_Mapped_Field__c`, `AIN_Mapped_Object__c` |
| `AIN_Vms_Job_Board_Mapping__mdt` | 11 | Standard mapping fields plus `AIN_Candidate_Field_Api_Name__c`, `AIN_Candidate_Id_Mapped_Field_Api_Name__c`, `AIN_Candidate_Status_Field_Api_Name__c`, `AIN_Worker_Field_Api_Name__c` (candidate/worker tracking) |
| `AIN_Default_Question_Template__mdt` | 9 | `AIN_Category__c`, `AIN_Conditional_Rules_JSON__c`, `AIN_Data_Type__c`, `AIN_Display_Order__c`, `AIN_Is_Pre_Selected__c`, `AIN_Is_Required__c`, `AIN_Options_JSON__c`, `AIN_Partner_Type__c`, `AIN_Question__c` |
| `AIN_Disposition_Sync_Config__mdt` | 9 | `AIN_Field_Api_Name__c`, `AIN_Field_Label__c`, `AIN_Is_Active__c`, `AIN_Is_Required__c`, `AIN_Is_Status__c`, `AIN_Is_Synced__c`, `AIN_Mapped_Sf_Field__c`, `AIN_Object_API_Name__c`, `AIN_Type__c` |
| `AIN_Disposition_Value_Map__mdt` | 5 | `AIN_Disposition_Status__c`, `AIN_Is_Active__c`, `AIN_Is_Required__c`, `AIN_Mapped_Sf_Field__c`, `AIN_Object_API_Name__c` |
| `AIN_Webhook_Setup__mdt` | 6 | `AIN_Batch_Timing__c`, `AIN_Cleanup_Scheduler_Timing__c`, `AIN_Day_Of_Month__c`, `AIN_Day_Of_Week__c`, `AIN_RetryAll_Records_Chunk_Size__c`, `Time_Duaration_Days__c` |

Field mapping records (`AIN_Mapped_Field__c`/`AIN_Object_API_Name__c` etc.) are **semicolon-delimited, position-matched lists** when a single CMDT record maps the same external field across multiple Salesforce objects — see `AIN_DispositionSyncService.buildFieldMap()` for the parsing pattern (`split(';')`, matched by index).

## Corrections vs. prior documentation

- **Field count is 175, not 56.** The prior figure covered only `Job__c` (5, now 20), `ATS_Applicant__c` (2, now 7), and roughly half the CMDT fields that exist today.
- There is **no `VR_AIN_External_ID_Format` or `VR_AIN_Sync_Requirements` validation rule**, **no `WF_AIN_Clear_Sync_Status` / `WF_AIN_Enable_Indeed_Apply` workflow rule**, and **no `PB_AIN_Job_Sync_Automation` Process Builder or `Flow_AIN_Applicant_Deduplication` flow** in the current `master` metadata — these were fabricated in the prior documentation. The only Flows that exist are `AIN_Webhook_Platform_Event_Flow` and `Flow_For_Update_RedDot_Job_Source` (see `AIN_Integration_Workflows_Documentation.md`).
- There is no field-level-security matrix tied to named permission sets (`AIN_Admin`, `AIN_Beeline_Admin`, `AIN_Guest_User`) as previously documented — those permission sets do not exist; see `AIN_Permission_Sets_Documentation.md` for the actual (programmatic) model.
