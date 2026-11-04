# ARA Package Documentation

Welcome to the comprehensive documentation for the **ARA (Asymbl Copilot) Package** - a Salesforce managed package that adds AI-powered recruiting capabilities to the ASYMBL-ATS platform using Salesforce Agentforce / Einstein GenAI.

> **Source of truth**: This documentation reflects the `ara` repository `master` branch as of 2026-09-11 (commit `ba1083bd`, 818 total components). It supersedes the 2025-07-10 revision, which was found to be fabricated rather than merely outdated — it described a 64-component package built on a nonexistent TensorFlow/Kubernetes/Kafka/GraphQL stack, with fictional REST APIs, fictional SOC 2/ISO 27001 certifications, and custom objects that never existed in this codebase.

## 📋 Table of Contents

- [Overview](#overview)
- [Package Statistics](#package-statistics)
- [Entity Relationship Diagrams](#entity-relationship-diagrams)
- [Core Documentation](#core-documentation)
- [Object Categories](#object-categories)
- [Integration Architecture](#integration-architecture)
- [Getting Started](#getting-started)
- [Quick Navigation](#quick-navigation)

## 🎯 Overview

ARA (package label **Asymbl Copilot**) delivers AI-powered recruiting automation entirely through native Salesforce technology: Apex, Agentforce/Einstein GenAI metadata (GenAiFunction, GenAiPromptTemplate, GenAiPlugin), Lightning Web Components, Flow, and standard Salesforce email/notification infrastructure. There is no external microservices layer and no REST API surface beyond what Agentforce's own invocation model provides.

### Key Features

- **Recruiting Copilot ("Rosa")**: A full Agentforce agent built on 11 GenAiPlugins (Agentforce Topics) covering job summaries, candidate summaries, interview scheduling, offer drafting, and more
- **Job & Candidate Summaries**: AI-generated job summaries, hiring-manager summaries, and candidate profile summaries written directly to ATS base objects
- **Interview Management**: Scheduling (Google Meet/Zoom/in-person), rescheduling, reminder emails, and AI feedback summarization
- **Job Fit Assessment**: AI-generated candidate-job comparison and fit assessment, gated behind mandatory human confirmation before save
- **Voice Interview / Voice Agent Subsystem**: Outbound AI voice-agent calls for interview scheduling, rescheduling, and multi-candidate phone screening (net-new since the 2025-07 revision)
- **Confirmation-Gated Writes**: Every plugin capable of a persistent write requires explicit user confirmation before the corresponding Apex action executes

## 📊 Package Statistics

| Metric | Count | Source |
|--------|-------|--------|
| **Apex Classes** | 174 (~97 non-test) | `manifest/package.xml` `ApexClass` |
| **Apex Triggers** | 1 (`ARA_JobTrigger`) | `manifest/package.xml` `ApexTrigger` |
| **GenAI Functions** | 44 | `force-app/main/default/genAiFunctions` |
| **GenAI Plugins (Agentforce Topics)** | 11 | `force-app/main/default/genAiPlugins` |
| **GenAI Prompt Templates** | 16 (all `einstein_gpt__flex`) | `force-app/main/default/genAiPromptTemplates` |
| **Custom Objects** | 7 (6 CMDT types + 1 custom object) | `manifest/package.xml` `CustomObject` |
| **Custom Fields** | 85 | `manifest/package.xml` `CustomField` |
| **Custom Labels** | 404 (398 `ARA_*` + 6 `CP_*`) | `force-app/main/default/labels/CustomLabels.labels-meta.xml` |
| **Email Templates** | 19 (2 folders: `ARA`, `ARA_BaseEmailTemplates`) | `manifest/package.xml` `EmailTemplate` |
| **Lightning Web Components** | 17 bundles | `manifest/package.xml` `LightningComponentBundle` |
| **Flows** | 2 | `force-app/main/default/flows` |
| **Permission Sets** | 3 | `manifest/package.xml` `PermissionSet` |
| **Bot Template / Planner Bundle** | 1 / 1 (`Rosa_v1_Template`) | `manifest/package.xml` |
| **Total Package Components** | 818 | `manifest/package.xml` (all types) |

## 🗺️ Entity Relationship Diagrams

### Core Data Model

| ERD Diagram | Description | Key Objects |
|-------------|-------------|--------------|
| [**ARA Recruiting Objects**](ERD_ARA_Recruiting_Objects.md) | ARA's 7 owned objects and their relationships to 7 extended ATS base objects | `ARA_Voice_Agent_Call_Log__c`, `ARA_Voice_Interview_Setting__mdt`, `Contact`, `Job__c`, `ATS_Applicant__c` |

### GenAI / Agentforce Documentation

| Document | Description | Coverage |
|-------------|-------------|----------|
| [**GenAI Functions**](ARA_GenAI_Functions_Documentation.md) | All 44 GenAiFunctions with real `invocationTarget`/schema data | Full inventory + representative schema deep-dive |
| [**GenAI Plugins**](ARA_GenAI_Plugins_Documentation.md) | All 11 GenAiPlugins (Agentforce Topics) with real function groupings and instructions | Scope, confirmation gates, topic-boundary rules per plugin |
| [**GenAI Prompt Templates**](ARA_GenAI_Prompt_Templates_Documentation.md) | All 16 GenAiPromptTemplates | Consuming function, purpose, design pattern |
| [**Agentforce Components Research**](Agentforce_Components_Research.md) | Platform-level reference on the GenAiFunction/GenAiPromptTemplate/GenAiPlugin metadata model | General Salesforce Agentforce research, grounded in ARA's real plugin inventory |

### Technical Architecture Documents

| Document | Description | Coverage |
|-------------|-------------|----------|
| [**Apex Architecture**](ARA_Apex_Architecture.md) | Full Apex class inventory grouped by functional domain | 17 functional domains, Main+Helper+Test pattern |
| [**Email Templates**](ARA_Email_Templates_Documentation.md) | All 19 email templates across 2 folders | Transactional + general-purpose templates |
| [**Custom Labels**](ARA_Custom_Labels_Documentation.md) | 404 labels categorized by functional area | Voice interview, errors, UI, email |

### Audit and Completeness

| Document | Purpose |
|-------------|---------|
| [**ULTRATHINK Verification Report**](ARA_ULTRATHINK_Verification_Report.md) | Full component-count verification matrix against `master`, with explicitly disclosed documentation gaps |

## 📖 Core Documentation

**[ARA Package Overview](ARA_Package_Overview.md)** - The comprehensive guide containing:

- Executive summary and what ARA actually ships (818 components)
- Capability areas grouped by GenAiPlugin / Agentforce Topic
- Voice Interview / Voice Agent subsystem detail
- ARA's footprint on ATS base objects
- Architecture pattern (Apex action + GenAiFunction + GenAiPlugin layering)

*This is the primary reference document for understanding the complete ARA package structure and functionality.*

## 🏗️ Object Categories

### 1. Core Recruiting Capabilities (via GenAiPlugin)
- **Recruiting**: Job summaries, hiring-manager summaries, offer letter drafting
- **Candidate_Summary**: AI candidate profile summaries on `Contact`
- **Interview_Management**: Scheduling, rescheduling, feedback summarization
- **Recruitment_Marketing**: Job Description generation, candidate-job comparison
- **JobFitAssessment**: AI job-fit assessment and application notes

### 2. GenAI Functions & Prompt Templates
- 44 `GenAiFunction` records (`apex` or `generatePromptResponse` invocation targets)
- 16 `GenAiPromptTemplate` records, all type `einstein_gpt__flex`

### 3. Voice Interview / Voice Agent Subsystem (new since 2025-07 revision)
- **ARA_Voice_Agent_Call_Log__c**: One record per AI voice-agent call (scheduling, rescheduling, screening)
- **ARA_Voice_Agent_Parameter_Mapping__mdt / ARA_Voice_Interview_Setting__mdt**: Per-agent configuration and provider connection settings
- Plugins: Schedule_Voice_Interview, Reschedule_Voice_Interview, Screening_Process

### 4. Configuration Objects (Custom Metadata Types)
- **ARA_Summary_Related_Objects__mdt**: Drives related-object data for summaries
- **ARA_Generic_Org_Setting__mdt / ARA_Action_Component_Configuration__mdt / ARA_Screening_Question_Config__mdt**: Org-level and component-level configuration

### 5. Email Templates & Notifications
- 15 templates in the `ARA` folder (transactional/automation) + 4 in `ARA_BaseEmailTemplates` (general-purpose)
- 2 `CustomNotificationType` records (`ScreeningCallCompleted`, `VoiceInterviewEmailNotSendedNotify`)

### 6. Extended ATS Base Objects
- **Contact / Job__c / ATS_Applicant__c**: Carry ARA's AI-generated summary and assessment fields
- **Interview__c / Interview_Feedback__c / ATS_Question_Response__c**: Carry voice-call and feedback-cleanup fields

## 🔗 Integration Architecture

ARA follows a consistent **Apex action + GenAiFunction + GenAiPlugin** layering, with no external services beyond one voice-calling provider integration.

### Platform Integration
- **Agentforce/Atlas**: All plugin-to-function-to-Apex invocation happens through Salesforce's native Agentforce invocation model — no custom outbound REST layer
- **Voice Provider**: 2 inbound `@RestResource` webhook handlers (`ARA_VoiceInterviewWebhookHandler`, `ARA_CandidateScreeningWebhookHandler`) receive callbacks from an external voice-calling provider; outbound calls use 1 `RemoteSiteSetting` (`Voice_Agent_Remote_Setting`)
- **ASYMBL-ATS (bpats)**: Live, load-bearing dependency — functions and Apex classes directly reference `bpats__`-namespaced objects (`bpats__Job__c`, `bpats__Contact_List__c`)

### Security Architecture
Three permission sets govern ARA access: `Asymbl_Recruiting_Agent` (general Copilot access), `Screening_Agent_Permission` (multi-candidate voice screening), `Voice_Agent_Permissions` (voice interview scheduling/rescheduling), plus 1 `CustomPermission` (`ARA_Job_Summary_Permission`).

### Design Pattern
1. A **GenAiPlugin** groups related functions with natural-language instructions steering the Agentforce reasoning engine
2. Each **GenAiFunction** invokes either an Apex class (`apex` type) or a `GenAiPromptTemplate` (`generatePromptResponse` type)
3. Apex action classes follow a **Main class + Helper class + Test class** triad
4. Batch Apex handles bulk/background summary regeneration and voice-call lifecycle processing

## 🚀 Getting Started

### For Developers
1. Start with [ARA Package Overview](ARA_Package_Overview.md) for complete technical overview
2. Review [Apex Architecture](ARA_Apex_Architecture.md) for class layering and the Main+Helper+Test pattern
3. Check [GenAI Functions Documentation](ARA_GenAI_Functions_Documentation.md) for real schema patterns and invocation targets

### For Business Users / Recruiters
1. Begin with [GenAI Plugins Documentation](ARA_GenAI_Plugins_Documentation.md) to understand what each Agentforce Topic does
2. Review [ARA Package Overview](ARA_Package_Overview.md)'s capability areas section for a plain-language feature summary
3. Explore the Voice Interview / Voice Agent subsystem section for AI phone-call capabilities

### For System Administrators
1. Study [Custom Labels Documentation](ARA_Custom_Labels_Documentation.md) and [Email Templates Documentation](ARA_Email_Templates_Documentation.md) for UI/communication setup
2. Review [GenAI Prompt Templates Documentation](ARA_GenAI_Prompt_Templates_Documentation.md) for prompt configuration
3. Check [ULTRATHINK Verification Report](ARA_ULTRATHINK_Verification_Report.md) for current documentation-verification status

## 🧭 Quick Navigation

### By Functional Area
- **Recruiting & Job Summaries**: [GenAI Plugins](ARA_GenAI_Plugins_Documentation.md#1-recruiting) | [Apex Architecture](ARA_Apex_Architecture.md)
- **Interview Scheduling**: [GenAI Plugins](ARA_GenAI_Plugins_Documentation.md#3-interview_management) | [GenAI Functions](ARA_GenAI_Functions_Documentation.md)
- **Voice Interview/Screening**: [Package Overview](ARA_Package_Overview.md) | [ERD](ERD_ARA_Recruiting_Objects.md)
- **Job Fit Assessment**: [GenAI Plugins](ARA_GenAI_Plugins_Documentation.md#7-jobfitassessment)

### By Object Type
- **Custom Objects**: [ERD ARA Recruiting Objects](ERD_ARA_Recruiting_Objects.md)
- **GenAI Metadata**: [GenAI Functions](ARA_GenAI_Functions_Documentation.md) | [GenAI Plugins](ARA_GenAI_Plugins_Documentation.md) | [GenAI Prompt Templates](ARA_GenAI_Prompt_Templates_Documentation.md)
- **Communication**: [Email Templates](ARA_Email_Templates_Documentation.md) | [Custom Labels](ARA_Custom_Labels_Documentation.md)

### Implementation Resources
- **Complete Component List**: [ARA Package Overview](ARA_Package_Overview.md)
- **Documentation Verification Status**: [ULTRATHINK Verification Report](ARA_ULTRATHINK_Verification_Report.md)
- **Entity Relationships**: [ERD ARA Recruiting Objects](ERD_ARA_Recruiting_Objects.md)

---

## 📝 Documentation Notes

- All ERD diagrams use Mermaid syntax for consistent visualization, following the color-coding scheme in `ProductDocumentations/Claude.md`
- Field types and relationships are verified directly against `manifest/package.xml` and `force-app/main/default` on the `master` branch
- Cross-references between documents provide complete navigation paths
- This revision (2026-10-30) fully replaced fabricated content across all 11 files in this folder — the prior documentation described a different product entirely; see the "Correction Notice" at the top of each file and the [ULTRATHINK Verification Report](ARA_ULTRATHINK_Verification_Report.md) for the full verification matrix

For questions or contributions to this documentation, please refer to the main repository structure and contribution guidelines.
