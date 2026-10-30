# ERD Audit Report - Documentation Verification Log

> **Revision note (2026-10-30)**: This file previously served as a gap-analysis of the 2025-07-10 documentation set against itself (listing fields that existed in the real package but were omitted from the ERDs at that time). As part of this revision, every ERD in this folder was rebuilt directly against the `bpats` `master` branch metadata, so the specific line-item gaps the prior audit tracked have now been resolved. This file is repurposed to record **what was verified, how, and what residual limitations/uncertainties remain** — consistent with the ULTRATHINK gap-analysis methodology in `Claude.md`.

## Executive Summary

This audit compares the current (2026-10-30) ATS documentation set against the `bpats` repository `master` branch. Verification was performed by:
1. Reading `manifest/package.xml` in full and extracting a component-count matrix for every `<types>` block
2. Enumerating every object directory under `force-app/main/default/objects` (72 CustomObjects + 22 `__mdt` + 2 platform events = matches package.xml `CustomObject` count exactly)
3. Reading the actual `fields/*.field-meta.xml` file list for every object referenced in the domain ERDs (Job, Template, Stage, Applicant, Job Board Application, Screening Questions, Interview family, Topic Kit family, Placement/Credit family, Target family, Matching Intelligence family, supporting objects, all 22 metadata types, and the 3 standard object extensions)
4. Cross-checking every field name/type claim in the prior (2025-07-10) documentation set against this real metadata

## Component Count Verification Matrix

| Component Type | package.xml Count | Prior Doc Claimed | Status |
|---|---|---|---|
| CustomObject | 72 | 45+ / 50+ (README) | ✓ Corrected — undercounted |
| CustomField | 699 | 500+ | ✓ Corrected — undercounted |
| ApexClass | 426 (423 .cls files) | 291 | ✓ Corrected — significantly undercounted |
| CustomMetadata (records) | 238 | n/a | ✓ New to this revision |
| LightningComponentBundle | 204 | 90+ | ✓ Corrected — significantly undercounted |
| Layout | 66 | "comprehensive... 30+ additional" | ✓ Corrected — now exact |
| ListView | 39 | n/a | ✓ New to this revision |
| CompactLayout | 36 | n/a (described qualitatively) | ✓ Corrected — now exact |
| QuickAction | 33 | 25+ | ✓ Corrected — undercounted |
| CustomTab | 31 | n/a (described qualitatively) | ✓ Corrected — now exact |
| FlexiPage | 21 | n/a (described qualitatively) | ✓ Corrected — now exact |
| Report | 18 | 14 | ✓ Corrected — undercounted |
| ApexTrigger | 17 | 9 | ✓ Corrected — significantly undercounted |
| ValidationRule | 14 | 8 | ✓ Corrected — undercounted |
| ApexPage | 10 | 9 (implied) | ✓ Corrected |
| AuraDefinitionBundle | 9 | not mentioned | ✓ New to this revision |
| GlobalValueSet | 8 | not mentioned by count | ✓ New to this revision |
| CustomMetadata TYPES (objects) | 22 | 10+ / 15+ / 20+ (inconsistent across prior files) | ✓ Corrected — now exact, and several named types were fabricated |
| EmailTemplate | 4 | 3 | ✓ Corrected |
| FieldSet | 4 | 3 | ✓ Corrected |
| LightningMessageChannel | 4 | 2 | ✓ Corrected |
| RemoteSiteSetting | 4 | 4 | ✓ Matched |
| Flow | 3 | 2 | ✓ Corrected |
| PermissionSet | 3 | 3 | ✓ Matched |
| CustomPermission | 2 | 1 | ✓ Corrected |
| CustomLabel | 3,024 | 1,900+ / 1,911 | ✓ Corrected — undercounted |
| Platform Events | 2 | 2 | ✓ Matched |

## Fabricated Content Removed in This Revision

The following objects/fields appeared in the 2025-07-10 documentation but **do not exist anywhere in the `bpats` package** as of `master`. They have been removed from all affected files:

### Entirely fabricated objects (previously in `ERD_Integration_Objects.md`)
`TextKernel_Config__c`, `Resume_Parse_Log__c`, `Twilio_Configuration__c`, `SMS_Log__c`, `Zoom_Configuration__c`, `Zoom_Meeting__c`, `Google_Calendar_Integration__c`, `Integration_Setting__mdt`, `Field_Mapping__mdt`, `Integration_Event__e`, `Email_Tracking_Event__e`, `Integration_Log__c`, `Webhook_Registration__c`, `Email_Template_Stats__c`

### Entirely fabricated objects (previously in `ERD_Standard_Object_Extensions.md`)
`Job_Posting__c`, `Position__c`, `Candidate__c`, `Application__c`

### Entirely fabricated metadata types (previously in `ERD_Configuration_Metadata.md`)
`ATS_Calendar_Integration__mdt`, `ATS_Email_Template__mdt`, `ATS_Document_Template__mdt`, `ATS_Workflow_Configuration__mdt`, `ATS_Permission_Set_Mapping__mdt`, `ATS_Notification_Settings__mdt`

### Fabricated fields on real objects
- **Job__c** (previously in `ERD_Job_Management.md`): `Department__c`, `Min_Salary__c`, `Max_Salary__c`, `Is_Confidential__c`, `Is_Remote__c`, `External_Job_ID__c`, `Application_Deadline__c`, `Experience_Level__c`, `Required_Skills__c`
- **ATS_Template__c**: `Template_Type__c`, `Stage_Configuration__c`, `Default_Days_To_Fill__c`, `Approval_Process__c`, `Required_Documents__c`, `Interview_Rounds__c`, `Scoring_Criteria__c`, `Background_Check_Required__c`, `Reference_Check_Required__c`, `SLA_Days__c`
- **ATS_Stage__c**: `Stage_Name__c`, `Duration_Days__c`, `Stage_Criteria__c`, `Required_Actions__c`, `Assigned_To__c`, `Send_Notifications__c`, `Candidates_In_Stage__c`, `Pass_Rate__c`, `Min_Score_Required__c`
- **Interview__c** (previously in `ERD_Interview_Management.md`): `Stage_Application__c`, `Interview_Status__c` (actual: `Status__c`), `Interview_Result__c`, `Interview_Score__c`, `Send_Calendar_Invite__c`, `Time_Zone__c`, `Average_Rating__c`, `Total_Feedback_Count__c`
- **Interview_Template__c**: `Template_Name__c`, `Description__c`, `Default_Duration_Minutes__c`, `Department__c`, `Job_Level__c`, `Required_Interviewers__c`, `Question_Bank__c`, `Scoring_Guidelines__c`, `Template_Owner__c`, `Average_Interview_Score__c`, `Pass_Rate__c`, `Template_Version__c`, `Parent_Template__c`
- **Interview_Topic__c**: `Topic_Name__c`, `Topic_Category__c`, `Difficulty_Level__c`, `Assessment_Type__c`, `Parent_Topic__c`, `Department__c`, `Job_Level__c`, `Topic_Owner__c`
- **Interview_Feedback__c**: `Technical_Skills_Rating__c`, `Communication_Rating__c`, `Problem_Solving_Rating__c`, `Cultural_Fit_Rating__c`, `Strengths__c`, `Areas_of_Improvement__c`, `Follow_Up_Questions__c`, `Red_Flags__c`, `Feedback_Visibility__c`, `Anonymous_Feedback__c`
- **Placement__c** (previously in `ERD_Placement_Credit_Management.md`): `GP_Percentage__c`, `GP_Amount__c`, `Notes__c`, `Credit_Allocated__c`, `Credit_Allocation_Date__c`
- **Placement_Credit__c**: `Credit_Amount__c`, `Is_Primary__c`, `Notes__c`, `Effective_Date__c`, `End_Date__c`, `Status__c`, `Created_From_Template__c`
- **Placement_Credit_Template__c**: `Template_Type__c`, `Department__c`, `Is_Default__c`
- **Placement_Credit_Template_Entry__c**: `Role__c`, `Is_Primary__c`, `Sort_Order__c`, `Is_Active__c`
- **ATS_Action__c** (previously in `ERD_Action_Template_Configuration.md`): `Icon__c`, `Label__c`, `Parameters__c`, `Permissions__c`, `Record_Type__c`, `Target_Object__c`
- **ATS_Action_Link__c**: `Condition_Logic__c`, `Display_Condition__c`, `Is_Required__c`, `Button_Label__c`, `Button_Style__c`
- **ATS_Template__c** (action-config file variant): `Object_Name__c`, `Category__c`, `Icon__c`, `Version__c`, `Status__c`, `Effective_Date__c`, `End_Date__c`, `Owner_Field__c`, `Parent_Template__c`
- **ATS_Template_Stage__c**: `Stage_Label__c`, `Description__c`, `Is_Required__c`, `Completion_Criteria__c`, `Display_Condition__c`, `Days_To_Complete__c`, `Icon__c`, `Color__c`, `Help_Text__c`
- **ATS_Template_Filter__c**: `Field_Type__c`, `Filter_Order__c`, `Logic_Group__c`, `Parent_Field_Path__c`, `Filter_Logic__c`
- **ATS_Stage__c** (action-config file variant): `Object_Name__c`, `Default_Sequence__c`, `Icon__c`, `Color__c`
- **Interview_Topic_Feedback__c**: `Interview_Feedback__c` lookup field (actual relationship is via `Topic__c` only)
- **Message_Stats__c**: `Spam_Reports__c`, `Unsubscribes__c`
- **Google_Recaptcha_Detail__c**: `Site_Key__c`, `Recaptcha_URL__c`, `Version__c`, `Domain__c`
- **ATS_Applicant__c** (ERD-only, Applicant Management file): `Applied_On__c`, `Average_Rating__c`, `City__c`, `Country__c`, `First_Name__c`, `Last_Name__c`, `Rating_Count__c`, `Website__c`, `Years_of_Experience__c`, `Zip_Postal_Code__c`, `State__c`
- **Job_Board_Application__c** (ERD-only): `Address__c`, `Cover_Letter__c`, `Cover_Letter_Text__c`, `Resume_Attachment_ID__c`, `Source__c`, `Website__c`

## Domains Entirely Missing From the Prior (2025-07-10) Documentation Set

The following domains exist in the real package but had **zero** prior documentation coverage. All are now documented across the relevant ERD files and the main package doc:

1. **Screening Questions** — `ATS_Question__c`, `ATS_Question_Category__c`, `ATS_Question_Response__c`, `ATS_Job_Screening_Question__c` (see `ERD_Job_Management.md`, `ERD_Applicant_Management.md`)
2. **Interview Topic Kits & Availability** — `Interview_Topic_Kit__c`, `Interview_Topic_Kit_Item__c`, `Interview_Availability__c` (see `ERD_Interview_Management.md`)
3. **Target / Goal Management** — `Target__c`, `Target_Template__c`, `Target_Template_Entry__c` (see `ERD_Placement_Credit_Management.md`)
4. **Candidate/Job Matching Intelligence** — `ATS_Match_Cache__c`, `ATS_Candidate_Search_Log__c`, `ATS_Search_Location__c`, `Qualification_Run__c`, plus metadata `ATS_Qualification_Config__mdt`, `ATS_Talent_Intelligence_Data__mdt`, `ATS_Job_Intelligence_Setting__mdt` (see `ERD_Integration_Objects.md`, `ERD_Missing_Objects_Complete.md`, `ERD_Configuration_Metadata.md`)
5. **Per-template action toggles** — `ATS_Template_Action_Toggles__c` (see `ERD_Action_Template_Configuration.md`)
6. **Newer resume-mapping metadata** — `ATS_Resume_Field_Mapping__mdt`, `ATS_Skill_Summary_Mapping__mdt`, `ATS_Work_Experience_Mapping__mdt` (see `ERD_Configuration_Metadata.md`)
7. **Feature gating and platform-event restriction config** — `Feature_Gate__mdt`, `ATS_Platform_Event_Restriction__mdt`, `ATS_Applicant_Field_Type_Config__mdt` (see `ERD_Configuration_Metadata.md`)
8. **New triggers** — `ATS_AccountTrigger`, `ATS_ApplicantTrigger`, `ATS_OfferTrigger`, `ATS_QuestionTrigger`, `ATS_QuestionCategoryTrigger`, `ATS_TargetTrigger`, `ATS_TargetTemplateTrigger`, `ATS_TargetTemplateEntryTrigger` (see main `ATS_Package_Documentation.md`)

## Residual Limitations of This Revision

Despite the depth of this pass, the following should be flagged as **not exhaustively verified at the individual-field level** and may warrant a follow-up pass if precision is required:

1. **Apex class / LWC inventories** in the main documentation are organized by representative domain sample, not an exhaustive enumeration of all 423 Apex classes / 204 LWC bundles — the package.xml component counts are exact, but per-class descriptions are illustrative, not complete
2. **`Add_to_ATS__e` platform event fields** — this event's object directory contains no `fields/` subdirectory in the inspected tree, so no custom fields could be enumerated for it; this may mean the event currently has no custom fields, or that its field definitions are declared in a location/format not captured by this pass — flagged as uncertain rather than asserted either way
3. **Validation Rules, Quick Actions, Layouts, FlexiPages, Report field-level detail** — counts are verified exactly against `manifest/package.xml`, but full itemized lists of all 14 validation rules / 33 quick actions / 66 layouts / 21 FlexiPages were not individually enumerated field-by-field in this pass (component names/counts only)
4. **Custom Label content** — the 3,024-label count is verified via the packaged `CustomLabels.labels-meta.xml`, but individual label text/values were not audited
5. **Branch used**: this revision reflects `origin/master` as fetched on 2026-10-30. The local bpats working tree was on `release/ATS-1.52` at the time of this analysis and was **not checked out** to avoid disrupting in-progress work — all reads were performed non-destructively via `git show`/`git ls-tree` against `origin/master`. If `master` has advanced further since this fetch, a re-verification pass is recommended before treating this documentation as current.

## Recommendations for Future Maintenance

1. Re-run the ULTRATHINK component-count verification matrix against `manifest/package.xml` on each significant release to catch drift early
2. Treat any ERD file with highly "regular"/generic-sounding field names (e.g., `Status__c`, `Is_Active__c`, `Description__c` repeated identically across many objects with no object-specific verification) as a signal to re-verify against source metadata — this pattern was the clearest tell that the 2025-07-10 revision's integration and configuration-metadata ERDs were fabricated rather than derived
3. Prefer generating field lists directly from `force-app/main/default/objects/*/fields/*.field-meta.xml` rather than reconstructing them from memory/inference, per the methodology in `Claude.md`
