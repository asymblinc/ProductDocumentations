# Interview Management ERD - ATS Package

> **Revision note (2026-10-30)**: Completely rebuilt against real field metadata. The prior revision invented a large number of fields and objects that do not exist — `Stage_Application__c`, `Interview__c.Interview_Score__c`/`Average_Rating__c`/`Time_Zone__c`, `Interview_Template__c.Parent_Template__c`/`Template_Version__c`/`Pass_Rate__c`, `Interview_Topic__c.Parent_Topic__c`/`Difficulty_Level__c`/`Assessment_Type__c`, and most of `Interview_Feedback__c`'s rating/visibility fields. It also predates the real **Interview Topic Kit** and **Interview Availability** domains added to the package since the prior revision. All fields below are verified against `force-app/main/default/objects/Interview*/fields`.

## Entity Relationship Diagram

```mermaid
erDiagram
    %% Color coding for different object types
    Interview__c {
        Id Id PK "Record ID"
        Lookup Candidate__c FK "Lookup to Contact"
        Lookup ATS_Applicant__c FK "Lookup to ATS_Applicant__c"
        Lookup Job__c FK "Lookup to Job__c"
        Lookup Interview_Template__c FK "Lookup to Interview_Template__c"
        Lookup Interview_Template_Detail__c FK "Lookup to Interview_Template_Detail__c"
        Lookup Asymbl_Interview_Availability__c FK "Lookup to Interview_Availability__c"
        Lookup Assigned_To__c FK "Lookup to User"
        Lookup Interviewer__c FK "Lookup to User (primary)"
        MultiSelectPicklist Interviewers__c "All interviewers"
        TextArea Additional_Information__c "Additional info"
        Text Candidate_Name__c "Candidate name (denormalized)"
        Number Duration_Minutes__c "Duration in minutes"
        Text Event_Id__c "Calendar event ID"
        LongTextArea Feedback__c "Interview feedback"
        Checkbox Filter_To_Client_Account__c "Restrict interviewer picker to client account"
        DateTime Interview_Date__c "Date/time"
        TextArea Interview_Description__c "Description"
        Checkbox Is_Scheduled_By_Automation__c "Scheduled by automation"
        Text Job_Position__c "Job position (denormalized)"
        Text Location__c "Location"
        Picklist Location_Type__c "Location type"
        Picklist Online_Platforms__c "None/Google Meet/Zoom Meeting"
        Picklist Status__c "Interview status"
        Picklist Type__c "Interview type"
        Text Zoom_Meeting_Id__c "Zoom meeting ID"
    }

    Interview_Template__c {
        Id Id PK "Record ID"
        Text Name "Template Name"
        Picklist Feedback_Format__c "Feedback format type"
        TextArea Feedback_Picklist_Options__c "Picklist values"
        LongTextArea Field_to_display__c "Field configuration"
        LongTextArea Filter_Criteria__c "Filter logic"
        Checkbox Is_Active__c "Active"
        Number Priority__c "Template priority"
    }

    Interview_Template_Detail__c {
        Id Id PK "Record ID"
        MasterDetail Interview_Plan_Template__c FK "Master-Detail to Interview_Template__c"
        Lookup Topic_Kit__c FK "Lookup to Interview_Topic_Kit__c"
        Text Stage__c "Stage name"
        Picklist Feedback_Format__c "Feedback format"
        TextArea Feedback_Picklist_Options__c "Picklist options"
        LongTextArea Field_to_display__c "Display fields"
        Checkbox Fields_to_Display_Same_as_Template__c "Inherit fields flag"
        Checkbox Hide_Topics__c "Hide topics on feedback form"
        Text Interviewers__c "Interviewer list"
        Text Interviewers_to_display__c "Display interviewers"
        Picklist Topic_Source__c "Custom / Kit"
    }

    Interview_Topic_Kit__c {
        Id Id PK "Record ID"
        Text Name "Kit name"
        LongTextArea Description__c "Kit description"
        TextArea Feedback_Picklist_Options__c "Default feedback options"
        Checkbox Is_Active__c "Active"
    }

    Interview_Topic_Kit_Item__c {
        Id Id PK "Record ID"
        MasterDetail Interview_Topic_Kit__c FK "Master-Detail to Interview_Topic_Kit__c"
        Text External_Interviewer__c "External interviewer"
        Picklist Feedback_Type__c "Feedback type"
        Number Sequence__c "Order within kit"
        TextArea Topic_Description__c "Topic description"
        Text Topic_Feedbacks__c "Feedback options"
    }

    Interview_Topic__c {
        Id Id PK "Record ID"
        Lookup Interview_Template__c FK "Lookup to Interview_Template__c"
        Lookup Interview_Template_Detail__c FK "Lookup to Interview_Template_Detail__c"
        Lookup Source_Kit_Item__c FK "Lookup to Interview_Topic_Kit_Item__c"
        Text External_Interviewer__c "External interviewer"
        Picklist Feedback_Type__c "Feedback type"
        Checkbox IsDefault__c "Default topic flag"
        Checkbox Is_Retired__c "Retired (soft-delete)"
        Number Sequence__c "Topic sequence"
        TextArea Topic_Description__c "Topic description"
        Text Topic_Feedbacks__c "Feedback options"
    }

    Interview_Feedback__c {
        Id Id PK "Record ID"
        MasterDetail Interview__c FK "Master-Detail to Interview__c"
        Lookup Candidate__c FK "Lookup to Contact"
        Lookup Interviewer__c FK "Lookup to User"
        Lookup Submitted_By__c FK "Lookup to User"
        Text External_Interviewer__c "External interviewer"
        DateTime Interview_Date__c "Interview date"
        LongTextArea Feedback__c "Feedback text"
        Picklist Recommended_New__c "Recommendation"
        DateTime Submission_Date_Time__c "Submission timestamp"
    }

    Interview_Topic_Feedback__c {
        Id Id PK "Record ID"
        Lookup Topic__c FK "Lookup to Interview_Topic__c"
        TextArea Feedback__c "Feedback text"
        Checkbox Is_Additional_Topic__c "Additional topic flag"
        Checkbox Topic_Asked__c "Topic covered flag"
        Text Topic_Feedback__c "Topic feedback"
        Number Topic_Rating__c "Rating value"
    }

    Interview_Availability__c {
        Id Id PK "Record ID"
        Lookup Asymbl_Job__c FK "Lookup to Job__c"
        Lookup Asymbl_Offered_By__c FK "Lookup to User"
        Checkbox Asymbl_Confirmed__c "Slot confirmed"
        DateTime Asymbl_Slot_Start__c "Slot start"
        DateTime Asymbl_Slot_End__c "Slot end"
        Text Asymbl_Source__c "Source of availability"
    }

    %% Related Objects
    Contact {
        Id Id PK "Standard Object"
        Text Name "Full Name"
        Email Email "Email field"
        Phone Phone "Phone field"
    }

    Job__c {
        Id Id PK "Standard/Custom"
        Text Job_Title__c "Job Title"
    }

    ATS_Applicant__c {
        Id Id PK "Custom Object"
        Text Applicant_Name__c "Applicant Name"
    }

    User {
        Id Id PK "Standard Object"
        Text Name "Full Name"
        Email Email "Email field"
    }

    %% Relationships
    Interview__c ||--o{ Interview_Feedback__c : "has feedback (Master-Detail)"
    Interview__c }o--|| Contact : "for candidate"
    Interview__c }o--o| ATS_Applicant__c : "for applicant"
    Interview__c }o--|| Job__c : "for job"
    Interview__c }o--o| Interview_Template__c : "uses template"
    Interview__c }o--o| Interview_Template_Detail__c : "uses template detail (stage)"
    Interview__c }o--o| Interview_Availability__c : "scheduled from slot"

    Interview_Template__c ||--o{ Interview_Template_Detail__c : "has details (Master-Detail)"
    Interview_Template__c ||--o{ Interview_Topic__c : "has topics"

    Interview_Template_Detail__c }o--o| Interview_Topic_Kit__c : "sources topics from"

    Interview_Topic_Kit__c ||--o{ Interview_Topic_Kit_Item__c : "has items (Master-Detail)"

    Interview_Topic_Kit_Item__c ||--o{ Interview_Topic__c : "materializes into"

    Interview_Feedback__c }o--|| User : "submitted by"
    Interview_Feedback__c ||--o{ Interview_Topic_Feedback__c : "has topic feedback"

    Interview_Topic__c ||--o{ Interview_Topic_Feedback__c : "evaluated in"

    Interview_Availability__c }o--|| User : "offered by"
    Interview_Availability__c }o--|| Job__c : "for job"

    %% Styling
    classDef primaryObject fill:#4A90E2,stroke:#2E5C8A,stroke-width:3px,color:#FFFFFF
    classDef kitObject fill:#F5A623,stroke:#B9790B,stroke-width:2px,color:#FFFFFF
    classDef relatedObject fill:#7FBA00,stroke:#5C8700,stroke-width:2px,color:#FFFFFF
    classDef standardObject fill:#FF6B6B,stroke:#CC5555,stroke-width:2px,color:#FFFFFF

    class Interview__c,Interview_Template__c,Interview_Template_Detail__c,Interview_Feedback__c,Interview_Topic__c primaryObject
    class Interview_Topic_Kit__c,Interview_Topic_Kit_Item__c,Interview_Availability__c kitObject
    class Interview_Topic_Feedback__c relatedObject
    class Contact,User,Job__c,ATS_Applicant__c standardObject
```

## Object Relationships Summary

### Interview__c (Primary Object)
- **Master Object for**: Interview_Feedback__c (Master-Detail)
- **Related to**: Contact (via `Candidate__c`), Job__c (via `Job__c`), ATS_Applicant__c (via `ATS_Applicant__c`)
- **Optional relationship to**: Interview_Template__c, Interview_Template_Detail__c (for the specific stage), Interview_Availability__c (the slot it was booked against)

### Interview_Template__c / Interview_Template_Detail__c (Configuration Objects)
- **Interview_Template__c** defines overall feedback format/filter criteria and owns multiple `Interview_Template_Detail__c` (one per interview stage)
- **Interview_Template_Detail__c** determines, per stage, whether topics are `Custom`-authored directly as `Interview_Topic__c` records, or sourced from a reusable `Interview_Topic_Kit__c` (`Topic_Source__c` = Kit)

### Interview_Topic_Kit__c / Interview_Topic_Kit_Item__c (new domain)
- A **Topic Kit** is a reusable library of interview topics (e.g., "Standard Technical Screen") authored once
- **Kit Items** are cloned into live `Interview_Topic__c` records (via `ATS_CloneInterviewTopicKit`) when attached to a template detail; the resulting topic retains a `Source_Kit_Item__c` lookup back to its origin for traceability

### Interview_Feedback__c (Detail Object)
- **Child of**: Interview__c (Master-Detail relationship)
- **Submitted by**: User (via `Interviewer__c`/`Submitted_By__c` lookups)
- **Topic-level detail**: `Interview_Topic_Feedback__c` records capture per-topic ratings/comments tied to a specific `Interview_Topic__c`

### Interview_Topic__c (Reference Data)
- **Sourced from**: Either direct authoring on a template detail, or a Topic Kit Item (`Source_Kit_Item__c`)
- **Retirement**: `Is_Retired__c` soft-deletes a topic from active use while preserving historical `Interview_Topic_Feedback__c` records that reference it
- There is **no** parent/child topic hierarchy (`Parent_Topic__c` does not exist) — reuse is achieved exclusively through Topic Kits

### Interview_Availability__c (new object)
- Interviewer-submitted time slots (`Asymbl_Slot_Start__c`/`Asymbl_Slot_End__c`) offered for a given Job; once confirmed (`Asymbl_Confirmed__c`) and consumed, the resulting `Interview__c` links back via `Asymbl_Interview_Availability__c`

## Key Features

1. **Interview Scheduling & Management**
   - Complete interview lifecycle tracking, calendar event ID capture, automation-aware scheduling flag (`Is_Scheduled_By_Automation__c`)
   - Interviewer-submitted availability slots feed directly into scheduling

2. **Template-Based Interviews with Reusable Topic Kits**
   - Reusable interview templates, broken into per-stage details
   - Topic Kits let teams define a topic library once and reuse it across many templates/stages, instead of re-authoring per template

3. **Comprehensive Feedback System**
   - Interview-level feedback plus topic-level feedback (`Interview_Topic_Feedback__c`) with asked/not-asked tracking and per-topic rating
   - Retired topics preserve historical feedback integrity

4. **Video Conferencing Integration**
   - `Online_Platforms__c` supports both Zoom Meeting and Google Meet
   - `Zoom_Meeting_Id__c` captures the Zoom-specific meeting reference

## Field Type Legend

- **PK**: Primary Key
- **FK**: Foreign Key
- **Lookup**: Lookup relationship field
- **MasterDetail**: Master-Detail relationship field
- **Text**: Text fields with specified length
- **TextArea / LongTextArea**: Long text fields for detailed content
- **Number**: Numeric fields with precision
- **DateTime/Date**: Date and time fields
- **Checkbox**: Boolean fields
- **Picklist / MultiSelectPicklist**: Single/multi-select picklist
- **Formula**: Calculated fields (none currently defined on these objects beyond what's noted)
