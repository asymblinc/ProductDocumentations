# ATS Package ERD Documentation Index

This repository contains comprehensive Entity Relationship Diagrams (ERDs) for the Asymbl ATS (Applicant Tracking System) Salesforce package. Each diagram provides detailed visualizations of objects, fields, and relationships.

## 📊 ERD Diagrams Available

### Core Functional Areas

1. **[Job Management ERD](./ERD_Job_Management.md)** *(Updated)*
   - Job__c (now with all 60+ fields), ATS_Template__c, ATS_Stage__c
   - Master-detail and lookup relationships
   - Complete field documentation including all missing fields

2. **[Applicant Management ERD](./ERD_Applicant_Management.md)**
   - ATS_Applicant__c, Job_Board_Application__c
   - Application flow and contact relationships
   - Stage tracking mechanisms

3. **[Interview Management ERD](./ERD_Interview_Management.md)**
   - Interview__c, Interview_Template__c, Interview_Feedback__c, Interview_Topic__c
   - Template-based interview structures
   - Feedback collection system

4. **[Placement & Credit Management ERD](./ERD_Placement_Credit_Management.md)**
   - Placement__c, Placement_Credit__c, Placement_Credit_Template__c
   - Credit allocation flow diagrams
   - Template-based credit distribution

### Configuration & Support

5. **[Action & Template Configuration ERD](./ERD_Action_Template_Configuration.md)**
   - ATS_Action__c, ATS_Action_Link__c, ATS_Template_Stage__c
   - Action automation framework
   - Stage-specific configurations

6. **[Supporting Objects ERD](./ERD_Supporting_Objects.md)**
   - Contact_List__c, Offer__c, Work_Site_Location__c
   - ATS_Stage_Tracking__c, ATS_Log__c, Branch__c
   - Purchase_Order__c and utility objects

7. **[Configuration Metadata ERD](./ERD_Configuration_Metadata.md)**
   - All custom metadata types (__mdt)
   - System settings and mappings
   - Integration configurations

### Technical Components

8. **[Integration Objects ERD](./ERD_Integration_Objects.md)**
   - TextKernel, Twilio, Zoom, Google integrations
   - Message_Stats__c, webhook management
   - Platform event architecture

9. **[Timeline & Event Objects ERD](./ERD_Timeline_Event_Objects.md)**
   - ATS_Timeline_Configuration__c, ATS_Timeline_Child__c
   - Platform events (ATS_Record_Action__e, Add_to_ATS__e)
   - UI configuration framework

### New Additions - Complete Coverage

10. **[Missing Objects Complete ERD](./ERD_Missing_Objects_Complete.md)** 🆕
    - Interview_Template_Detail__c
    - Interview_Topic_Feedback__c
    - Message_Stats__c
    - Google_Recaptcha_Detail__c
    - ATS_Demo_Data_Info__mdt
    - ATS_Job_View_List__mdt
    - ATS_Job_Description_Template__mdt

11. **[Standard Object Extensions ERD](./ERD_Standard_Object_Extensions.md)** 🆕
    - Contact extensions (10 custom fields)
    - ContentVersion extensions (Is_Primary__c)
    - EmailMessage extensions (7 custom fields)
    - Integration patterns with ATS objects

## 📚 Additional Documentation

- **[ATS Package Documentation](./ATS_Package_Documentation.md)** - Complete package overview with statistics and technical details
- **[ERD Audit Report](./ERD_Audit_Missing_Objects.md)** - Comprehensive audit of missing objects and coverage analysis

## 🎨 Diagram Features

All ERD diagrams include:
- ✅ Complete field listings with data types
- ✅ Relationship cardinality (1:1, 1:N, M:N)
- ✅ Color coding for object categories
- ✅ Primary and foreign key indicators
- ✅ Field size specifications where applicable
- ✅ Business logic documentation
- ✅ Mermaid syntax for GitHub rendering

## 📈 Coverage Status

After the latest updates:
- **Total Objects Documented**: 60+ custom objects
- **Standard Object Extensions**: 3 objects (Contact, ContentVersion, EmailMessage)
- **Metadata Types**: 15+ configuration objects
- **Coverage**: ~100% of all objects in package.xml

## 🔍 Quick Navigation

### By Object Type
- **Custom Objects**: Job__c, ATS_Applicant__c, Interview__c, Placement__c
- **Configuration Objects**: ATS_Template__c, ATS_Action__c, Interview_Template__c
- **Junction Objects**: ATS_Action_Link__c, Placement_Credit__c
- **Metadata Types**: Generic_Setting__mdt, TK_Field_Mapping__mdt
- **Platform Events**: ATS_Record_Action__e, Add_to_ATS__e
- **Standard Extensions**: Contact, ContentVersion, EmailMessage

### By Functionality
- **Recruitment Pipeline**: Job → Applicant → Interview → Placement
- **Template System**: Templates → Stages → Actions
- **Credit Management**: Placement → Credit Templates → Credit Allocation
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