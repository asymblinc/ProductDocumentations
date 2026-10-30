# ARA (Asymbl Copilot) Custom Labels Documentation

*Regenerated 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`.*

## Correction Notice

The prior version documented 24 labels. The actual count is **404** (`manifest/package.xml` `CustomLabel` type), verified directly against `force-app/main/default/labels/CustomLabels.labels-meta.xml`.

## Summary

| Prefix | Count | Meaning |
|---|---|---|
| `ARA_*` | 398 | ARA package's own labels |
| `CP_*` | 6 | Inherited "Core Platform" labels from the base ATS layer (candidate-portal/applicant-addition error messages) |
| **Total** | **404** | |

The 6 `CP_` labels — `CP_ATS_Applicant_Created_Successfully_Label`, `CP_All_Contact_Records_Not_Found_Label`, `CP_Contact_Records_Not_Found_Label`, `CP_Error_Occured_Label`, `CP_Job_Doesnt_Has_Stage_Label`, `CP_Job_Records_Not_Found_Label`, `CP_Duplicate_Contact_Records_Found_Label` — are the only labels shared with the `CP_AddApplicantToJob` core-platform utility class and predate most of the rest of the package.

## Categorization (keyword-based, approximate — many labels span multiple categories)

| Category | Approx. Count | Examples |
|---|---|---|
| Voice Interview / Screening | ~75 | `ARA_VOICE_CALL_USER_NOT_PICK_CALL_COMMENT`, `ARA_Call_Limit_Reached_Label`, `ARA_Voice_Interview_Successfully_Schedule`, `ARA_Slots_Not_Availbale_For_Interviewer`, `ARA_Reschedule_Interview` |
| Error / Validation Messages | ~47 | `ARA_ErrorInvalidJson`, `ARA_ErrorMissingPromptTemplate`, `ARA_Configuration_Error`, `ARA_Invalid_Input_Parameter`, `ARA_Job_Validation_Error` |
| Candidate / Contact Summary UI | ~52 (overlapping with UI below) | `ARA_No_Contacts_Available`, `ARA_Select_Contact_To_Generate_Summary`, `ARA_Has_Summary_Badge`, `ARA_Existing_Summary_On_Record` |
| Job-related | ~35 | `ARA_Job_Not_Found`, `ARA_Job_Summary_Component_Header`, `ARA_Job_Summary_Updated_In_JustNow_Label`, `ARA_Job_Confirmation_Error` |
| Email | ~33 | `ARA_Email_Preview`, `ARA_Send_Email`, `ARA_Email_Drafted_Successfully`, `ARA_Failed_To_Sent_Email`, `Email_Content` |
| Interview scheduling/reminders | ~25 | `ARA_Interview_Scheduled_Label`, `ARA_Interview_Timing_Label`, `ARA_Failed_Schedule_Reminder_Error` |
| Candidate summary generator UI (modals, confirmations) | ~12 | `ARA_Unsaved_Changes_Title`, `ARA_Confirm_Save_Title`, `ARA_What_To_Do_Next_Title` |
| General UI / misc | ~125 | Button labels (`ARA_Back_Button_Label`, `ARA_Cancel`, `ARA_OK`), toast titles (`ARA_Error_Toast_Title`), task metadata labels (`ARA_Task_Subject_Label`, `ARA_Task_TaskType_Label`) |

Note: exact bucket boundaries are approximate since labels are plain strings without a `category` tag populated in most cases in this org (the `<categories>` element in the labels XML was empty/absent for the large majority of entries); this categorization is derived from naming-convention keywords, consistent with the methodology used elsewhere in this documentation set.

## Representative Labels by Functional Area

### Interview Scheduling & Reminders
`ARA_Interview_Scheduled_Label`, `ARA_Error_Interview_Scheduling_Label`, `ARA_Empty_List_of_Interviewers`, `ARA_Invalid_Interview`, `ARA_Interviewer_Not_Found_Error`, `ARA_Invalid_Interviewers`, `ARA_Interview_Title_Pattern_Attributes_Label`, `ARA_Date_After_Interview_Error`, `ARA_Date_Past_Error`, `ARA_Failed_Load_Email_Preview_Error`, `ARA_Failed_Schedule_Reminder_Error`

### Candidate Submission & Email
`ARA_Email_Not_Found`, `ARA_Email_Template_Not_Found`, `ARA_Error_While_Sending_Email`, `ARA_Email_Sent_All`, `ARA_Failed_To_Generate_Email`, `ARA_Email_Drafted_Successfully`, `ARA_Not_Associated_Candidates`, `ARA_Not_Eligible_Candidates_For_Submit_To_Manager`

### Job Summary & Job Description
`ARA_Job_Summary_Component_Header`, `ARA_Job_Updation_Label`, `ARA_Job_Summary_Time_Duration_Label`, `ARA_Job_Summary_Batch_Successfully_Run`, `ARA_Job_Description_Template_Not_Found_Label`, `ARA_Job_Description_Update_Label`

### Candidate Summary Generator (Lightning UI)
`ARA_Select_Contact`, `ARA_No_Contacts_Found`, `ARA_Generate_Summary`, `ARA_Summary_Generated`, `ARA_Summary_Generation_Failed`, `ARA_Summary_Saved_Successfully`, `ARA_Confirm_Save_Message`, `ARA_Insufficient_Data_Save_Message`, `ARA_Save_Anyway`, `ARA_Remove_Contact`, `ARA_View_Contact_Record_Title`

### Voice Interview / Voice Agent
`ARA_Voice_Interview_Human_Need_Failure_Email`, `ARA_Voice_Interview_Successfully_Schedule`, `ARA_Voice_Interview_Failure_Notification_Not_Sent`, `ARA_Call_Limit_Reached_Label`, `ARA_VOICE_CALL_USER_USE_ABUSSIVE_WORD_COMMENT`, `ARA_VOICE_CALL_USER_NOT_PICK_CALL_COMMENT`, `ARA_VOICE_CALL_WRONG_PERSON_COMMENT`, `ARA_VOICE_CALL_TECHNICAL_ISSUE_COMMENT`, `ARA_VOICE_INTERVIEW_SCHEDULED_WITH_DISCONNECT_FAILURE_REASON`, `ARA_Voice_Interview_Verification_Failed_Comment`, `ARA_Slots_Not_Availbale_For_Interviewer`, `ARA_Reschedule_Interview`

### Multi-Candidate Screening
`ARA_Task_Subject_Label`, `ARA_Task_Description_Call_Initiate_Label`, `ARA_Task_Description_Candidate_Not_Interested_Label`, `ARA_Task_Description_Candidate_Not_Available_Label`, `ARA_Task_TaskType_Label`, `ARA_Task_Created_Success_Label`, `ARA_Task_Creation_Error_Label`, `ARA_Task_Batch_Completed_Label`, `ARA_Interviewer_Not_Present_Label`

### GenAI Configuration Errors (Prompt/Config validation)
`ARA_Error_No_Active_Config`, `ARA_ErrorInvalidJson`, `ARA_ErrorMissingPromptTemplate`, `ARA_ErrorEmptyJson`, `ARA_ErrorMissingObjectConfig`, `ARA_ErrorConfigurationError`, `ARA_ErrorMissingPromptName`, `ARA_Configuration_Warning`, `ARA_Configuration_Load_Failed`

### Inherited Core Platform (CP) Labels
`CP_ATS_Applicant_Created_Successfully_Label`, `CP_All_Contact_Records_Not_Found_Label`, `CP_Contact_Records_Not_Found_Label`, `CP_Error_Occured_Label`, `CP_Job_Doesnt_Has_Stage_Label`, `CP_Job_Records_Not_Found_Label`, `CP_Duplicate_Contact_Records_Found_Label`
