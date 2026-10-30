# ARA (Asymbl Copilot) GenAI Prompt Templates Documentation

*Regenerated 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`.*

## Correction Notice

The prior version documented 2 prompt templates (`Generating_summary_of_hiring_manager`, `Candidate_Feedback_Summary_For_All_Jobs`) — **neither name exists as a `GenAiPromptTemplate` in the current codebase.** The actual count is **16**, all of type `einstein_gpt__flex` (Prompt Builder "Flex" templates, Salesforce's freeform prompt template type that supports merge fields, grounding, and multi-step resolvers).

## Full Template Inventory (16)

| Template (DeveloperName) | Consumed By (GenAiFunction) | Plugin | Purpose |
|---|---|---|---|
| `ARA_Candidate_Summary` | `ARA_GetContactAndGenerateCandidateSummaryAction` | Candidate_Summary | Generates/edits the candidate profile summary from Contact + related object data |
| `ARA_CreateHiringManagerSummaryTemplate` | `ARA_CreateHiringManagerSummaryTemplateAction` | Recruiting | Generates the hiring-manager summary |
| `ARA_CreateJobFitAssessment` | `ARA_CreateJobFitAssessmentTemplateAction` | JobFitAssessment | Generates/modifies a Job Fit Assessment of a candidate against a job |
| `ARA_CreateJobSummaryTemplate` | `ARA_CreateJobSummaryTemplateAction` | Recruiting | Summarizes job-related records, excluding empty/missing fields |
| `ARA_CreateComparisionSummaryTemplate` | (feeds `ARA_GenerateCandidateJobComparisonAction`) | Recruitment_Marketing | Candidate-job comparison profile generation (skills/experience/fit narrative) |
| `ARA_DateTimeZoneControllerTemplate` | `ARA_DateTimeZoneControllerTemplateAction` | Interview_Management | Resolves correct date/time/timezone from free-text prompt |
| `ARA_ParseDateTimeWithTimeZoneTemplate` | (supporting date/time parsing) | Interview_Management | Secondary date/time + timezone parsing template |
| `ARA_DraftOfferLetterTemplate` | `ARA_DraftOfferLetterTemplateAction` | Recruiting | Drafts offer-letter text for a candidate/job |
| `ARA_GenerateEmailTemplate` | (feeds `ARA_DraftEmailsAction`) | Email_Management | Generates candidate email bodies for preview |
| `ARA_GenerateFeedbackSummary` | (feeds `ARA_GenerateCandidateFeedbackSummaryAction`) | Interview_Management | Summarizes multi-interviewer feedback for a candidate |
| `ARA_Generate_SOQL_Template` | `ARA_SOQLGenerator` | Recruiting | Generates a SOQL query from natural-language user input + target object |
| `ARA_JD_Generator_Template` | `ARA_JD_Generator_Template_Action` | Recruitment_Marketing | Generates a Job Description using a named JD template |
| `ARA_JobOverviewTemplate` | `ARA_GenerateJobOverviewTemplateAction` | Recruiting | Produces a job activity overview from SOQL query results |
| `ARA_Notes_CleanUP_Template_V2` | (backs the `Interview__c.Notes_Cleanup_Agent` QuickAction / `ARA_NotesCleanupController`) | — (QuickAction-driven, not a Topic plugin) | Cleans up/reformats interview notes text |
| `ARA_Reminder_Email_Date_Time_Template` | `ARA_Reminder_Email_Date_Time_Template` | Interview_Reminder | Mandatory first-step date/time resolution for reminder scheduling; must return ISO 8601 |
| `ARA_ScreeningQuestionReframeTemplate_v4` | (supports `ARA_ProcessScreeningQuestions` / multi-candidate screening) | Screening_Process | Reframes/normalizes screening questions for voice-agent delivery |

## Design Pattern

All 16 templates are `einstein_gpt__flex` type, consistent with a package-wide decision to use Prompt Builder's Flex template type rather than the more rigid Field Generation or Record Summary template types — this gives each template full control over merge-field resolvers (often backed by invocable Apex) and multi-section structure, which is necessary given how much conditional, confirmation-gated logic the GenAiPlugin instructions push down into these templates (e.g. `ARA_Reminder_Email_Date_Time_Template` being explicitly marked "MANDATORY FIRST STEP" in its consuming function's description).

Several templates exist specifically to **isolate a single sub-task** (date/time resolution, SOQL generation, notes cleanup, screening-question reframing) that is then composed into a larger GenAiFunction/plugin workflow — rather than one monolithic prompt per plugin. This decomposition pattern recurs across the Interview_Management, Recruiting, and Screening_Process plugins.

## Relationship to GenAiFunctions

10 of the 16 templates map 1:1 to a `generatePromptResponse`-type GenAiFunction of a closely related name (see `ARA_GenAI_Functions_Documentation.md`). The remaining 6 (`ARA_CreateComparisionSummaryTemplate`, `ARA_GenerateEmailTemplate`, `ARA_GenerateFeedbackSummary`, `ARA_ParseDateTimeWithTimeZoneTemplate`, `ARA_Notes_CleanUP_Template_V2`, `ARA_ScreeningQuestionReframeTemplate_v4`) are invoked indirectly — resolved from within an `apex`-type GenAiFunction's Apex class (e.g. via `Messaging.PromptTemplate` / `ConnectApi` prompt-execution calls) rather than being the function's direct `invocationTarget`. Confirming the exact internal call site for each would require tracing individual Apex class bodies, which is out of scope for this metadata-level inventory; flagged here as the one area of this document where API-schema-level verification (rather than package.xml/metadata-level verification) would be needed for full confidence.
