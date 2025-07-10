# ERD Diagrams for Missing Objects

This document contains comprehensive Entity Relationship Diagrams (ERD) for all objects that were completely missed in the documentation.

## 1. Interview_Template_Detail__c

```mermaid
erDiagram
    Interview_Template_Detail__c {
        ID Id PK
        MasterDetail Interview_Plan_Template__c FK
        Text Interviewers__c
        Text Stage__c
        TextArea Field_to_display__c
        Checkbox Fields_to_Display_Same_as_Template__c
        Text Interviewers_to_display__c
        Picklist Feedback_Format__c
        TextArea Feedback_Picklist_Options__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        Boolean IsDeleted
        Text Name
        ID OwnerId FK
        DateTime SystemModstamp
    }
    
    Interview_Plan_Template__c ||--o{ Interview_Template_Detail__c : "has"
    User ||--o{ Interview_Template_Detail__c : "created"
    User ||--o{ Interview_Template_Detail__c : "lastModified"
    User ||--o{ Interview_Template_Detail__c : "owns"
    
    %% Color coding
    classDef masterDetail fill:#ff9999,stroke:#333,stroke-width:2px
    classDef lookup fill:#99ccff,stroke:#333,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px
    
    class Interview_Template_Detail__c standard
    class Interview_Plan_Template__c masterDetail
```

## 2. Interview_Topic_Feedback__c

```mermaid
erDiagram
    Interview_Topic_Feedback__c {
        ID Id PK
        TextArea Feedback__c
        Number Topic_Rating__c
        Lookup Topic__c FK
        Checkbox Topic_Asked__c
        Text Topic_Feedback__c
        Checkbox Is_Additional_Topic__c
        Lookup Interview_Feedback__c FK
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
    Interview_Feedback__c ||--o{ Interview_Topic_Feedback__c : "contains"
    User ||--o{ Interview_Topic_Feedback__c : "created"
    User ||--o{ Interview_Topic_Feedback__c : "lastModified"
    User ||--o{ Interview_Topic_Feedback__c : "owns"
    
    %% Color coding
    classDef masterDetail fill:#ff9999,stroke:#333,stroke-width:2px
    classDef lookup fill:#99ccff,stroke:#333,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px
    
    class Interview_Topic_Feedback__c standard
    class Interview_Topic__c lookup
    class Interview_Feedback__c lookup
```

## 3. Message_Stats__c

```mermaid
erDiagram
    Message_Stats__c {
        ID Id PK
        Number Blocks__c
        Number Bounces__c
        Number Clicks__c
        Date Date__c
        Number Delivered__c
        Number Opens__c
        Lookup Parent__c FK
        Picklist Stats_Type__c
        Number Spam_Reports__c
        Number Unique_Clicks__c
        Number Unique_Opens__c
        Number Unsubscribes__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        Boolean IsDeleted
        Text Name
        ID OwnerId FK
        DateTime SystemModstamp
    }
    
    Generic_Object ||--o{ Message_Stats__c : "parent"
    User ||--o{ Message_Stats__c : "created"
    User ||--o{ Message_Stats__c : "lastModified"
    User ||--o{ Message_Stats__c : "owns"
    
    %% Color coding
    classDef masterDetail fill:#ff9999,stroke:#333,stroke-width:2px
    classDef lookup fill:#99ccff,stroke:#333,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px
    
    class Message_Stats__c standard
    class Generic_Object lookup
```

### Stats_Type__c Picklist Values:
- Email
- SMS
- Push Notification
- In-App Message

## 4. Google_Recaptcha_Detail__c

```mermaid
erDiagram
    Google_Recaptcha_Detail__c {
        ID Id PK
        Text Client_Id__c
        EncryptedText Key__c
        Text Site_Key__c
        URL Recaptcha_URL__c
        Picklist Version__c
        Checkbox Is_Active__c
        Text Domain__c
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
    
    %% Color coding
    classDef masterDetail fill:#ff9999,stroke:#333,stroke-width:2px
    classDef lookup fill:#99ccff,stroke:#333,stroke-width:2px
    classDef standard fill:#99ff99,stroke:#333,stroke-width:2px
    
    class Google_Recaptcha_Detail__c standard
```

## 5. Custom Metadata Types

### ATS_Demo_Data_Info__mdt

```mermaid
erDiagram
    ATS_Demo_Data_Info__mdt {
        ID Id PK
        Text MasterLabel
        Text DeveloperName
        Text Language
        Text NamespacePrefix
        Text Label
        Text QualifiedApiName
        Text Object_Name__c
        Text Record_Type__c
        Number Number_of_Records__c
        Checkbox Is_Active__c
        Text Query_Filter__c
        Text Field_Mapping__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        DateTime SystemModstamp
    }
    
    User ||--o{ ATS_Demo_Data_Info__mdt : "created"
    User ||--o{ ATS_Demo_Data_Info__mdt : "lastModified"
    
    %% Color coding
    classDef metadata fill:#ffcc99,stroke:#333,stroke-width:2px
    
    class ATS_Demo_Data_Info__mdt metadata
```

### ATS_Job_View_List__mdt

```mermaid
erDiagram
    ATS_Job_View_List__mdt {
        ID Id PK
        Text MasterLabel
        Text DeveloperName
        Text Language
        Text NamespacePrefix
        Text Label
        Text QualifiedApiName
        Text List_View_Name__c
        Text Object_Name__c
        Text Filter_Criteria__c
        Text Sort_Order__c
        Text Fields_to_Display__c
        Number Display_Order__c
        Checkbox Is_Default__c
        Checkbox Is_Active__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        DateTime SystemModstamp
    }
    
    User ||--o{ ATS_Job_View_List__mdt : "created"
    User ||--o{ ATS_Job_View_List__mdt : "lastModified"
    
    %% Color coding
    classDef metadata fill:#ffcc99,stroke:#333,stroke-width:2px
    
    class ATS_Job_View_List__mdt metadata
```

### ATS_Job_Description_Template__mdt

```mermaid
erDiagram
    ATS_Job_Description_Template__mdt {
        ID Id PK
        Text MasterLabel
        Text DeveloperName
        Text Language
        Text NamespacePrefix
        Text Label
        Text QualifiedApiName
        Text Template_Name__c
        LongTextArea Template_Content__c
        Text Department__c
        Text Job_Family__c
        Picklist Template_Type__c
        Text Tags__c
        Checkbox Is_Active__c
        Checkbox Is_Default__c
        Number Sort_Order__c
        DateTime CreatedDate
        ID CreatedById FK
        DateTime LastModifiedDate
        ID LastModifiedById FK
        DateTime SystemModstamp
    }
    
    User ||--o{ ATS_Job_Description_Template__mdt : "created"
    User ||--o{ ATS_Job_Description_Template__mdt : "lastModified"
    
    %% Color coding
    classDef metadata fill:#ffcc99,stroke:#333,stroke-width:2px
    
    class ATS_Job_Description_Template__mdt metadata
```

## Relationship Summary

### Interview_Template_Detail__c Relationships:
- **Master-Detail** with Interview_Plan_Template__c (parent)
- **Lookup** relationships with User (CreatedBy, LastModifiedBy, Owner)

### Interview_Topic_Feedback__c Relationships:
- **Lookup** to Interview_Topic__c
- **Lookup** to Interview_Feedback__c
- **Lookup** relationships with User (CreatedBy, LastModifiedBy, Owner)

### Message_Stats__c Relationships:
- **Lookup** to Parent (polymorphic - can reference multiple objects)
- **Lookup** relationships with User (CreatedBy, LastModifiedBy, Owner)

### Google_Recaptcha_Detail__c Relationships:
- **Lookup** relationships with User (CreatedBy, LastModifiedBy, Owner)

### Custom Metadata Type Relationships:
All metadata types have standard relationships with User for audit fields (CreatedBy, LastModifiedBy)

## Color Coding Legend
- 🔴 **Red**: Master-Detail Relationships
- 🔵 **Blue**: Lookup Relationships
- 🟫 **Green**: Standard Objects
- 🟪 **Orange**: Custom Metadata Types

## Field Type Definitions
- **Text**: Standard text field (up to 255 characters)
- **TextArea**: Long text area (up to 32,768 characters)
- **LongTextArea**: Long text area (up to 131,072 characters)
- **Number**: Numeric field with decimal support
- **Checkbox**: Boolean field (true/false)
- **Picklist**: Single-select dropdown
- **Date**: Date without time
- **DateTime**: Date with time
- **Lookup**: Reference to another record
- **MasterDetail**: Parent-child relationship with cascade delete
- **EncryptedText**: Encrypted text field for sensitive data
- **URL**: URL field with validation

## Important Notes

1. **Interview_Template_Detail__c** has a Master-Detail relationship with Interview_Plan_Template__c, meaning:
   - Child records are automatically deleted when parent is deleted
   - Child records inherit sharing settings from parent
   - Roll-up summary fields can be created on the parent

2. **Message_Stats__c** uses a polymorphic Parent__c lookup, which can reference different object types depending on Stats_Type__c

3. **Google_Recaptcha_Detail__c** stores the Key__c as an encrypted field for security

4. All Custom Metadata Types (\_\_mdt) are deployable and can be packaged with the application

5. These objects support the interview management, messaging analytics, and security features of the ATS package