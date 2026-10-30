# ATS Package ERD Documentation Index

This repository contains comprehensive Entity Relationship Diagrams (ERDs) for the Asymbl ATS (Applicant Tracking System) Salesforce package. Each diagram provides detailed visualizations of objects, fields, and relationships.

> **Revision note (2026-10-30)**: This index and the files it references were rebuilt against the `bpats` `master` branch (72 CustomObjects / 699 CustomFields per `manifest/package.xml`). The prior 2025-07-10 revision contained several ERDs with objects/fields that do not exist in the package (most notably `ERD_Integration_Objects.md` and `ERD_Standard_Object_Extensions.md`, which referenced invented objects like `TextKernel_Config__c`, `Zoom_Meeting__c`, `Application__c`, `Candidate__c`, `Position__c`). Those have been rewritten from the real metadata.

## 📊 ERD Diagrams Available

### Core Functional Areas

1. **[Job Management ERD](./ERD_Job_Management.md)**
   - Job__c (60+ real fields: Salary_Min__c/Salary_Max__c, Job_Level__c, Onsite_or_Remote__c, New_or_Replacement__c, etc.), ATS_Template__c, ATS_Stage__c, ATS_Job_Screening_Question__c
   - Master-detail and lookup relationships
   - Field documentation verified against `force-app/main/default/objects/Job__c/fields`

2. **[Applicant Management ERD](./ERD_Applicant_Management.md)**
   - ATS_Applicant__c, Job_Board_Application__c, ATS_Question_Response__c
   - Application flow and contact relationships
   - Stage tracking mechanisms (`ATS_Stage_Tracking__c`)

3. **[Interview Management ERD](./ERD_Interview_Management.md)**
   - Interview__c, Interview_Template__c, Interview_Template_Detail__c, Interview_Feedback__c, Interview_Topic__c, Interview_Topic_Feedback__c
   - **New since prior revision**: Interview_Topic_Kit__c, Interview_Topic_Kit_Item__c (reusable topic libraries), Interview_Availability__c (interviewer availability slots)
   - Template-based interview structures with Kit-sourced or Custom topics (`Topic_Source__c`)
   - Feedback collection system

4. **[Placement & Credit Management ERD](./ERD_Placement_Credit_Management.md)**
   - Placement__c, Placement_Credit__c, Placement_Credit_Template__c, Placement_Credit_Template_Entry__c
   - Credit allocation flow diagrams
   - **New domain**: Target__c, Target_Template__c, Target_Template_Entry__c (recruiter/team KPI goal tracking) — not present in prior revision

### Configuration & Support

5. **[Action & Template Configuration ERD](./ERD_Action_Template_Configuration.md)**
   - ATS_Action__c, ATS_Action_Link__c, ATS_Template_Stage__c, ATS_Template_Filter__c
   - **New**: ATS_Template_Action_Toggles__c (per-template action enablement)
   - Action automation framework
   - Stage-specific configurations

6. **[Configuration Metadata ERD](./ERD_Configuration_Metadata.md)**
   - All 22 custom metadata types (`__mdt`) actually present in `master`, including the matching-intelligence (`ATS_Qualification_Config__mdt`, `ATS_Talent_Intelligence_Data__mdt`, `ATS_Job_Intelligence_Setting__mdt`), resume-mapping (`ATS_Resume_Field_Mapping__mdt`, `ATS_Skill_Summary_Mapping__mdt`, `ATS_Work_Experience_Mapping__mdt`), and feature-gating (`Feature_Gate__mdt`) types added since the prior revision
   - System settings and mappings
   - Integration configurations

### Technical Components

7. **[Integration Objects ERD](./ERD_Integration_Objects.md)**
   - TextKernel, Twilio, Zoom/Google Meet, Google reCAPTCHA integrations — documented using the **real** metadata types that ship in the package (`TK_Field_Mapping__mdt`, `ATS_SMS_Integration__mdt`, `ATS_Email_Integration__mdt`, `ATS_Zoom_Integration_Data__mdt`, `Google_Recaptcha_Detail__c`)
   - Message_Stats__c communication analytics
   - Platform event architecture (`ATS_Record_Action__e`, `Add_to_ATS__e`, `ATS_Platform_Event_Restriction__mdt`)

8. **[Standard Object Extensions ERD](./ERD_Standard_Object_Extensions.md)**
   - Contact extensions (13 custom fields, incl. `Asymbl_Parsed_Resume__c`, `Matching_Jobs_Last_Refreshed__c`, `Timezone__c` — added since prior revision)
   - ContentVersion extensions (`Is_Primary__c`, `Primary_Set_By_User__c`)
   - EmailMessage extensions (8 custom fields, incl. `Is_Incoming__c`)

### Audit and Completeness

9. **[Missing Objects Complete ERD](./ERD_Missing_Objects_Complete.md)**
   - Deep field-level coverage of objects not fully detailed elsewhere: Interview_Template_Detail__c, Interview_Topic_Feedback__c, Message_Stats__c, Google_Recaptcha_Detail__c, ATS_Log__c, ATS_Match_Cache__c, ATS_Candidate_Search_Log__c, ATS_Search_Location__c, Qualification_Run__c, and remaining `__mdt` types

10. **[ERD Audit Report](./ERD_Audit_Missing_Objects.md)**
    - Verification log comparing this documentation set against the actual `master` package.xml/metadata as of 2026-10-30

## 🎨 Diagram Features

All ERD diagrams include:
- ✅ Complete field listings with data types, verified against field-meta.xml source
- ✅ Relationship cardinality (1:1, 1:N, M:N)
- ✅ Color coding for object categories
- ✅ Primary and foreign key indicators
- ✅ Business logic documentation
- ✅ Mermaid syntax for GitHub rendering

## 📈 Coverage Status

As of the 2026-10-30 revision:
- **Total Custom Objects Documented**: 72 (matches `manifest/package.xml` `CustomObject` count exactly)
- **Standard Object Extensions**: 3 objects (Contact, ContentVersion, EmailMessage)
- **Custom Metadata Types**: 22 (matches actual object directory count)
- **Platform Events**: 2 (ATS_Record_Action__e, Add_to_ATS__e)
- **Coverage**: 100% of CustomObject members in package.xml; field-level detail verified for all objects referenced across the domain ERDs

## 🔍 Quick Navigation

### By Object Type
- **Custom Objects**: Job__c, ATS_Applicant__c, Interview__c, Placement__c, Target__c
- **Configuration Objects**: ATS_Template__c, ATS_Action__c, Interview_Template__c, ATS_Template_Action_Toggles__c
- **Junction Objects**: ATS_Action_Link__c, Placement_Credit__c, ATS_Job_Screening_Question__c
- **Matching/Intelligence Objects**: ATS_Match_Cache__c, Qualification_Run__c, ATS_Candidate_Search_Log__c
- **Metadata Types**: Generic_Setting__mdt, TK_Field_Mapping__mdt, ATS_Qualification_Config__mdt
- **Platform Events**: ATS_Record_Action__e, Add_to_ATS__e
- **Standard Extensions**: Contact, ContentVersion, EmailMessage

### By Functionality
- **Recruitment Pipeline**: Job → Screening Question → Applicant / Job Board Application → Interview → Placement
- **Template System**: Templates → Stages → Actions (with per-template Action Toggles)
- **Interview Topic System**: Interview Topic Kit → Kit Item → Interview Topic (Custom or Kit-sourced) → Topic Feedback
- **Credit Management**: Placement → Credit Templates → Credit Allocation
- **Target Management**: Target Template → Target Template Entry → Target (recruiter/team KPIs)
- **Matching Intelligence**: Job/Candidate → Qualification Run / Match Cache → Candidate Search Log
- **Integration Layer**: External APIs → Platform Events → Core Objects
- **Document Management**: ContentVersion → ContentDocumentLink → ATS Objects

## 🛠️ Usage

These ERD diagrams are designed to help:
- Developers understand the data model
- Architects plan integrations
- Administrators configure the system
- Business analysts understand relationships
- QA teams plan test scenarios

## 📖 Reading the Diagrams

- **Blue objects**: Core business objects
- **Green objects**: Configuration/template objects
- **Purple objects**: Junction/linking objects
- **Orange objects**: Metadata/settings objects
- **Gray objects**: Standard Salesforce objects
- **Pink objects**: Platform events

Relationship notation:
- `||--o{` : One-to-many
- `||--||` : One-to-one
- `}o--o{` : Many-to-many
- Red lines: Master-Detail relationships
- Blue lines: Lookup relationships
