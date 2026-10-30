# ATS Package Documentation

Welcome to the comprehensive documentation for the **ATS (Applicant Tracking System) Package** - a complete Salesforce-based solution for managing recruitment and hiring processes.

> **Source of truth**: This documentation reflects the `bpats` repository `master` branch as of 2026-10-30 (package.xml API v62.0+, 72 CustomObjects). It supersedes the 2025-07-10 revision, which had drifted significantly from the shipping package (fabricated objects/fields in several ERDs, and missing several domains added since mid-2025 — Screening Questions, Interview Topic Kits, Candidate/Job Matching Intelligence, and Target/Goal Management).

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

The ATS Package is a comprehensive Salesforce application designed to streamline the entire recruitment lifecycle. It provides tools for job management, candidate tracking, screening questions, interview scheduling (including reusable topic kits), placement and credit management, recruiter/team target tracking, candidate-job matching intelligence, and extensive integration capabilities with external services.

### Key Features

- **Job Management**: Complete job lifecycle from posting to closure, including structured job-level fields (level, salary range, onsite/remote, travel, replacement tracking)
- **Candidate Tracking**: Advanced applicant management and tracking, direct and job-board sourced
- **Screening Questions**: Configurable pre-application screening questions per job/template with response capture
- **Interview Management**: Structured interview processes, reusable Interview Topic Kits, interviewer availability slots, and feedback collection
- **Placement Management**: Credit allocation and placement tracking
- **Target/Goal Management**: Recruiter and team KPI target templates and tracking (new domain)
- **Candidate/Job Matching Intelligence**: Caching and logging infrastructure for AI-assisted candidate search and job-candidate qualification scoring
- **Integration Support**: TextKernel (resume parsing), Twilio (email/SMS), Zoom/Google Meet, Google services (Calendar, reCAPTCHA)
- **Timeline Events**: Comprehensive audit trail and activity tracking
- **Configurable Templates**: Flexible workflow and process templates, with action toggles per template

## 📊 Package Statistics

| Metric | Count | Source |
|--------|-------|--------|
| **Custom Objects** | 72 | `manifest/package.xml` `CustomObject` |
| **Custom Fields** | 699 | `manifest/package.xml` `CustomField` |
| **Apex Classes** | 423 (426 incl. supporting members) | `force-app/main/default/classes` |
| **Apex Triggers** | 17 | `force-app/main/default/triggers` |
| **Lightning Web Components** | 204 bundles | `manifest/package.xml` `LightningComponentBundle` |
| **Aura Components** | 9 bundles | `manifest/package.xml` `AuraDefinitionBundle` |
| **Custom Metadata Types** | 22 | `force-app/main/default/objects/*__mdt` |
| **Custom Labels** | 3,024 | `manifest/package.xml` `CustomLabel` |
| **Platform Events** | 2 (`ATS_Record_Action__e`, `Add_to_ATS__e`) | `manifest/package.xml` |
| **Permission Sets** | 3 | `force-app/main/default/permissionsets` |
| **Validation Rules** | 14 | `manifest/package.xml` `ValidationRule` |
| **Quick Actions** | 33 | `manifest/package.xml` `QuickAction` |
| **Custom Tabs** | 31 | `manifest/package.xml` `CustomTab` |
| **Flows** | 3 | `force-app/main/default/flows` |
| **Reports** | 18 | `manifest/package.xml` `Report` |
| **Report Types** | 5 | `force-app/main/default/reportTypes` |
| **Global Value Sets** | 8 | `force-app/main/default/globalValueSets` |
| **Custom Permissions** | 2 | `force-app/main/default/customPermissions` |
| **Integration Points** | 5+ (TextKernel, Twilio, Zoom, Google Meet, Google reCAPTCHA/Calendar) | — |

## 🗺️ Entity Relationship Diagrams

Our documentation includes comprehensive ERD diagrams organized by functional area:

### Core Business Process ERDs

| ERD Diagram | Description | Key Objects |
|-------------|-------------|-------------|
| [**Job Management**](ERD_Job_Management.md) | Job creation, templates, stage management, screening questions | Job__c, ATS_Template__c, ATS_Stage__c, ATS_Job_Screening_Question__c |
| [**Applicant Management**](ERD_Applicant_Management.md) | Candidate application and tracking workflows | ATS_Applicant__c, Job_Board_Application__c, ATS_Question_Response__c |
| [**Interview Management**](ERD_Interview_Management.md) | Interview scheduling, feedback, templates, topic kits, availability | Interview__c, Interview_Feedback__c, Interview_Topic__c, Interview_Topic_Kit__c, Interview_Availability__c |
| [**Placement Credit Management**](ERD_Placement_Credit_Management.md) | Placement tracking, credit allocation, and recruiter/team targets | Placement__c, Placement_Credit__c, Target__c, Target_Template__c |

### Technical Architecture ERDs

| ERD Diagram | Description | Coverage |
|-------------|-------------|----------|
| [**Integration Objects**](ERD_Integration_Objects.md) | External service integrations and APIs | TextKernel, Twilio, Zoom/Google Meet, Google reCAPTCHA, Message_Stats__c |
| [**Configuration Metadata**](ERD_Configuration_Metadata.md) | Custom metadata and system configuration | All 22 `__mdt` types: field mappings, templates, settings, matching intelligence |
| [**Standard Object Extensions**](ERD_Standard_Object_Extensions.md) | Custom fields on standard Salesforce objects | Contact, ContentVersion, EmailMessage |
| [**Action & Template Configuration**](ERD_Action_Template_Configuration.md) | Workflow and automation templates | ATS_Action__c, ATS_Action_Link__c, ATS_Template_Stage__c, ATS_Template_Action_Toggles__c |

### Audit and Completeness

| ERD Diagram | Purpose |
|-------------|---------|
| [**Missing Objects Complete**](ERD_Missing_Objects_Complete.md) | Deep-dive coverage of objects not detailed in the primary domain ERDs (supporting/log/matching objects) |
| [**Audit Missing Objects**](ERD_Audit_Missing_Objects.md) | Gap-analysis audit log for this documentation set — used to track what has been verified against `master` vs. still needs confirmation |
| [**ERD Index**](ERD_Index.md) | Master index of all ERD diagrams with cross-references |

## 📖 Core Documentation

### Main Package Documentation

**[ATS Package Documentation](ATS_Package_Documentation.md)** - The comprehensive guide containing:

- Complete object inventory and relationships
- Field-level documentation for all custom objects
- Business process workflows
- Integration architecture details
- Security and permission model
- Implementation guidelines
- Best practices and recommendations

*This is the primary reference document for understanding the complete ATS package structure and functionality.*

## 🏗️ Object Categories

### 1. Core Business Objects
- **Job__c**: Central job management object
- **ATS_Applicant__c**: Candidate application tracking
- **Interview__c**: Interview scheduling and management
- **Placement__c**: Placement and hiring outcomes

### 2. Configuration Objects
- **ATS_Template__c**: Process templates and workflows
- **ATS_Stage__c**: Hiring stage definitions
- **ATS_Action_Template__c (ATS_Action__c / ATS_Action_Link__c)**: Automated action configurations
- **ATS_Template_Action_Toggles__c**: Per-template action enablement

### 3. Screening & Matching Intelligence (new since 2025-07 revision)
- **ATS_Question__c / ATS_Question_Category__c / ATS_Question_Response__c / ATS_Job_Screening_Question__c**: Screening question framework
- **ATS_Match_Cache__c / ATS_Candidate_Search_Log__c / ATS_Search_Location__c**: Candidate/job matching intelligence
- **Qualification_Run__c** + `ATS_Qualification_Config__mdt`: Automated job-candidate qualification scoring

### 4. Target / Goal Management (new domain)
- **Target__c / Target_Template__c / Target_Template_Entry__c**: Recruiter and team KPI targets

### 5. Interview Topic Kits (new since 2025-07 revision)
- **Interview_Topic_Kit__c / Interview_Topic_Kit_Item__c**: Reusable interview topic libraries
- **Interview_Availability__c**: Interviewer availability slots

### 6. Integration Objects
- **TextKernel**: Resume parsing integration
- **Twilio**: SMS/Email communication
- **Zoom / Google Meet**: Video conferencing (`Online_Platforms__c` picklist on Interview__c)
- **Google Services**: Calendar and reCAPTCHA

### 7. Supporting Objects
- **Timeline Events**: Activity tracking (`ATS_Timeline_Configuration__c`, `ATS_Timeline_Child__c`)
- **Message Stats**: Communication analytics
- **Credit Management**: Placement fee allocation

## 🔗 Integration Architecture

The ATS package includes robust integration capabilities:

### External Service Integrations
- **TextKernel**: AI-powered resume parsing (`TK_Field_Mapping__mdt`, `ATS_Resume_Field_Mapping__mdt`, `ATS_Work_Experience_Mapping__mdt`, `ATS_Skill_Summary_Mapping__mdt`)
- **Twilio**: SMS and communication services (`ATS_SMS_Integration__mdt`, `ATS_Email_Integration__mdt`)
- **Zoom / Google Meet**: Video interview management (`ATS_Zoom_Integration_Data__mdt`, `Interview__c.Online_Platforms__c`)
- **Google Calendar**: Interview scheduling sync
- **Google reCAPTCHA**: Security and bot protection (`Google_Recaptcha_Detail__c`)
- **Talent Intelligence Service**: `ATS_Talent_Intelligence_Data__mdt`, `ATS_Job_Intelligence_Setting__mdt` — powers job/candidate matching intelligence surfaced via `ATS_Match_Cache__c` and `Qualification_Run__c`

### Platform Events
- **ATS_Record_Action__e**: Real-time activity tracking
- **Add_to_ATS__e**: Cross-object workflow triggers
- **ATS_Platform_Event_Restriction__mdt**: Per-object kill switch controlling whether platform events fire for Account/Contact/Job/Offer/Placement/ATS_Applicant

### Custom Metadata Configuration
- Dynamic field mappings (resume parsing, job board, timesheet, screening)
- Integration settings (TextKernel, Twilio, Zoom, generic)
- Process templates (job description, job view lists, demo data)
- Feature gating (`Feature_Gate__mdt` — Launch/Enterprise/Ultimate tiers)
- UI configuration (applicant field type config, job view lists)

## 🚀 Getting Started

### For Developers
1. Start with [ATS Package Documentation](ATS_Package_Documentation.md) for complete technical overview
2. Review [ERD Index](ERD_Index.md) for object relationship understanding
3. Check [Integration Objects ERD](ERD_Integration_Objects.md) for external service setup

### For Business Users
1. Begin with [Job Management ERD](ERD_Job_Management.md) for core process understanding
2. Review [Applicant Management ERD](ERD_Applicant_Management.md) for candidate workflows
3. Explore [Interview Management ERD](ERD_Interview_Management.md) for interview processes, including Topic Kits

### For System Administrators
1. Study [Configuration Metadata ERD](ERD_Configuration_Metadata.md) for system setup
2. Review [Standard Object Extensions ERD](ERD_Standard_Object_Extensions.md) for field configurations
3. Check [Audit Missing Objects](ERD_Audit_Missing_Objects.md) for current documentation-verification status

## 🧭 Quick Navigation

### By Functional Area
- **Job Management**: [ERD](ERD_Job_Management.md) | [Main Docs](ATS_Package_Documentation.md#job-management)
- **Candidate Tracking**: [ERD](ERD_Applicant_Management.md) | [Main Docs](ATS_Package_Documentation.md#applicant-management)
- **Interview Process**: [ERD](ERD_Interview_Management.md) | [Main Docs](ATS_Package_Documentation.md#interview-management)
- **Placement & Credits & Targets**: [ERD](ERD_Placement_Credit_Management.md) | [Main Docs](ATS_Package_Documentation.md#placement-and-credit-management)

### By Object Type
- **Custom Objects**: See [ERD Index](ERD_Index.md)
- **Standard Extensions**: [ERD](ERD_Standard_Object_Extensions.md)
- **Integrations**: [ERD](ERD_Integration_Objects.md)
- **Configuration**: [ERD](ERD_Configuration_Metadata.md)

### Implementation Resources
- **Complete Object List**: [Main Documentation](ATS_Package_Documentation.md)
- **Documentation Verification Status**: [Audit Report](ERD_Audit_Missing_Objects.md)
- **Supporting/Log/Matching Objects Deep Dive**: [Missing Objects Complete](ERD_Missing_Objects_Complete.md)

---

## 📝 Documentation Notes

- All ERD diagrams use Mermaid syntax for consistent visualization
- Field types and relationships are clearly documented in each ERD, verified directly against `force-app/main/default/objects/*/fields/*.field-meta.xml` on the `master` branch
- Cross-references between documents provide complete navigation paths
- This revision (2026-10-30) corrected multiple ERD files that had drifted from or never matched the actual package schema — see each file's changelog note at the top

For questions or contributions to this documentation, please refer to the main repository structure and contribution guidelines.
