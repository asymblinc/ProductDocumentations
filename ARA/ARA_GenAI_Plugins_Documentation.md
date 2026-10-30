# ARA (Asymbl Copilot) GenAI Plugins Documentation

*Regenerated 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`.*

## Correction Notice

The prior version documented 2 plugins (`Recruiting`, `Interview_Management`) with fabricated REST API endpoints (`/ara/v1/recruiting/*`), fabricated custom objects (`PerformanceMetric__c`, `Plugin_Audit__c`), fabricated SLA numbers, and invented `PluginConfiguration` JSON that does not correspond to any real Salesforce `GenAiPlugin` metadata shape. **GenAiPlugin in Salesforce has no REST layer, no performance-monitoring custom objects, and no plugin-level JSON configuration block** — it is declarative Agentforce Topic metadata (`pluginType=Topic`, `scope`, `genAiFunctions`, `genAiPluginInstructions`, optional `aiPluginUtterances`). The actual count is **11** plugins.

## What a GenAiPlugin Actually Is (per real metadata on this org)

A `GenAiPlugin` is an **Agentforce Topic**: it declares a `scope` (what the topic covers, used by Atlas to route user intent), a set of `genAiFunctions` (the functions available once this topic is active), one or more `genAiPluginInstructions` blocks (long-form natural-language instructions — often hundreds of lines of prescriptive workflow logic with validation gates, confirmation requirements, and formatting rules), and optionally `aiPluginUtterances` (example phrases used for topic-routing/training).

## Full Plugin Inventory (11)

### 1. Recruiting
**Functions (14)**: `EmployeeCopilot__IdentifyObjectByName`, `ARA_IdentifyRecordByFieldAction`, `EmployeeCopilot__QueryRecords`, `ARA_CreateJobSummaryTemplateAction`, `ARA_GenerateJobOverviewTemplateAction`, `ARA_GenerateOfferEmailAction`, `ARA_AddApplicantToJobAction`, `Add_JBA_to_Job`, `ARA_UpdateJobSummary`, `ARA_UpdateHiringManagerSummary`, `ARA_SOQLGenerator`, `EmployeeCopilot__GetRecordDetails`, `ARA_CreateHiringManagerSummaryTemplateAction`, `ARA_DraftOfferLetterTemplateAction`

**Scope**: Hiring manager summaries, offer letter drafting/modification (with user confirmation before send), job summary generation/update, candidate-to-job addition (duplicate-checked, confirmed before save), and job activity overviews filtered by time period.

### 2. Candidate_Summary
**Functions (1)**: `ARA_GetContactAndGenerateCandidateSummaryAction`

**Scope**: Generates/updates AI candidate profile summaries from Contact data only — explicitly **excludes** feedback summaries and hiring-manager summaries, which are routed to other topics. Real instruction text mandates aggressive name extraction from the user's prompt (first name, last name, partial term — "test", "ra") and passes it through a `contactName` parameter, defaulting to empty string only if truly no name is present.

### 3. Interview_Management
**Functions (12)**: `ARA_GenerateCandidateFeedbackSummaryAction`, `ARA_SaveFeedbackSummaryAction`, `ARA_InterviewerDetailsForAgent`, `EmployeeCopilot__GetRecordDetails`, `EmployeeCopilot__QueryRecords`, `ARA_ScheduleInterviewControllerApexAction`, `ARA_UpdateScheduleInterviewApexAction`, `ARA_DateTimeZoneControllerTemplateAction`, `ARA_IdentifyRecordByFieldAction`, `ARA_IdentifyJobRecordsByHiringManagerAction`, `ARA_CandidateSubmission_To_HMAction`, `ARA_GetInterviewRecordsForUpdateAction`

**Scope**: Interview scheduling (Google Meet, Zoom, in-person), feedback summarization, and email-based candidate submission to hiring managers.

### 4. Interview_Reminder
**Functions (3)**: `ARA_Reminder_Email_Date_Time_Template`, `ARA_Get_Interviews_For_Reminder_Action`, `EmployeeCopilot__GetRecordDetails`

**Scope**: Dedicated, narrowly-scoped reminder-only topic. Real `description` explicitly states it does **not** schedule interviews, submit candidates, or summarize feedback — those are deliberately excluded to avoid topic-routing collisions with Interview_Management.

### 5. Email_Management
**Functions (4)**: `EmployeeCopilot__GetRecordDetails`, `ARA_IdentifyRecordByFieldAction`, `ARA_DraftEmailsAction`, `EmployeeCopilot__QueryRecords`

**Scope**: Draft/preview/generate professional candidate emails, identifying candidates by name, current record page, or contact list, optionally scoped to exactly one job. This plugin's real instructions are the most elaborate in the package — a strict multi-step validation-gate workflow (identify contact list → identify candidate(s) → identify job (optional, max one) → final pre-flight confirmation → generate). Hard rules embedded in the instructions include: always render record names as hyperlinks, never display or ask for record IDs, never allow more than one job to be selected, and never proceed past a validation gate without explicit user confirmation ("yes"/"confirm").

### 6. Recruitment_Marketing
**Functions (9)**: `ARA_IdentifyRecordByFieldAction`, `EmployeeCopilot__QueryRecords`, `EmployeeCopilot__GetRecordDetails`, `ARA_JD_Generator_Template_Action`, `ARA_JDTemplateCheckerAction`, `ARA_SaveJobDescription`, `ARA_GenerateCandidateJobComparisonAction`, `ARA_SaveCandidateJobComparisonAction`, `ARA_ApplicationNotesValidatorAction`

**Scope**: Job Description (JD) generation/modification and Candidate-Job Comparison profile generation (skills alignment, experience match, overall fit). Requires an explicit success-message confirmation to the user after a JD update is saved to the job record.

### 7. JobFitAssessment
**Functions (5)**: `ARA_CreateJobFitAssessmentTemplateAction`, `ARA_UpdateJobFitAssessmentAction`, `ARA_IdentifyRecordByFieldAction`, `ARA_QueryRecordAction`, `ARA_QueryApplicantByFieldsAction`

**Scope**: Job Fit Assessment generation and application-notes management, analyzing a candidate against a specific job's requirements. Real description emphasizes a **strict approval workflow** — the system "never saves automatically"; assessments are only persisted to `ATS_Applicant__c` after explicit user confirmation.

### 8. Sourcing_Management
**Functions (3)**: `ARA_PostJobExternally`, `ARA_IdentifyJob`, `ARA_QueryRecordAction`

**Scope**: Identify a job (by title or current record-page context) that has not yet been posted externally, present ambiguous matches as a hyperlinked list for user selection, then post the confirmed job externally.

### 9. Schedule_Voice_Interview
**Functions (5)**: `ARA_IdentifyRecordByFieldAction`, `ARA_QueryApplicantByFieldsAction`, `EmployeeCopilot__GetRecordDetails`, `EmployeeCopilot__QueryRecords`, `ARA_ScheduleVoiceInterview`

**Scope**: Creates tasks to initiate AI voice-agent calls for interview scheduling. Only triggers when the user explicitly says "voice interview" or "voice call" — deliberately scoped away from the text/manual Interview_Management topic.

### 10. Reschedule_Voice_Interview
**Functions (3)**: `ARA_IdentifyRecordByFieldAction`, `ARA_GetVoiceInterviewRecordsForUpdateAction`, `ARA_RescheduleByVoiceAgentApexAction`

**Scope**: Reschedules an existing interview via an outbound voice-agent phone call. Real description requires the user's initial prompt to explicitly contain both "reschedule" AND ("voice interview" OR "voice call") — general (non-voice) rescheduling is explicitly routed to Interview_Management instead.

### 11. Screening_Process
**Functions (3)**: `EmployeeCopilot__GetRecordDetails`, `ARA_IdentifyRecordByFieldAction`, `ARA_InitiateMultiCandidateScreeningApexAction`

**Scope**: Conducts AI voice screening calls for **multiple** candidates at once against a given job, where the candidate group is defined by pipeline stage, contact list name, or an explicit list of names. Launches a Multi Screening Call LWC that renders pre-call validation cards (Job Assignment Check, Mobile Number Check, screening-question configuration) for all selected candidates simultaneously.

## Cross-Cutting Observations

- **`EmployeeCopilot__*` functions** (`GetRecordDetails`, `QueryRecords`, `IdentifyObjectByName`) appear across 7 of the 11 plugins. These are standard Salesforce/Einstein Copilot platform actions, not ARA-authored functions — ARA composes them alongside its own custom `ARA_*` actions rather than reimplementing generic record lookup from scratch.
- **Deliberate topic-boundary instructions** are a recurring, explicit design pattern: several plugins' instructions explicitly state what they must *not* handle, to prevent Atlas from mis-routing a user request to the wrong topic (e.g., Interview_Reminder vs. Interview_Management; Schedule_Voice_Interview vs. Interview_Management; Reschedule_Voice_Interview vs. Interview_Management).
- **Confirmation-gated writes** are universal: every plugin that performs a save/send/post action (Recruiting's offer/job-summary/candidate-add flows, Recruitment_Marketing's JD save, JobFitAssessment's assessment save, Sourcing_Management's external post, Email_Management's email generation) requires an explicit user confirmation step before the corresponding Apex action executes.
- **No plugin-invocation REST API exists.** All plugin-to-function-to-Apex invocation happens through Salesforce's native Agentforce/Atlas invocation model, not a custom REST layer. The package does contain exactly **2** `@RestResource` classes (`ARA_VoiceInterviewWebhookHandler` at `/voiceinterview/webhook/*`, `ARA_CandidateScreeningWebhookHandler`), but these are **inbound** webhook receivers for callbacks from the external voice-calling provider (call analysis results, screening responses) — the reverse direction from the fabricated outbound `/ara/v1/recruiting/*` and `/ara/v1/interview/*` "Plugin APIs" described in the prior documentation, which do not exist.
