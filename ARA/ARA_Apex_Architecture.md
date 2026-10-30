# ARA (Asymbl Copilot) Apex Architecture Document

*Regenerated 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`.*

## Correction Notice

The prior version of this document documented 25 Apex classes. The actual count on `master` is **174** (97 non-test / ~77 test classes), plus 1 trigger. The prior document's design-pattern description (Main + Helper + Test triad, consistent error handling via Custom Labels) remains directionally accurate and is retained below, now applied to the full real inventory.

## Component Groups

### 1. Job Summary & Job Lifecycle
- `ARA_GenerateJobSummary`, `ARA_JobSummaryHelper`, `ARA_UpdateJobSummary`, `ARA_UpdateJobSummaryBatch`, `ARA_JobSummaryController` — generate/refresh AI job summaries on `Job__c.Job_Summary__c`, both on-demand and via nightly/batch regeneration
- `ARA_JobTriggerHandler`, `ARA_JobTriggerHelper`, `ARA_TriggerHandler` (base handler framework) — fired from `ARA_JobTrigger` on `bpats__Job__c` (all before/after insert/update/delete/undelete contexts)
- `ARA_GenerateJobOverview` — produces job-activity overview (time-period/filter driven), backs the `ARA_GenerateJobOverviewTemplateAction` GenAiFunction
- `ARA_GenerateJobDescriptionTemplate`, `ARA_SaveJobDescription`, `ARA_JDTemplateChecker` — JD generation/validation/save workflow (Recruitment_Marketing plugin)
- `ARA_PostJobExternally`, `ARA_IdentifyJobForPostExternally` — external job-board posting (Sourcing_Management plugin)

### 2. Candidate Summary
- `ARA_GenerateCandidateSummary`, `ARA_CandidateSummaryHelper`, `ARA_UpdateCandidateSummary`, `ARA_UpdateCandidateSummaryOnContactBatch`, `ARA_CandidateSummaryGeneratorController`, `ARA_GetContacts` — generate/update AI summaries on `Contact.Candidate_Summary__c` / `Overall_summary__c`, surfaced through a custom UI (`araCandidateSummaryGeneratorPage` LWC)

### 3. Hiring Manager Summary
- `ARA_GetHiringManagerInfo`, `ARA_HiringManagerInfoHelper`, `ARA_UpdateHiringManagerSummary` — writes `Contact.Hiring_Manager_Summary__c`

### 4. Candidate Submission / Hiring Manager Communication
- `ARA_SubmitCandidatesToManager`, `ARA_SubmitCandidatesToManagerHelper` — original candidate-submission-by-email flow
- `ARA_CandidateSubmission`, `ARA_CandidateSubmissionToHM`, `ARA_CandidateSubmissionToHMHelper`, `ARA_CandidateSubmissionEmailController` — expanded hiring-manager submission/email workflow (Interview_Management plugin)

### 5. Interview Scheduling & Management
- `ARA_ScheduleInterview`, `ARA_ScheduleInterviewHelper`, `ARA_ScheduleInterviewController`, `ARA_ScheduleInterviewForApplicantHelper` — core scheduling (Google Meet/Zoom/in-person)
- `ARA_UpdateScheduleInterview`, `ARA_UpdateScheduleInterviewHelper` — reschedule/update an existing interview
- `ARA_GetInterviewRecordsForUpdate`, `ARA_InterviewerDetailsForAgent` — interview lookups used by Agentforce to resolve records before update
- `ARA_AvailabilitySlotFinder` — interviewer slot-availability resolution
- `ARA_TimeZoneController` — timezone normalization for scheduling

### 6. Interview Reminders
- `ARA_ScheduleReminderEmail`, `ARA_ScheduledEmailSender`, `ARA_GetInterviewsForReminder`, `ARA_InterviewReminderWizardController` — reminder-email scheduling and the multi-step reminder wizard LWC backend

### 7. Interview Feedback
- `ARA_CandidatesSummarisedFeedback`, `ARA_GenerateCandidateFeedbackSummary`, `ARA_UpdateFeedbackSummariesOnContact` — aggregate/summarize interviewer feedback and persist to `Contact`

### 8. Candidate–Job Comparison & Job Fit Assessment
- `ARA_CandidateDataForJobComparision`, `ARA_CandidateJobComparisonHelper`, `ARA_GenerateCandidateJobComparison`, `ARA_SaveCandidateJobComparison` — candidate-job comparison profile generation (writes `ATS_Applicant__c.Candidate_Job_Comparison__c`)
- `ARA_UpdateApplicationNotes`, `ARA_UpdateApplicationNotesBatch`, `ARA_ApplicationNotesValidator` — job-fit assessment / application-notes workflow (writes `ATS_Applicant__c.Enhanced_Application_Notes__c`), paired with `ARA_UpdateJobFitAssessmentFlow`

### 9. Offer Management
- `ARA_DraftOfferEmail`, `ARA_DraftOfferEmailHelper`, `ARA_GenerateOfferEmail` — offer letter drafting and email generation (uses `ARA/ARA_Offer_Draft_Template`)

### 10. Applicant / Job-Board Application Addition
- `CP_AddApplicantToJob`, `ARA_ApplicantAdditionToJob`, `ARA_ApplicantRetriever` — add a candidate to a job
- `ARA_AddJBAApplicantToJob` — add a Job Board Application record to a job

### 11. Record Identification / Query Utilities (shared across most plugins)
- `ARA_IdentifyRecordByField`, `ARA_IdentifyJobRecordsByHiringManager`, `ARA_QueryApplicantByFields`, `ARA_QueryRecord`, `ARA_SOQLGenerator` — generic natural-language-to-SOQL and field-based record lookup used across nearly every plugin to resolve ambiguous user references to concrete records
- `ARA_GetJobQuestions` — retrieves job screening questions

### 12. Email Infrastructure
- `ARA_EmailUtility`, `ARA_DraftEmailsForPreview`, `ARA_SendEmailController` — shared email send/preview infrastructure used by Email_Management plugin and candidate-submission flows
- `ARA_OwaErrorNotificationQueueable` — queued org-wide-address error notifications

### 13. Voice Interview / Voice Agent Subsystem
- `ARA_ScheduleVoiceInterview`, `ARA_VoiceInterviewHandler`, `ARA_VoiceInterviewCalloutHandler`, `ARA_VoiceInterviewWebhookHandler` — outbound voice-call scheduling and inbound webhook processing
- `ARA_RescheduleByVoiceAgent`, `ARA_GetVoiceInterviewRecordsForUpdate` — voice-driven rescheduling
- `ARA_VoiceInterviewTaskBatch`, `ARA_FailureNotifyVoiceAgentBatch` — background task/notification batches for voice interviews
- `ARA_VoiceMockGenerator`, `ARA_VoiceCalloutMockMultiResponse`, `ARA_MockGenerator` — HTTP callout mocks for voice-agent integration tests

### 14. Candidate Screening (Multi-Candidate Voice Screening)
- `ARA_InitiateMultiScreeningCall`, `ARA_InitiateScreeningCall`, `ARA_ProcessScreeningQuestions`, `ARA_CandidateScreeningWebhookHandler`, `ARA_ScreeningResponseController` — orchestrate multi-candidate phone screening calls and capture responses
- `ARA_ScreeningCallBatch`, `ARA_ScreenRetryBatch`, `ARA_ScreenRetryNotificationBatch`, `ARA_ScreeningCallSummaryBatch`, `ARA_ScreeningCompletionBellBatch` — batch lifecycle for screening calls (initiation, retries, summary email, completion notification)

### 15. Configuration & Notes Cleanup
- `ARA_ActionComponentConfiguration`, `ARA_ActionComponentConfigurationUtil` — reads `ARA_Action_Component_Configuration__mdt` to drive conditional UI/action behavior
- `ARA_NotesCleanupController` — backs the `Interview__c.Notes_Cleanup_Agent` QuickAction

### 16. Shared Utilities
- `ARA_Utility`, `ARA_TestUtility` — cross-cutting helper and test-data-factory utilities
- `CP_Constants` — shared constants (CP prefix = Candidate Portal / core-platform utilities inherited from base ATS)

### 17. Test Classes
Each main/helper class above has a corresponding `*Test` class (roughly 77 of the 174 total classes), following the package's enforced minimum code-coverage convention. Representative examples: `ARA_ScheduleInterviewTest`, `ARA_DraftOfferEmailTest`, `ARA_VoiceInterviewWebhookHandlerTest`, `ARA_ScreeningCallBatchTest`, `CP_AddApplicantToJobTest`.

## Design Patterns

### Main Class + Helper Class + Test Class
The dominant pattern remains: a thin "Main" class exposed to GenAiFunction/Apex-action invocation, a "Helper" class containing the actual business logic and SOQL/DML, and a dedicated test class. This is now applied across roughly 30+ Main/Helper pairs rather than the single handful previously documented.

### GenAI Integration Pattern
1. GenAiPlugin instructions (natural language) steer the Agentforce reasoning engine on when/how to call a function.
2. GenAiFunction (`apex` type) invokes the Main Apex class with structured `lightning:type`-typed inputs.
3. Main class validates inputs, calls Helper class for business logic.
4. Result is returned via the function's output schema (often `lightning__richTextType` or `lightning__objectType` payloads consumed directly by the Agentforce response, not a generic JSON envelope).

### Trigger Framework
`ARA_JobTrigger` (the package's only Apex trigger) delegates entirely to `ARA_JobTriggerHandler`, which itself extends/uses the shared `ARA_TriggerHandler` base class — a conventional one-trigger-per-object, handler-class pattern.

## Security Architecture

Three permission sets now govern ARA access (not one, as previously documented):
- **Asymbl_Recruiting_Agent** — general ARA / Agentforce Copilot access
- **Screening_Agent_Permission** — multi-candidate voice screening capability
- **Voice_Agent_Permissions** — voice interview scheduling/rescheduling capability

Plus 1 CustomPermission (`ARA_Job_Summary_Permission`) gating job-summary-specific behavior.
