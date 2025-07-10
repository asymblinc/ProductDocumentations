# ERD: Standard Object Extensions - ATS Package

## Overview
This ERD illustrates how standard Salesforce objects are extended with custom fields to support the ATS (Applicant Tracking System) package functionality.

```mermaid
erDiagram
    Contact {
        ID Id PK
        String FirstName
        String LastName
        String Email
        String Phone
        String Title
        ID AccountId FK
        ID OwnerId FK
        DateTime CreatedDate
        DateTime LastModifiedDate
        String Contact_Relationship__c "Picklist"
        Currency Current_Salary__c
        Date Earliest_Available_Date__c
        Currency Current_Pay_Rate__c
        Currency Pay_Rate__c
        Currency Desired_Salary__c
        Boolean ATS_Is_Job_Board_Applicant__c
        ID ATS_Job_Board_Application__c FK
        Boolean Is_Resume_Parsed__c
        Text Parsing_Error__c
    }

    ContentVersion {
        ID Id PK
        String Title
        String PathOnClient
        Blob VersionData
        String ContentUrl
        String Description
        String FileType
        String FileExtension
        ID OwnerId FK
        ID ContentDocumentId FK
        DateTime CreatedDate
        DateTime LastModifiedDate
        Boolean Is_Primary__c "Custom Field"
    }

    ContentDocumentLink {
        ID Id PK
        ID LinkedEntityId FK
        ID ContentDocumentId FK
        String ShareType
        String Visibility
    }

    ContentDocument {
        ID Id PK
        String Title
        DateTime CreatedDate
        DateTime LastModifiedDate
    }

    EmailMessage {
        ID Id PK
        String FromAddress
        String ToAddress
        String Subject
        Text TextBody
        Text HtmlBody
        ID ParentId FK
        String Status
        DateTime MessageDate
        ID RelatedToId FK
        Number Clicks_Count__c "Custom Field"
        DateTime Last_Event_Time__c "Custom Field"
        Number Opens_Count__c "Custom Field"
        Text Status__c "Custom Field"
        Text Twilio_Message_Id__c "Custom Field"
        Text Message_Type__c "Custom Field"
        Phone Mobile_Phone__c "Custom Field"
    }

    Job_Board_Application__c {
        ID Id PK
        String Name
        ID Contact__c FK
        ID Job_Board__c FK
        ID Job_Posting__c FK
        DateTime Application_Date__c
        String Status__c
    }

    Job_Posting__c {
        ID Id PK
        String Name
        Text Description__c
        String Status__c
        Date Posted_Date__c
        ID Position__c FK
    }

    Position__c {
        ID Id PK
        String Name
        Text Description__c
        String Status__c
        ID Hiring_Manager__c FK
        Currency Min_Salary__c
        Currency Max_Salary__c
    }

    Candidate__c {
        ID Id PK
        String Name
        ID Contact__c FK
        String Status__c
        Date First_Applied__c
        Date Last_Applied__c
    }

    Application__c {
        ID Id PK
        String Name
        ID Candidate__c FK
        ID Position__c FK
        ID Contact__c FK
        String Status__c
        DateTime Application_Date__c
        String Source__c
    }

    Interview__c {
        ID Id PK
        String Name
        ID Application__c FK
        ID Candidate__c FK
        ID Interviewer__c FK
        DateTime Interview_Date__c
        String Status__c
        Text Notes__c
    }

    Offer__c {
        ID Id PK
        String Name
        ID Application__c FK
        ID Candidate__c FK
        ID Position__c FK
        Currency Offer_Amount__c
        Date Start_Date__c
        String Status__c
    }

    Contact ||--o{ Job_Board_Application__c : "ATS_Job_Board_Application__c"
    Contact ||--o{ Candidate__c : "has"
    Contact ||--o{ Application__c : "applicant"
    
    Job_Board_Application__c }o--|| Job_Posting__c : "applies to"
    
    ContentDocumentLink }o--|| ContentDocument : "links to"
    ContentDocumentLink }o--|| Contact : "LinkedEntityId"
    ContentDocumentLink }o--|| Application__c : "LinkedEntityId"
    ContentDocumentLink }o--|| Candidate__c : "LinkedEntityId"
    
    ContentDocument ||--o{ ContentVersion : "has versions"
    
    EmailMessage }o--|| Contact : "RelatedToId"
    EmailMessage }o--|| Application__c : "RelatedToId"
    EmailMessage }o--|| Candidate__c : "RelatedToId"
    EmailMessage }o--|| Interview__c : "RelatedToId"
    
    Application__c }o--|| Candidate__c : "from"
    Application__c }o--|| Position__c : "for"
    
    Interview__c }o--|| Application__c : "related to"
    Interview__c }o--|| Candidate__c : "with"
    
    Offer__c }o--|| Application__c : "for"
    Offer__c }o--|| Candidate__c : "to"
    Offer__c }o--|| Position__c : "for position"
    
    Position__c ||--o{ Job_Posting__c : "has postings"
```

## Standard Object Extensions Details

### 1. Contact Object Extensions

The Contact object is extended with ATS-specific fields to capture candidate information:

| Field API Name | Field Type | Description | Relationship |
|----------------|------------|-------------|--------------|
| Contact_Relationship__c | Picklist | Type of relationship with the contact | - |
| Current_Salary__c | Currency | Contact's current salary | - |
| Earliest_Available_Date__c | Date | When the contact can start work | - |
| Current_Pay_Rate__c | Currency | Current hourly/daily rate | - |
| Pay_Rate__c | Currency | Desired pay rate | - |
| Desired_Salary__c | Currency | Expected salary | - |
| ATS_Is_Job_Board_Applicant__c | Checkbox | Indicates if contact applied via job board | - |
| ATS_Job_Board_Application__c | Lookup | Links to Job Board Application | Job_Board_Application__c |
| Is_Resume_Parsed__c | Checkbox | Indicates if resume was parsed | - |
| Parsing_Error__c | Text | Error details if parsing failed | - |

### 2. ContentVersion Object Extensions

ContentVersion is extended to manage document priorities:

| Field API Name | Field Type | Description | Usage |
|----------------|------------|-------------|--------|
| Is_Primary__c | Checkbox | Marks primary document (e.g., main resume) | Used to identify the main resume among multiple documents |

**Relationships:**
- ContentVersion → ContentDocument (Standard)
- ContentDocument → ContentDocumentLink → Contact/Application/Candidate (via LinkedEntityId)

### 3. EmailMessage Object Extensions

EmailMessage is extended for email tracking and SMS capabilities:

| Field API Name | Field Type | Description | Integration |
|----------------|------------|-------------|-------------|
| Clicks_Count__c | Number | Number of link clicks in email | Email tracking |
| Last_Event_Time__c | DateTime | Timestamp of last interaction | Email tracking |
| Opens_Count__c | Number | Number of email opens | Email tracking |
| Status__c | Text | Custom status tracking | Workflow management |
| Twilio_Message_Id__c | Text | Twilio SMS message identifier | SMS integration |
| Message_Type__c | Text | Type of message (Email/SMS) | Multi-channel comms |
| Mobile_Phone__c | Phone | Mobile number for SMS | SMS delivery |

**Relationships:**
- EmailMessage → Contact (via RelatedToId)
- EmailMessage → Application__c (via RelatedToId)
- EmailMessage → Candidate__c (via RelatedToId)
- EmailMessage → Interview__c (via RelatedToId)

## Integration Patterns

### 1. Document Management
- Resumes and documents are stored as ContentVersion records
- Is_Primary__c identifies the main resume
- ContentDocumentLink connects documents to Contacts, Applications, and Candidates

### 2. Communication Tracking
- EmailMessage tracks all communications with candidates
- Extended fields enable email open/click tracking
- SMS capabilities through Twilio integration

### 3. Job Board Integration
- Contact.ATS_Is_Job_Board_Applicant__c flags job board applicants
- Contact.ATS_Job_Board_Application__c links to the specific application
- Enables tracking of application sources

### 4. Resume Parsing
- Contact.Is_Resume_Parsed__c indicates parsing status
- Contact.Parsing_Error__c captures any parsing errors
- Integrates with resume parsing services

## Key Relationships Summary

1. **Contact ↔ ATS Objects**
   - Contact → Job_Board_Application__c (via lookup)
   - Contact → Candidate__c (1:many)
   - Contact → Application__c (1:many)

2. **ContentVersion ↔ ATS Objects**
   - ContentVersion → ContentDocument → ContentDocumentLink → ATS Objects
   - Supports multiple document attachments per record

3. **EmailMessage ↔ ATS Objects**
   - EmailMessage → Various ATS objects via RelatedToId
   - Enables activity tracking across the recruitment process

## Implementation Notes

1. **Data Model Considerations**
   - Standard objects are extended rather than replaced
   - Maintains Salesforce standard functionality
   - Enables reporting across standard and custom fields

2. **Security Model**
   - Field-level security should be configured for custom fields
   - Sharing rules apply to both standard and custom fields
   - Consider field accessibility for different user profiles

3. **Performance Optimization**
   - Index custom lookup fields for better query performance
   - Consider data archiving for historical email messages
   - Implement selective queries on extended objects