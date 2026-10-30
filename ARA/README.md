# ARA (Asymbl Copilot) — Documentation Index

## Overview

**ARA** (package label **Asymbl Copilot**, namespace `bpats`) is a Salesforce managed package that adds AI-powered recruiting capabilities to the ASYMBL-ATS platform using Salesforce Agentforce / Einstein GenAI. ARA is an **add-on dependent package** — it ships no standalone data model of its own beyond a small set of supporting objects; its GenAI functions, Apex, and Lightning components operate primarily on ATS base objects (`Job__c`, `ATS_Applicant__c`, `Contact`, `Interview__c`, `Interview_Feedback__c`, `Job_Board_Application__c`) layered with its own metadata configuration objects.

This documentation set was regenerated on **2026-10-30** by direct inspection of the `ara` repository's `master` branch (commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`, 2026-09-11), superseding a prior July 2025 documentation set that was found to be materially fabricated rather than merely outdated (see `ARA_ULTRATHINK_Verification_Report.md` for the verification matrix and `ARA_Package_Overview.md` for notes on what was discarded).

## Component Summary (verified against `manifest/package.xml`, 818 total components)

| Component Type | Count |
|---|---|
| ApexClass | 174 |
| ApexTrigger | 1 |
| CustomLabel | 388 |
| CustomLabels (container) | 1 |
| EmailTemplate | 19 |
| GenAiFunction | 44 |
| GenAiPromptTemplate | 16 |
| GenAiPlugin | 11 |
| Flow | 2 |
| PermissionSet | 3 |
| CustomObject (incl. Custom Metadata Types) | 7 |
| CustomField | 85 |
| CustomMetadata (records) | 22 |
| Layout | 7 |
| LightningComponentBundle | 17 |
| LightningTypeBundle | 5 |
| RemoteSiteSetting | 1 |
| CustomTab | 1 |
| FlexiPage | 1 |
| QuickAction | 1 |
| StaticResource | 1 |
| ReportType | 2 |
| Report | 3 |
| CustomNotificationType | 2 |
| CustomPermission | 1 |
| BotTemplate | 1 |
| GenAiPlannerBundle | 1 |
| ListView | 1 |
| **TOTAL** | **818** |

## Documentation Files in This Folder

- **README.md** — this file
- **ARA_Package_Overview.md** — executive summary, true architecture, capability areas
- **ARA_Apex_Architecture.md** — full Apex class inventory grouped by functional domain
- **ARA_Custom_Labels_Documentation.md** — label inventory and categorization (404 labels: 398 `ARA`, 6 `CP`)
- **ARA_Email_Templates_Documentation.md** — all 19 email templates (2 folders: `ARA`, `ARA_BaseEmailTemplates`)
- **ARA_GenAI_Functions_Documentation.md** — all 44 GenAiFunctions with real invocationTarget/schema data
- **ARA_GenAI_Plugins_Documentation.md** — all 11 GenAiPlugins (Agentforce Topics) with real function groupings and instructions
- **ARA_GenAI_Prompt_Templates_Documentation.md** — all 16 GenAiPromptTemplates
- **Agentforce_Components_Research.md** — platform-level reference on GenAiFunction/GenAiPromptTemplate/GenAiPlugin metadata (retained, lightly corrected — this file was general Salesforce platform research, not ARA-specific fabrication)
- **ERD_ARA_Recruiting_Objects.md** — ERD for ARA's 7 custom objects and their relationships to ATS base objects
- **ARA_ULTRATHINK_Verification_Report.md** — real component-count verification matrix vs. documentation coverage

## Key Correction Notice

The documentation previously in this folder (dated ~2025-07-10) significantly **misrepresented** the ARA package: it described a 64-component package built on TensorFlow/Kubernetes/Kafka/GraphQL with fictional REST APIs, SOC 2/ISO 27001 certifications, and custom objects (`Job_Application__c`, `AI_Metrics__c`, `Plugin_Audit__c`) that do not exist anywhere in this codebase. That content has been discarded in full. The current set reflects only what is verifiably present in `ara:manifest/package.xml` and `force-app/main/default` on `master` as of commit `ba1083bd`.

Most significant net-new coverage: a complete **Voice Interview / Voice Agent subsystem** (7 custom objects, voice screening/scheduling/rescheduling GenAiPlugins, voice-related Apex batches and webhook handlers, voice-specific email templates and custom notification types) that did not appear anywhere in the prior documentation.
