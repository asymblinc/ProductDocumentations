# AIN Package Documentation

Welcome to the comprehensive documentation for the **AIN (Asymbl Integration) Package** - a Salesforce managed package that extends ASYMBL-ATS with multi-channel candidate communication and external job board/VMS integrations.

> **Source of truth**: This documentation reflects the `asymbl-integration` repository `master` branch as of 2026-10-22 (commit `3d45738`, package.xml API v66.0, 16 CustomObjects). It supersedes the 2025-07-10 revision, which was found to be materially stale and, in multiple sections, fabricated rather than derived from source — invented classes (`AIN_FieldMappingService`, `AIN_RetryHandler`, `AIN_MetadataCache`), invented objects (`Activity_Log__c`, `Permission_Usage__c`), and three static permission sets that do not exist in the repository.

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

The AIN package connects Salesforce to **Indeed** (job publishing, Indeed Apply, screening/demographic questions, disposition sync-back), **Beeline** (VMS job/candidate sync), and **RedDot** (job board posting + webhook source tracking), with a durable webhook request/retry subsystem underpinning all three. Every field mapping is metadata-driven — no hardcoded Salesforce or external field names in Apex.

### Key Features

- **Job Publishing**: Indeed (GraphQL), Beeline (REST/OAuth), RedDot (REST) — all driven by `AIN_Job_Mapping__mdt` / `AIN_Vms_Job_Mapping__mdt` records
- **Inbound Application Processing**: Indeed Apply webhook → `Job_Board_Application__c` + `Contact`/`ATS_Applicant__c`, with resume JSON flattening and screening/demographic question capture
- **Candidate Submission to VMS**: Beeline candidate posting with attachment sync
- **Disposition Sync-back to Indeed**: Pushes ATS Applicant/Stage status changes to Indeed's `partnerDispositionSync` GraphQL API, fully metadata-configured
- **Durable Webhook Framework**: Inbound webhooks (Beeline, Indeed, RedDot) logged, retried, and auto-cleaned on a schedule
- **Screening/Demographic Question Builder**: Admin-configurable question library with per-job assignment, hierarchical/conditional question support
- **Runtime Permission Provisioning**: Four permission sets created and kept in sync programmatically — no static permission set metadata ships in this package

## 📊 Package Statistics

| Metric | Count | Source |
|--------|-------|--------|
| **Apex Classes** | 106 (43 test classes) | `manifest/package.xml` `ApexClass` |
| **Apex Triggers** | 3 | `manifest/package.xml` `ApexTrigger` |
| **Custom Fields** | 175 | `manifest/package.xml` `CustomField` |
| **Custom Objects** | 16 (6 true objects/platform event + 10 `__mdt` types) | `manifest/package.xml` `CustomObject` |
| **Custom Labels** | 296 | `manifest/package.xml` `CustomLabel` |
| **Custom Metadata Records** | 240 across 10 types | `force-app/main/default/customMetadata` |
| **Lightning Web Components** | 31 bundles | `manifest/package.xml` `LightningComponentBundle` |
| **Custom Tabs** | 3 | `manifest/package.xml` `CustomTab` |
| **Flows** | 2 | `force-app/main/default/flows` |
| **Quick Actions** | 3 | `manifest/package.xml` `QuickAction` |
| **Apex Pages (Visualforce)** | 2 | `manifest/package.xml` `ApexPage` |
| **Permission Sets (static)** | 0 | Created programmatically at runtime — see Integration Architecture |
| **Integration Points** | 3 (Indeed, Beeline, RedDot) | — |

## 🗺️ Entity Relationship Diagrams

### Core Integration ERD

| ERD Diagram | Description | Key Objects |
|-------------|-------------|--------------|
| [**AIN Integration Objects**](ERD_AIN_Integration_Objects.md) | Full object model: Candidate Question/Response/Assignment, Webhook Request/Event, External Custom Fields, and all 10 CMDT types | `AIN_Candidate_Question__c`, `AIN_Candidate_Response__c`, `AIN_Question_Assignment__c`, `AIN_Webhook_Request__c`, `AIN_Webhook_Event__e`, `AIN_External_Custom_Fields__c` |

### Technical Architecture Documents

| Document | Description | Coverage |
|-------------|-------------|----------|
| [**Integration Architecture**](AIN_Integration_Architecture.md) | Indeed/Beeline/RedDot system architecture, authentication, field mapping engine | 10 CMDT mapping types, auth mechanisms per system |
| [**Integration Workflows**](AIN_Integration_Workflows_Documentation.md) | End-to-end sequence diagrams for every workflow | Job publishing, webhook processing, disposition sync-back |
| [**Apex Architecture**](AIN_Apex_Architecture.md) | Apex class inventory and layering | 106 classes across 10 functional categories |
| [**Permission Sets**](AIN_Permission_Sets_Documentation.md) | Runtime permission set provisioning model | 4 programmatically-created permission sets |

### Component Inventories

| Document | Description | Coverage |
|-------------|-------------|----------|
| [**Custom Objects**](AIN_Custom_Objects_Documentation.md) | 6 true objects/platform event + 10 CMDT type definitions | Full field-level detail |
| [**Custom Fields**](AIN_Custom_Fields_Documentation.md) | Field inventory by object, 175 fields | `Job__c`, `ATS_Applicant__c`, custom objects, CMDT fields |
| [**Custom Labels**](AIN_Custom_Labels_Documentation.md) | 296 labels categorized by naming pattern | UI, messages, errors, help text |
| [**Custom Metadata**](AIN_Custom_Metadata_Documentation.md) | 240 records across 10 CMDT types | Field mappings, credentials, disposition config |
| [**Lightning Components**](AIN_Lightning_Components_Documentation.md) | 31 LWC bundles | Admin UI, question builder, webhook config, RedDot |

### Audit and Completeness

| Document | Purpose |
|-------------|---------|
| [**ULTRATHINK Verification Report**](AIN_ULTRATHINK_Verification_Report.md) | Full component-count verification matrix against `master`, with confidence levels per area |

## 📖 Core Documentation

**[AIN Package Overview](AIN_Package_Overview.md)** - The comprehensive guide containing:

- Executive summary and key business capabilities
- High-level architecture diagram
- Full component breakdown table
- Integration points with ASYMBL-ATS (bpats)
- Post-install behavior (`AIN_PostInstallScript`)

*This is the primary reference document for understanding the complete AIN package structure and functionality.*

## 🏗️ Object Categories

### 1. Screening/Demographic Question Builder
- **AIN_Candidate_Question__c**: Question library, supports hierarchical/conditional questions via self-lookup
- **AIN_Candidate_Response__c**: Candidate answers with point-in-time question snapshot
- **AIN_Question_Assignment__c**: Job-to-question junction object

### 2. Durable Webhook Framework
- **AIN_Webhook_Request__c**: Durable log of every inbound webhook request (Beeline, Indeed Apply, RedDot)
- **AIN_Webhook_Event__e**: Platform event decoupling webhook receipt from async processing

### 3. VMS/Job Board Support Objects
- **AIN_External_Custom_Fields__c**: VMS-supplied (Beeline) candidate custom field definitions per job posting

### 4. Field Mapping Configuration (10 CMDT types)
- **AIN_Job_Mapping__mdt / AIN_Vms_Job_Mapping__mdt**: Job field mapping (Indeed / Beeline)
- **AIN_Job_Board_Mapping__mdt / AIN_Vms_Job_Board_Mapping__mdt**: Application/candidate submission field mapping
- **AIN_Indeed_Apply_Mapping__mdt**: Indeed Apply form configuration
- **AIN_Integration_Credentials__mdt**: Beeline/Indeed/Indeed_Apply/RedDot credentials

### 5. Disposition Sync Configuration (new since 2025-07 revision)
- **AIN_Disposition_Sync_Config__mdt / AIN_Disposition_Value_Map__mdt**: Metadata-driven Indeed disposition sync-back

### 6. Extended ATS Base Objects
- **Job__c**: 17 AIN fields (publishing/sync control, posting history)
- **ATS_Applicant__c**: 7 AIN fields (VMS/disposition sync tracking)
- **Job_Board_Application__c / Contact / ContentVersion**: sync identifier fields

## 🔗 Integration Architecture

AIN integrates Salesforce with three external systems through outbound REST/GraphQL callouts and inbound REST webhook endpoints, all routed through a shared, durable webhook-request logging layer.

### External Service Integrations
- **Indeed**: OAuth 2.0 (client-credentials for job publishing/GraphQL; authorization-code flow via `ExternalClientApplication`/`NamedCredential` for employer login), disposition sync-back via `partnerDispositionSync`
- **Beeline**: OAuth 2.0 client-credentials grant, REST job/candidate sync, webhook-driven updates
- **RedDot**: REST job posting, webhook source tracking, trusted via `Reddot_JobSplice` CSP Trusted Site

### Runtime Permission Provisioning
No static `PermissionSet` metadata ships in this package. `AIN_PostInstallScript` / `AIN_PermissionSetController` create and maintain four permission sets at install/runtime (`AIN_Admin_Clone`, `AIN_User_Clone`, `AIN_Guest_Clone`, `AIN_Api_User_Clone`), discovering fields dynamically via `Schema.describe()` rather than static XML. See [Permission Sets Documentation](AIN_Permission_Sets_Documentation.md) for the full model.

### Custom Metadata Configuration
- Dynamic field mappings (Indeed job/application, Beeline job/candidate)
- Disposition sync configuration (field-level mapping + value translation)
- Webhook retry/cleanup scheduling (`AIN_Webhook_Setup__mdt`)
- Screening question seed templates (`AIN_Default_Question_Template__mdt`)

## 🚀 Getting Started

### For Developers
1. Start with [AIN Package Overview](AIN_Package_Overview.md) for complete technical overview
2. Review [Apex Architecture](AIN_Apex_Architecture.md) for class layering and trigger architecture
3. Check [Integration Workflows](AIN_Integration_Workflows_Documentation.md) for end-to-end sequence diagrams

### For Integration/Business Administrators
1. Begin with [Integration Architecture](AIN_Integration_Architecture.md) for system-by-system setup understanding
2. Review [Custom Metadata Documentation](AIN_Custom_Metadata_Documentation.md) for field-mapping configuration
3. Explore [Permission Sets Documentation](AIN_Permission_Sets_Documentation.md) to understand access provisioning

### For System Administrators
1. Study [Custom Labels Documentation](AIN_Custom_Labels_Documentation.md) and [Lightning Components Documentation](AIN_Lightning_Components_Documentation.md) for UI setup
2. Review [Custom Objects Documentation](AIN_Custom_Objects_Documentation.md) for object-level configuration
3. Check [ULTRATHINK Verification Report](AIN_ULTRATHINK_Verification_Report.md) for current documentation-verification status

## 🧭 Quick Navigation

### By Functional Area
- **Job Publishing**: [Integration Architecture](AIN_Integration_Architecture.md) | [Workflows](AIN_Integration_Workflows_Documentation.md)
- **Webhook Framework**: [ERD](ERD_AIN_Integration_Objects.md) | [Apex Architecture](AIN_Apex_Architecture.md)
- **Disposition Sync-back**: [Integration Architecture](AIN_Integration_Architecture.md) | [Custom Metadata](AIN_Custom_Metadata_Documentation.md)
- **Screening/Question Builder**: [Custom Objects](AIN_Custom_Objects_Documentation.md) | [Lightning Components](AIN_Lightning_Components_Documentation.md)

### By Object Type
- **Custom Objects**: [Custom Objects Documentation](AIN_Custom_Objects_Documentation.md)
- **Custom Fields**: [Custom Fields Documentation](AIN_Custom_Fields_Documentation.md)
- **Custom Metadata**: [Custom Metadata Documentation](AIN_Custom_Metadata_Documentation.md)
- **Permission Model**: [Permission Sets Documentation](AIN_Permission_Sets_Documentation.md)

### Implementation Resources
- **Complete Object List**: [AIN Package Overview](AIN_Package_Overview.md)
- **Documentation Verification Status**: [ULTRATHINK Verification Report](AIN_ULTRATHINK_Verification_Report.md)
- **Entity Relationships**: [ERD AIN Integration Objects](ERD_AIN_Integration_Objects.md)

---

## 📝 Documentation Notes

- All ERD diagrams use Mermaid syntax for consistent visualization, following the color-coding scheme in `ProductDocumentations/Claude.md`
- Field types and relationships are verified directly against `manifest/package.xml` and `force-app/main/default` on the `master` branch
- Cross-references between documents provide complete navigation paths
- This revision (2026-10-30) replaced fabricated content in several documents — most consequentially the Permission Sets document, which previously described three static permission sets that do not exist — see each file's "Corrections vs. prior documentation" section

For questions or contributions to this documentation, please refer to the main repository structure and contribution guidelines.
