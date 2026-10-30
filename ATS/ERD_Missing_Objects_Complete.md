# ERD Diagrams for Supporting, Log, and Matching Objects

> **Revision note (2026-10-30)**: Refreshed against `master`. Prior content for `Interview_Template_Detail__c`, `Interview_Topic_Feedback__c`, `Message_Stats__c`, and `Google_Recaptcha_Detail__c` was largely accurate but has been corrected (e.g. `Interview_Topic_Feedback__c` has no `Interview_Feedback__c` lookup field — it relates only via `Topic__c`; `Message_Stats__c` has no `Spam_Reports__c`/`Unsubscribes__c` fields). This revision also adds full coverage for the candidate/job matching intelligence objects (`ATS_Match_Cache__c`, `ATS_Candidate_Search_Log__c`, `ATS_Search_Location__c`, `Qualification_Run__c`) and `ATS_Log__c`, none of which appeared in the prior revision of this file.

This document contains comprehensive Entity Relationship Diagrams (ERD) for objects that are not fully detailed in the primary domain-specific ERDs (Job Management, Applicant Management, Interview Management, Placement/Credit/Target Management).

## 1. Interview_Template_Detail__c

```mermaid
erDiagram
    Interview_Template_Detail__c {
        ID Id PK
        MasterDetail Interview_Plan_Template__c FK "Master-Detail to Interview_Template__c"
        Lookup Topic_Kit__c FK "Lookup to Interview_Topic_Kit__c (new)"
        Text Interviewers__c
        Text Interviewers_to_display__c
        Text Stage__c
        LongTextArea Field_to_display__c
        Checkbox Fields_to_Display_Same_as_Template__c
        Checkbox Hide_Topics__c "new"
        Picklist Feedback_Format__c
        TextArea Feedback_Picklist_Options__c
        Picklist Topic_Source__c "Custom / Kit - new"
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        Boolean IsDeleted
        Text Name
        ID OwnerId FK
        DateTime SystemModstamp
    }

    Interview_Template__c ||--o{ Interview_Template_Detail__c : "has (Master-Detail)"
    Interview_Topic_Kit__c }o--o| Interview_Template_Detail__c : "sources topics for"
    User ||--o{ Interview_Template_Detail__c : "created"
    User ||--o{ Interview_Template_Detail__c : "lastModified"
    User ||--o{ Interview_Template_Detail__c : "owns"

    classDef masterDetail fill:#ff9999,stroke:#333,stroke-width:2px
    classDef lookup fill:#99ccff,stroke:#333,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px

    class Interview_Template_Detail__c standard
    class Interview_Template__c masterDetail
    class Interview_Topic_Kit__c lookup
```

## 2. Interview_Topic_Feedback__c

```mermaid
erDiagram
    Interview_Topic_Feedback__c {
        ID Id PK
        Lookup Topic__c FK "Lookup to Interview_Topic__c"
        TextArea Feedback__c
        Number Topic_Rating__c
        Checkbox Topic_Asked__c
        Text Topic_Feedback__c
        Checkbox Is_Additional_Topic__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        Boolean IsDeleted
        Text Name
        ID OwnerId FK
        DateTime SystemModstamp
    }

    Interview_Topic__c ||--o{ Interview_Topic_Feedback__c : "related to"
    User ||--o{ Interview_Topic_Feedback__c : "created"
    User ||--o{ Interview_Topic_Feedback__c : "lastModified"
    User ||--o{ Interview_Topic_Feedback__c : "owns"

    classDef lookup fill:#99ccff,stroke:#333,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px

    class Interview_Topic_Feedback__c standard
    class Interview_Topic__c lookup
```

> **Correction**: the prior revision of this ERD included a direct `Interview_Feedback__c` lookup field on `Interview_Topic_Feedback__c`. That field does not exist on the object in current metadata — the only custom relationship field is `Topic__c`.

## 3. Message_Stats__c

```mermaid
erDiagram
    Message_Stats__c {
        ID Id PK
        Lookup Parent__c FK "Polymorphic"
        Number Blocks__c
        Number Bounces__c
        Number Clicks__c
        Date Date__c
        Number Delivered__c
        Text External_Key__c
        Number Opens__c
        Picklist Stats_Type__c
        Number Unique_Clicks__c
        Number Unique_Opens__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        Boolean IsDeleted
        Text Name
        ID OwnerId FK
        DateTime SystemModstamp
    }

    Generic_Object ||--o{ Message_Stats__c : "parent (polymorphic)"
    User ||--o{ Message_Stats__c : "created"
    User ||--o{ Message_Stats__c : "lastModified"
    User ||--o{ Message_Stats__c : "owns"

    classDef lookup fill:#99ccff,stroke:#333,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px

    class Message_Stats__c standard
    class Generic_Object lookup
```

> **Correction**: the prior revision invented `Spam_Reports__c` and `Unsubscribes__c` fields. These do not exist on `Message_Stats__c` in current metadata — the full field set is exactly the 12 fields listed above.

## 4. Google_Recaptcha_Detail__c

```mermaid
erDiagram
    Google_Recaptcha_Detail__c {
        ID Id PK
        Text Client_Id__c
        Text Key__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        Boolean IsDeleted
        Text Name
        ID OwnerId FK
        DateTime SystemModstamp
    }

    User ||--o{ Google_Recaptcha_Detail__c : "created"
    User ||--o{ Google_Recaptcha_Detail__c : "lastModified"
    User ||--o{ Google_Recaptcha_Detail__c : "owns"

    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px

    class Google_Recaptcha_Detail__c standard
```

> **Correction**: the prior revision invented `Site_Key__c`, `Recaptcha_URL__c`, `Version__c`, `Domain__c` fields. The object's actual complete field set is exactly `Client_Id__c` and `Key__c`.

## 5. ATS_Log__c

```mermaid
erDiagram
    ATS_Log__c {
        ID Id PK
        Text Class_Name__c
        Text Method_Name__c
        LongTextArea Log_Message__c
        Picklist Log_Type__c "Error/Info/Warning"
        LongTextArea Error_Stack_trace__c
        Number Line_Number__c
        Checkbox Preserve_Log__c
        Text Related_Record_Id__c
        DateTime CreatedDate
        ID CreatedById FK
    }

    User ||--o{ ATS_Log__c : "created"

    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px
    class ATS_Log__c standard
```

## 6. Candidate/Job Matching Intelligence Objects (new domain)

```mermaid
erDiagram
    ATS_Match_Cache__c {
        ID Id PK
        Lookup Candidate__c FK "Contact"
        Lookup Job__c FK
        LongTextArea Payload__c "Cached match payload"
        Number Sequence__c
    }

    ATS_Candidate_Search_Log__c {
        ID Id PK
        Number Candidates_Added__c
        LongTextArea Filters__c
        Text Keywords__c
        DateTime Last_Searched__c
        Checkbox More_Results__c
        LongTextArea Refinements__c
        Number Results_Found__c
    }

    ATS_Search_Location__c {
        ID Id PK
        Text County__c
        Text Location__c
        Number Minimum_Radius_Mi__c
        Number Ranking__c
        Text Simplemaps_Id__c "External geo dataset key"
        Picklist Type__c
    }

    Qualification_Run__c {
        ID Id PK
        Lookup Asymbl_Job__c FK "Job__c"
        Number Asymbl_Expected_Value__c
        Checkbox Asymbl_Is_Qualified__c
        Picklist Asymbl_Margin_Band__c
        Text Asymbl_Pipeline_Exposure__c
        Picklist Asymbl_Risk_Tier__c
        DateTime Asymbl_Run_Date__c
        Text Asymbl_Run_Key__c
        Text Asymbl_Top_Action__c
        Number Asymbl_Top_Action_Priority__c
        Number Asymbl_Unmet_Count__c
        Number Asymbl_Weighted_Value__c
    }

    Contact {
        ID Id PK
        Text Name
        DateTime Matching_Jobs_Last_Refreshed__c "ATS extension"
    }

    Job__c {
        ID Id PK
        Text Job_Title__c
        DateTime Matching_Candidates_Last_Refreshed__c
    }

    Contact ||--o{ ATS_Match_Cache__c : "cached as candidate in"
    Job__c ||--o{ ATS_Match_Cache__c : "cached matches for"
    Job__c ||--o{ Qualification_Run__c : "qualification runs for"

    classDef matching fill:#ffe0b2,stroke:#e65100,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px

    class ATS_Match_Cache__c,ATS_Candidate_Search_Log__c,ATS_Search_Location__c,Qualification_Run__c matching
    class Contact,Job__c standard
```

### Qualification_Run__c Notes
- `Asymbl_Run_Key__c` functions as an idempotency/dedup key for repeated runs against the same job/candidate pairing
- `Asymbl_Unmet_Count__c` reflects how many `ATS_Qualification_Config__mdt` gates were not satisfied
- `Asymbl_Risk_Tier__c` and `Asymbl_Margin_Band__c` are categorical outputs of the scoring engine (`ATS_QualificationGateEngine`)

### ATS_Search_Location__c Notes
- Supports radius-based candidate search; `Simplemaps_Id__c` ties the record to the Simplemaps third-party geographic reference dataset
- `Ranking__c` likely controls display/priority order in location-based search results

## 7. Remaining Custom Metadata Types Not Separately Diagrammed

The following metadata types are covered in full field-level detail in the [Configuration Metadata ERD](ERD_Configuration_Metadata.md) and are referenced here only for completeness of the "nothing missing" verification pass:

- `ATS_Demo_Data_Info__mdt`, `ATS_Job_View_List__mdt`, `ATS_Job_Description_Template__mdt` (UI/config)
- `ATS_Applicant_Field_Type_Config__mdt`, `ATS_Platform_Event_Restriction__mdt`, `Feature_Gate__mdt` (new since prior revision)
- `ATS_Qualification_Config__mdt`, `ATS_Talent_Intelligence_Data__mdt`, `ATS_Job_Intelligence_Setting__mdt` (new matching-intelligence config)
- `ATS_Resume_Field_Mapping__mdt`, `ATS_Skill_Summary_Mapping__mdt`, `ATS_Work_Experience_Mapping__mdt` (new resume-mapping config)

## Relationship Summary

### Interview_Template_Detail__c Relationships:
- **Master-Detail** with Interview_Template__c (parent, via `Interview_Plan_Template__c`)
- **Lookup** to Interview_Topic_Kit__c (new) when sourcing topics from a reusable kit
- **Lookup** relationships with User (CreatedBy, LastModifiedBy, Owner)

### Interview_Topic_Feedback__c Relationships:
- **Lookup** to Interview_Topic__c only (no direct Interview_Feedback__c relationship field)
- **Lookup** relationships with User (CreatedBy, LastModifiedBy, Owner)

### Message_Stats__c Relationships:
- **Lookup** to Parent (polymorphic — can reference multiple object types depending on `Stats_Type__c`)
- **Lookup** relationships with User (CreatedBy, LastModifiedBy, Owner)

### Google_Recaptcha_Detail__c Relationships:
- No custom relationship fields; standard User audit lookups only

### Matching Intelligence Object Relationships:
- `ATS_Match_Cache__c`: Lookup to Contact and Job__c
- `Qualification_Run__c`: Lookup to Job__c (`Asymbl_Job__c`)
- `ATS_Candidate_Search_Log__c` / `ATS_Search_Location__c`: no formal object relationships in current metadata — standalone reference/log data

### Custom Metadata Type Relationships:
All metadata types have standard relationships with User for audit fields (CreatedBy, LastModifiedBy); custom metadata types do not support lookup fields to non-metadata objects, so all "relationships" to core ATS objects are logical (consulted via Apex), not formal schema relationships.

## Color Coding Legend
- 🔴 **Red**: Master-Detail Relationships
- 🔵 **Blue**: Lookup Relationships
- 🟢 **Green**: Standard Objects
- 🟠 **Orange**: Matching Intelligence Objects (new category)

## Field Type Definitions
- **Text**: Standard text field (up to 255 characters)
- **TextArea**: Long text area (up to 4,096/32,768 characters depending on field)
- **LongTextArea**: Long text area (larger capacity, up to 131,072 characters)
- **Number**: Numeric field with decimal support
- **Checkbox**: Boolean field (true/false)
- **Picklist**: Single-select dropdown
- **Date**: Date without time
- **DateTime**: Date with time
- **Lookup**: Reference to another record
- **MasterDetail**: Parent-child relationship with cascade delete

## Important Notes

1. **Interview_Template_Detail__c** has a Master-Detail relationship with Interview_Template__c, meaning child records are automatically deleted when the parent is deleted and inherit its sharing settings
2. **Message_Stats__c** uses a polymorphic `Parent__c` lookup, which can reference different object types depending on `Stats_Type__c`
3. **Google_Recaptcha_Detail__c** has a genuinely minimal two-field schema — there is no encryption or versioning field on this object despite what the prior documentation revision claimed
4. All Custom Metadata Types (`__mdt`) are deployable and packaged with the application
5. These objects support interview management, messaging analytics, security, and (new) candidate/job matching intelligence features of the ATS package
