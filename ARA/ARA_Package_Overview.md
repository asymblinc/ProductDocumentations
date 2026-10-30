# ARA (Asymbl Copilot) Package — Technical Overview

*Regenerated 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878` (2026-09-11).*

## Correction Notice

This document fully replaces a prior (~2025-07-10) version that described ARA as a 64-component, TensorFlow/Kubernetes/Kafka/GraphQL-based platform with REST APIs, SOC 2/ISO 27001 certification, and fabricated custom objects (`Job_Application__c`, `AI_Metrics__c`, `Plugin_Audit__c`, `PerformanceMetric__c`, `ErrorLog__c`). None of that existed in this codebase at any point visible in git history reachable from `master`. The content below reflects only what is present in `ara:manifest/package.xml` and `force-app/main/default`.

## Executive Summary

ARA (package label **Asymbl Copilot**, namespace `bpats`) is a Salesforce **managed package add-on** to ASYMBL-ATS (`bpats`), delivering AI-powered recruiting automation entirely through native Salesforce technology: **Apex**, **Agentforce/Einstein GenAI metadata** (GenAiFunction, GenAiPromptTemplate, GenAiPlugin), **Lightning Web Components**, **Flow**, and standard Salesforce email/notification infrastructure. There is no external microservices layer, no separate ML training pipeline, and no REST API surface beyond what Agentforce's own invocation model provides — AI reasoning is delegated to Salesforce's Einstein/Atlas layer via `GenAiPromptTemplate` (`generatePromptResponse` invocation type) and custom Apex actions (`apex` invocation type) registered as `GenAiFunction`s.

## What ARA Actually Ships (818 components)

| Category | Components | Notes |
|---|---|---|
| Apex | 174 classes + 1 trigger (`ARA_JobTrigger` on `bpats__Job__c`) | ~97 non-test classes, ~77 test classes |
| GenAI / Agentforce | 44 GenAiFunction, 16 GenAiPromptTemplate, 11 GenAiPlugin, 1 BotTemplate (`Rosa_v1_Template`), 1 GenAiPlannerBundle | Full Agentforce agent ("Rosa") built on these |
| Data model (ARA-owned) | 7 CustomObjects: 6 Custom Metadata Types + 1 custom object (`ARA_Voice_Agent_Call_Log__c`), 85 CustomFields, 22 CustomMetadata records | ARA otherwise extends ATS base objects via new fields, not new sObjects |
| Communication | 19 EmailTemplate (2 folders: `ARA`, `ARA_BaseEmailTemplates`), 2 CustomNotificationType | |
| UI | 17 LightningComponentBundle, 5 LightningTypeBundle, 1 FlexiPage, 1 CustomTab, 1 QuickAction, 7 Layout, 1 ListView | |
| Automation | 2 Flow (`ARA_UpdateJobFitAssessmentFlow`, `ARA_Voice_Interview_Flow`) | |
| Security | 3 PermissionSet (`Asymbl_Recruiting_Agent`, `Screening_Agent_Permission`, `Voice_Agent_Permissions`), 1 CustomPermission | |
| Reporting | 2 ReportType, 3 Report (folder `ARAReports`) | |
| Other | 388 CustomLabel + 1 CustomLabels container, 1 StaticResource, 1 RemoteSiteSetting (`Voice_Agent_Remote_Setting`) | |

## Capability Areas (grouped by GenAiPlugin / Agentforce Topic)

ARA organizes its AI capabilities into **11 GenAiPlugins**, each acting as an Agentforce "Topic" with its own scope, utterances, and natural-language instructions consumed by the org's Agentforce agent:

1. **Recruiting** — job summaries, hiring-manager summaries, offer letter drafting, adding candidates/JBAs to jobs, dynamic SOQL generation
2. **Candidate_Summary** — generate/update AI candidate profile summaries on `Contact`
3. **Interview_Management** — interview scheduling (Google Meet/Zoom/in-person), rescheduling, feedback summarization, candidate submission to hiring managers by email
4. **Interview_Reminder** — dedicated reminder-email scheduling for upcoming interviews
5. **Email_Management** — draft/preview candidate emails for an individual or a contact list, optionally scoped to one job
6. **Recruitment_Marketing** — Job Description (JD) generation/editing, candidate-job comparison profile generation
7. **JobFitAssessment** — AI job-fit assessment generation and application-notes management against `ATS_Applicant__c`, with mandatory human-confirmation-before-save workflow
8. **Sourcing_Management** — identify and post a job externally
9. **Schedule_Voice_Interview** — initiate outbound AI voice-agent calls to schedule interviews
10. **Reschedule_Voice_Interview** — reschedule an existing interview via outbound voice-agent call
11. **Screening_Process** — multi-candidate AI voice screening calls for a job, driven by pipeline stage / contact list / explicit candidate names

See `ARA_GenAI_Plugins_Documentation.md` for full function groupings, real instruction text, and utterance examples per plugin.

## Voice Interview / Voice Agent Subsystem (net-new vs. prior docs)

A substantial subsystem not present in the previous documentation set: ARA integrates with an outbound/inbound voice-calling agent (via `RemoteSiteSetting: Voice_Agent_Remote_Setting` and custom metadata credentials in `ARA_Voice_Interview_Setting__mdt`) to conduct AI voice calls for interview scheduling, interview rescheduling, and multi-candidate phone screening. Supporting metadata:

- **Custom object**: `ARA_Voice_Agent_Call_Log__c` — one call-log record per voice interaction, with lookups to `Account`, `Contact` (candidate), `Job__c`, and `User` (recruiter); tracks `Status__c` (Connected/Not Connected), `Type__c` (Screen Applied Candidate / Schedule Interview / Reschedule Interview), transcript, call duration, disconnection reason, and consent flags (`isConsent__c`, `isConnected__c`, `isInterviewScheduled__c`).
- **Custom Metadata Types**: `ARA_Voice_Agent_Parameter_Mapping__mdt` (per-agent business-hours, slot duration, call-limit, retry/notify configuration) and `ARA_Voice_Interview_Setting__mdt` (API key, endpoint URL, from-phone-number for the voice provider integration).
- **Batches/handlers**: `ARA_ScreeningCallBatch`, `ARA_ScreenRetryBatch`, `ARA_ScreenRetryNotificationBatch`, `ARA_ScreeningCallSummaryBatch`, `ARA_ScreeningCompletionBellBatch`, `ARA_VoiceInterviewTaskBatch`, `ARA_FailureNotifyVoiceAgentBatch`, `ARA_VoiceInterviewCalloutHandler`, `ARA_VoiceInterviewWebhookHandler`, `ARA_CandidateScreeningWebhookHandler`.
- **Email templates**: 6 of the 19 email templates are voice-interview-specific (confirmation/disconnect/failure notifications to candidate and recruiter, screening batch summaries).
- **Permission set**: `Voice_Agent_Permissions` is dedicated to this subsystem; `Screening_Agent_Permission` governs the multi-candidate screening flow.
- **Custom notification types**: `ScreeningCallCompleted`, `VoiceInterviewEmailNotSendedNotify`.

## ARA's Footprint on ATS Base Objects

Rather than introducing a parallel data model, ARA adds fields directly to existing ATS/standard objects:

- **Contact**: `Overall_summary__c`, `Candidate_Summary__c`, `Is_Candidate_Summary_Generated__c`, `Hiring_Manager_Summary__c`, `Account_Name__c`
- **Job__c**: `Job_Summary__c`, `Job_Open_Days__c`, `Is_Job_Summary_Generated__c`, `Job_Summary_Updated_Date__c`, `Job_Close_Date__c`, `Today_Date__c`, `Postion_Fill_Date__c`
- **ATS_Applicant__c**: `Enhanced_Application_Notes__c`, `Is_Application_Notes_Generated__c`, `Candidate_Job_Comparison__c`
- **Job_Board_Application__c**: `Full_Name__c`
- **Interview__c**: `Call_Details_JSON__c`, plus a `Notes_Cleanup_Agent` QuickAction
- **Interview_Feedback__c**: `Old_Feedback__c`
- **Activity** (Task): `Online_Platform__c`, `TaskType__c`, `Number_of_Calls__c`, `Is_Follow_Up_Email_Sent__c`, `Is_Call_Limit_Reached__c`, `Is_Email_Verification_Failed__c`, `Is_Human_Requested__c`
- **ATS_Question_Response__c**: `Voice_Agent_Call_Log__c` lookup back to `ARA_Voice_Agent_Call_Log__c`

See `ERD_ARA_Recruiting_Objects.md` for the full relationship diagram.

## Architecture Pattern

ARA follows a consistent **Apex action + GenAiFunction + GenAiPlugin** layering:

1. A **GenAiPlugin** (Agentforce Topic) groups related functions and carries natural-language instructions that steer the Agentforce reasoning engine (validation gates, confirmation requirements, hyperlink formatting rules, etc. — these are extensive and prescriptive in the real metadata; see the Email_Management and JobFitAssessment plugins for examples of mandatory approval workflows).
2. Each **GenAiFunction** has an `invocationTarget` of type either `apex` (points to an Apex class implementing `InvocableMethod`-style action classes, e.g. `ARA_ScheduleInterviewController`) or `generatePromptResponse` (points to a `GenAiPromptTemplate` that performs pure LLM generation with no Apex side effects, e.g. `ARA_CreateJobSummaryTemplate`).
3. Apex action classes typically follow a **Main class + Helper class + Test class** triad (e.g. `ARA_ScheduleInterview` / `ARA_ScheduleInterviewHelper` / `ARA_ScheduleInterviewTest`), consistent with the pattern documented (correctly) in the prior Apex Architecture doc, just at roughly 4x the real scale (97 non-test classes vs. the ~22 previously documented).
4. Batch Apex (`ARA_UpdateJobSummaryBatch`, `ARA_UpdateCandidateSummaryOnContactBatch`, `ARA_UpdateApplicationNotesBatch`, the Voice Interview batches, etc.) handles bulk/background summary regeneration and voice-call lifecycle processing.

## Dependency Context

Per `sfdx-project.json`, ARA depends on ASYMBL-ATS (`bpats`) — it is not a standalone installable product. Multiple GenAiFunctions and Apex classes directly reference ATS objects with the `bpats__` namespace prefix in their schema (`bpats__Job__c`, `bpats__Contact_List__c`, `bpats__Job_Title__c`), confirming the dependency is live and load-bearing, not merely declared.
