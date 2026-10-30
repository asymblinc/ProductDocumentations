# AIN Custom Labels Documentation

## Overview

The AIN package ships **296 custom labels** (verified `CustomLabel` member count in `manifest/package.xml` on `master`, stored in a single `CustomLabels.labels-meta.xml` file with 296 `<labels>` entries). This is roughly 3x the 99 labels previously documented — driven by the screening/demographic question builder, RedDot integration, webhook retry/configuration UI, and disposition mapping UI, none of which existed when labels were last documented.

## Label Category Distribution (derived from naming pattern analysis)

| Category (by naming heuristic) | Approx. Count | Examples |
|---|---|---|
| UI Labels (`*_Label`, `*_Button*`, `*_Heading*`, `*_Title`) | ~106 | `AIN_Save_Button_Label`, `AIN_Beeline_Tab_Heading`, `AIN_RedDot_Tab_Heading` |
| Messages (`*_Message`) | ~43 | `AIN_Job_Sync_Failure_Message`, `AIN_Credentials_Saved_Message` |
| Error-specific (`*Error*`) | ~34 | `AIN_Authentication_Error_Message`, `AIN_Error_in_WebHook_Setup` |
| Help/Info text (`*Help*`, `*Placeholder*`, `*Description*`, `*Info*`) | ~29 | `AIN_Job_Board_Mapping_Info`, `AIN_Choices_Help_Text` |
| Success-specific (`*Success*`) | ~5 | `AIN_Success_Label`, `AIN_Candidate_Submit_SuccessNotificationMessage` |
| Other (status/config/domain terms not matching the above) | ~79 | `AIN_Day_Of_Month`, `AIN_Disposition_Status_Label`, `AIN_Mode_Label` |
| **Total** | **296** | |

*(This is a naming-pattern approximation, not an authoritative design taxonomy — AIN does not define formal label categories in metadata. Treat the breakdown as directional.)*

## New Label Groups Since Prior Documentation (2025-07)

The following functional groups of labels did not exist in the previously documented version and are net-new:

### RedDot Integration
`AIN_RedDot_Email_Not_Be_Blank`, `AIN_RedDot_Fields`, `AIN_RedDot_Mapping_FileName`, `AIN_RedDot_Mapping_File_Downloaded_Successfully_Message`, `AIN_RedDot_Tab_Heading`, `AIN_RedDot_Toggel_Status`, `AIN_RedDot_Toggel_Status_Activate`

### Screening/Demographic Question Builder
`AIN_Add_Another_Option_Button`, `AIN_Add_Level_1_Button`, `AIN_Add_Level_2_Button`, `AIN_Add_New_Question_Tab`, `AIN_Build_Hierarchical_Structure_Heading`, `AIN_Category_Label`, `AIN_Choices_Help_Text`, `AIN_Conditional_Logic_Description`, `AIN_Conditional_Logic_Heading`, `AIN_Conditional_Questions_Warning`, `AIN_Configure_Hierarchical_Level_Error`, `AIN_Configured_Levels_Heading`, `AIN_Demographic_Questions_EEOC`, `AIN_EEOC_Compliance_Questions_Demographic_Data`, `AIN_Hierarchical_Builder_Help`, `AIN_Hierarchical_Options_Description`, `AIN_Hierarchical_Options_Heading`, `AIN_No_Demographic_Question_Selected`, `AIN_No_Demographic_Questions_Found`, `AIN_No_Screening_Question_Selected`, `AIN_No_Screening_Questions_Found`, `AIN_Parent_Question_Label`, `AIN_Pre_Configured_Questions_Description_UI`, `AIN_Question_Template_Label`, `AIN_Question_Text_Label`, `AIN_Question_Type_Label`, `AIN_Root_Options_Heading`, `AIN_Select_Parent_Question_Error`, `AIN_Select_Question_Type_Placeholder`, and ~20 more

### Webhook Configuration & Retry
`AIN_Base_Webhook_Url_Label`, `AIN_Batch_Schedule_Successfully`, `AIN_CleanUp_Scheduler_Timing`, `AIN_Day_Of_Month`, `AIN_Day_Of_Month_HelpText`, `AIN_Logs_Deletion_Finished_Label`, `AIN_No_Webhook_Failure_Records`, `AIN_RetryAll_Chunk_Size_EmptyMessage`, `AIN_RetryAll_Process_Message`, `AIN_Retry_All_Button`, `AIN_Retry_Log_Finished_Label`, `AIN_Unable_To_Retry_Webhook_Without_Admin`, `AIN_WebhookLog_Deletion_Info_Message`, `AIN_Webhook_Configuration_Description`, `AIN_Webhook_Configuration_Title`, `AIN_Webhook_LogManagement_Heading`, `AIN_Webhook_Request_Error_message`, `AIN_Webhook_Retry_Submission_Message`, `AIN_Webhook_Tab_Heading`, `AIN_Webhook_Url_Heading`

### Disposition Mapping
`AIN_Disposition_Status_Label`, `AIN_Indeed_Disposition_Mapping_Label`

### Indeed OAuth / Connection
`AIN_Authorize_Indeed_Required`, `AIN_Indeed_Connected`, `AIN_Indeed_Disconnection_Confirmation`, `AIN_Indeed_Disconnection_Message`, `AIN_Indeed_Not_Connected`, `AIN_Indeed_Successfully_Connected_Message`, `AIN_Error_In_Disconnecting_Indeed`

### Permission Set Labels (used as programmatic `PermissionSet.Label` values)
`AIN_Admin_User_Permission_Set_Label`, `AIN_User_Permission_Set_Label`, `AIN_Guest_User_Permission_Set_Label`, `AIN_Api_User_Permission_Set_Label` — these back the four runtime-created permission sets (`AIN_Admin_Clone`, `AIN_User_Clone`, `AIN_Guest_Clone`, `AIN_Api_User_Clone`); see `AIN_Permission_Sets_Documentation.md`.

## Implementation Patterns (unchanged, still accurate)

### In Apex
```apex
String errorMsg = Label.AIN_Authentication_Error_Message;
String syncError = String.format(Label.AIN_Job_Sync_Failure_Message, new String[]{'Indeed', 'Connection timeout'});
```

### In Lightning Web Components (`labels.js` pattern)
```javascript
import authError from '@salesforce/label/c.AIN_Authentication_Error_Message';
import saveButton from '@salesforce/label/c.AIN_Save_Button_Label';
const LABELS = { authError, saveButton };
export { LABELS };
```

## Corrections vs. prior documentation

- **Count is 296, not 99.** The prior figure predates RedDot, the screening/demographic question builder, and the webhook retry/configuration UI.
- Supported-language claims ("translation-ready for es/fr/de/ja/pt_BR") are **not verifiable from metadata** — only a single `en_US` label set exists in `CustomLabels.labels-meta.xml`; no translation files (`.translation-meta.xml`) ship in this package. That section of prior documentation should be treated as aspirational, not a documented fact.
- The previously-listed `AIN_Mapped_Object`, `AIN_No_Permission_Error_Message`, etc. groupings by "severity level" (Critical/Error/Warning/Info) are not a real metadata attribute — labels have no severity field in Salesforce; that table has been removed as unverifiable.
