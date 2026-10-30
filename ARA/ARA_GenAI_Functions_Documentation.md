# ARA (Asymbl Copilot) GenAI Functions Documentation

*Regenerated 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`.*

## Correction Notice

The prior version documented 8 fabricated GenAI functions with invented generic REST-style JSON schemas (`{"success": true, ...}` envelopes, made-up fields like `screeningScore`, `urgencyLevel`, `complianceChecks`). **Real ARA GenAiFunctions do not use that pattern at all.** They use Salesforce's native Agentforce schema format: `lightning:type`-typed properties (`lightning__recordInfoType`, `lightning__dateTimeStringType`, `lightning__textType`, `lightning__booleanType`, `lightning__listType`, `lightning__richTextType`, `lightning__objectType`), each with rich natural-language `description` fields that function as inline prompt-engineering instructions to the Agentforce reasoning engine (e.g. dictating exact date/time parsing rules, confirmation-gate behavior, or hyperlink formatting). The actual count is **44** functions, not 8.

## How to Read This Document

Each function's `invocationTarget` is either:
- **`apex`** — the function calls an Apex class (implementing the invocable-action pattern) directly, with side effects (DML, callouts, UI rendering)
- **`generatePromptResponse`** — the function is a pure LLM call against a named `GenAiPromptTemplate`, with no direct Apex side effects (the template's own Apex merge-field resolvers may still read data, but the function itself only returns generated text)

## Full Function Inventory (44)

| GenAiFunction | invocationTarget | Type | Plugin(s) | Purpose |
|---|---|---|---|---|
| `Add_Applicant_to_Job` | `CP_AddApplicantToJob` | apex | Recruiting | Add a candidate to the specified job |
| `Add_JBA_to_Job` | `ARA_AddJBAApplicantToJob` | apex | Recruiting | Add a Job Board Application to the specified job |
| `ARA_AddApplicantToJobAction` | `ARA_ApplicantAdditionToJob` | apex | Recruiting | Add a candidate to the specified job (newer variant) |
| `ARA_ApplicationNotesValidatorAction` | `ARA_ApplicationNotesValidator` | apex | JobFitAssessment | Validate candidate is associated with the job before notes/assessment actions |
| `ARA_CandidateSubmission_To_HMAction` | `ARA_CandidateSubmissionToHM` | apex | Interview_Management | Draft submission email for eligible candidates to the hiring manager |
| `ARA_CreateHiringManagerSummaryTemplateAction` | `ARA_CreateHiringManagerSummaryTemplate` | generatePromptResponse | Recruiting | Generate hiring-manager summary text |
| `ARA_CreateJobFitAssessmentTemplateAction` | `ARA_CreateJobFitAssessment` | generatePromptResponse | JobFitAssessment | Generate/modify a Job Fit Assessment for a candidate against a job |
| `ARA_CreateJobSummaryTemplateAction` | `ARA_CreateJobSummaryTemplate` | generatePromptResponse | Recruiting | Summarize job-related records; excludes empty/missing fields |
| `ARA_DateTimeZoneControllerTemplateAction` | `ARA_DateTimeZoneControllerTemplate` | generatePromptResponse | Interview_Management | Resolve correct date/time and timezone from a free-text prompt |
| `ARA_DraftEmailsAction` | `ARA_DraftEmailsForPreview` | apex | Email_Management | Generate polished email bodies for provided candidates/contact list |
| `ARA_DraftOfferEmail` | `ARA_DraftOfferEmail` | apex | Recruiting | Prepare a formal offer letter for a candidate |
| `ARA_DraftOfferLetterTemplateAction` | `ARA_DraftOfferLetterTemplate` | generatePromptResponse | Recruiting | Draft offer-letter text for a candidate/job |
| `ARA_GenerateCandidateFeedbackSummaryAction` | `ARA_GenerateCandidateFeedbackSummary` | apex | Interview_Management | Generate summary of interview feedback for a candidate |
| `ARA_GenerateCandidateJobComparisonAction` | `ARA_GenerateCandidateJobComparison` | apex | Recruitment_Marketing | Generate AI candidate-job comparison profile (skills/experience/fit) |
| `ARA_GenerateJobOverviewTemplateAction` | `ARA_JobOverviewTemplate` | generatePromptResponse | Recruiting | Return job overview from a SOQL query result |
| `ARA_GenerateOfferEmailAction` | `ARA_GenerateOfferEmail` | apex | Recruiting | Prepare a formal offer letter for a candidate (variant) |
| `ARA_GetContactAndGenerateCandidateSummaryAction` | `ARA_GetContacts` | apex | Candidate_Summary | Fetch requested contact, generate candidate summary, render in custom UI |
| `ARA_GetInterviewRecordsForUpdateAction` | `ARA_GetInterviewRecordsForUpdate` | apex | Interview_Management | Identify/retrieve interview records to be updated |
| `ARA_GetVoiceInterviewRecordsForUpdateAction` | `ARA_GetVoiceInterviewRecordsForUpdate` | apex | Reschedule_Voice_Interview | Identify/retrieve voice-interview records to be updated |
| `ARA_Get_Interviews_For_Reminder_Action` | `ARA_GetInterviewsForReminder` | apex | Interview_Reminder | Trigger/display custom form for scheduling reminder emails |
| `ARA_IdentifyJob` | `ARA_IdentifyJobForPostExternally` | apex | Sourcing_Management | Identify job(s) not yet posted externally |
| `ARA_IdentifyJobForPostExternallyAction` | `ARA_IdentifyJobForPostExternally` | apex | Sourcing_Management | Identify job records not yet posted externally (variant) |
| `ARA_IdentifyJobRecordsByHiringManagerAction` | `ARA_IdentifyJobRecordsByHiringManager` | apex | Interview_Management | Find all jobs related to a given hiring manager |
| `ARA_IdentifyRecordByFieldAction` | `ARA_IdentifyRecordByField` | apex | (shared — nearly every plugin) | Identify/retrieve records of any object by a specified field value |
| `ARA_InitiateMultiCandidateScreeningApexAction` | `ARA_InitiateMultiScreeningCall` | apex | Screening_Process | Resolve job + candidate set (by stage/contact list/names), validate, launch multi-screening UI |
| `ARA_InterviewerDetailsForAgent` | `ARA_InterviewerDetailsForAgent` | apex | Interview_Management | Resolve interviewer details when user wants to update/add/remove/reschedule interviewers |
| `ARA_JDTemplateCheckerAction` | `ARA_JDTemplateChecker` | apex | Recruitment_Marketing | Validate a job description template exists before generation |
| `ARA_JD_Generator_Template_Action` | `ARA_JD_Generator_Template` | generatePromptResponse | Recruitment_Marketing | Generate job description using a named template |
| `ARA_PostJobExternally` | `ARA_PostJobExternally` | apex | Sourcing_Management | Mark "Post Externally" checkbox true on a job record |
| `ARA_QueryApplicantByFieldsAction` | `ARA_QueryApplicantByFields` | apex | JobFitAssessment, Schedule_Voice_Interview | Query `ATS_Applicant__c` records by job/candidate field criteria |
| `ARA_QueryRecordAction` | `ARA_QueryRecord` | apex | JobFitAssessment, Sourcing_Management | Dynamically query any Salesforce object via Einstein-generated SOQL |
| `ARA_Reminder_Email_Date_Time_Template` | `ARA_Reminder_Email_Date_Time_Template` | generatePromptResponse | Interview_Reminder | Mandatory first step for date/time resolution; returns ISO 8601 |
| `ARA_RescheduleByVoiceAgentApexAction` | `ARA_RescheduleByVoiceAgent` | apex | Reschedule_Voice_Interview | Initiate voice-agent call to reschedule an existing interview |
| `ARA_SOQLGenerator` | `ARA_SOQLGenerator` | apex | Recruiting | Generate a SOQL query from a user query + object API name |
| `ARA_SaveCandidateJobComparisonAction` | `ARA_SaveCandidateJobComparison` | apex | Recruitment_Marketing | Save AI-generated candidate-job comparison to `ATS_Applicant__c` |
| `ARA_SaveFeedbackSummaryAction` | `ARA_UpdateFeedbackSummariesOnContact` | apex | Interview_Management | Update feedback summary on the Contact record |
| `ARA_SaveJobDescription` | `ARA_SaveJobDescription` | apex | Recruitment_Marketing | Save job description to the job record (always confirm with user first) |
| `ARA_ScheduleInterviewControllerApexAction` | `ARA_ScheduleInterviewController` | apex | Interview_Management | Validate and schedule an interview, checking candidate/interviewer validity |
| `ARA_ScheduleVoiceInterview` | `ARA_ScheduleVoiceInterview` | apex | Schedule_Voice_Interview | Create a task capturing job/candidate IDs for a voice-interview scheduling call |
| `ARA_SubmitCandidatesToManager` | `ARA_SubmitCandidatesToManager` | apex | Recruiting | Email candidates to the hiring/reporting manager |
| `ARA_UpdateHiringManagerSummary` | `ARA_UpdateHiringManagerSummary` | apex | Recruiting | Update Hiring Manager Summary field on Contact |
| `ARA_UpdateJobFitAssessmentAction` | `ARA_UpdateApplicationNotes` | apex | JobFitAssessment | Update Job Fit Assessment field on `ATS_Applicant__c` |
| `ARA_UpdateJobSummary` | `ARA_UpdateJobSummary` | apex | Recruiting | Update job summary field on the Job record |
| `ARA_UpdateScheduleInterviewApexAction` | `ARA_UpdateScheduleInterview` | apex | Interview_Management | Update/reschedule an existing interview |

## Representative Real Schema (ARA_ScheduleInterviewControllerApexAction)

Unlike the fabricated generic JSON schemas in the prior documentation, real input parameters are typed via `lightning:type` with instructional `description` text that directly shapes agent behavior, e.g.:

- `contact` — `lightning__recordInfoType` scoped to `Contact`
- `job` — `lightning__recordInfoType` scoped to `bpats__Job__c`
- `interviewTime` — `lightning__dateTimeStringType`, with an explicit instruction: *"If the time in interviewTime is MISSING or VAGUE... do NOT call the tool... Ask the user: 'Please confirm the exact start time for the interview.'"*
- `duration` — `lightning__integerType`, explicitly instructed never to default or reuse a prior conversational value
- `interviewerNames` — `lightning__listType` of `lightning__textType`
- `isInterviewDetailsConfirmation` — `lightning__booleanType`, defaults false, requires explicit user confirmation before scheduling proceeds
- `isCreateEvent` — `lightning__textType` capturing the platform name (e.g. "Google Meet", "Zoom Meet", "None") only if the user explicitly requests calendar event creation

Output is a single `Prompt` property (`lightning__richTextType`) with an explicit post-processing instruction: after scheduling, do not show full interview details in the chat response — only a hyperlink to the interview record.

This confirmation-gated, hyperlink-only-output pattern recurs across most "write" functions in the package (see `ARA_SaveJobDescription`, `ARA_UpdateJobFitAssessmentAction`, `ARA_SaveCandidateJobComparisonAction`) and is a defining architectural characteristic of this package that was entirely absent from the prior documentation.

## Workflow Groupings

See `ARA_GenAI_Plugins_Documentation.md` for how these 44 functions are grouped into the 11 GenAiPlugins (Agentforce Topics), including the real natural-language instruction text that governs sequencing, validation gates, and confirmation requirements for each plugin.
