# ATS Package - Applicant Management ERD

## Entity Relationship Diagram

```mermaid
erDiagram
    %% Color coding for different object types
    ATS_Applicant__c {
        Id id PK "Record ID"
        Lookup Stage__c FK "ATS_Stage__c"
        Lookup Contact_Job_Relationship__c FK "Contact"
        MasterDetail Job__c FK "Job__c"
        Text Name "Auto Number"
        Checkbox Active__c "Default: True"
        Text Application_Source__c "255 chars"
        DateTime Applied_On__c "Date/Time applied"
        Currency Average_Rating__c "Rating average"
        Text City__c "255 chars"
        Text Country__c "255 chars"
        Email Email__c "Email address"
        Text First_Name__c "255 chars"
        Text Last_Name__c "255 chars"
        Phone Mobile_Phone__c "Mobile number"
        Phone Phone__c "Phone number"
        Number Rating_Count__c "Number of ratings"
        TextArea Rejection_Reason__c "Long 32768"
        Date Start_Date__c "Start date"
        Text State__c "255 chars"
        URL Website__c "Personal website"
        Number Years_of_Experience__c "18,2"
        Text Zip_Postal_Code__c "255 chars"
        DateTime CreatedDate "System field"
        DateTime LastModifiedDate "System field"
    }

    Job_Board_Application__c {
        Id id PK "Record ID"
        Lookup Contact__c FK "Contact"
        Lookup Job__c FK "Job__c"
        Text Name "Auto Number"
        Checkbox Active__c "Default: True"
        Text Address__c "255 chars"
        Checkbox Cover_Letter__c "Has cover letter"
        LongTextArea Cover_Letter_Text__c "131072 chars"
        Text Email__c "255 chars"
        Text First_Name__c "255 chars"
        Text Last_Name__c "255 chars"
        Text Phone__c "255 chars"
        Text Resume_Attachment_ID__c "255 chars"
        Text Source__c "255 chars"
        TextArea Status__c "Long 32768"
        URL Website__c "Website URL"
        DateTime CreatedDate "System field"
        DateTime LastModifiedDate "System field"
    }

    Job__c {
        Id id PK "Record ID"
        Text Name "Job Title"
        Text Status__c "Open/Closed"
        Text Department__c "Department"
        Text Location__c "Job Location"
        Date Posted_Date__c "Date Posted"
        Date Close_Date__c "Closing Date"
        LongTextArea Description__c "Job Description"
    }

    ATS_Stage__c {
        Id id PK "Record ID"
        Text Name "Stage Name"
        Number Order__c "Stage Order"
        Checkbox Active__c "Is Active"
        Text Stage_Type__c "Type of Stage"
        Checkbox Default__c "Default Stage"
    }

    Contact {
        Id id PK "Salesforce Standard"
        Text FirstName "First Name"
        Text LastName "Last Name"
        Email Email "Email Address"
        Phone Phone "Phone Number"
        Phone MobilePhone "Mobile Number"
        Text MailingCity "City"
        Text MailingState "State"
        Text MailingCountry "Country"
        Text MailingPostalCode "Zip Code"
    }

    %% Relationships with cardinality
    ATS_Applicant__c ||--o{ ATS_Stage__c : "has stage"
    ATS_Applicant__c ||--o{ Contact : "related to contact"
    ATS_Applicant__c }o--|| Job__c : "applies for job"
    
    Job_Board_Application__c ||--o{ Contact : "submitted by"
    Job_Board_Application__c ||--o{ Job__c : "application for"
    
    %% Additional relationships
    Job__c ||--o{ ATS_Applicant__c : "has applicants"
    Job__c ||--o{ Job_Board_Application__c : "receives applications"
    Contact ||--o{ ATS_Applicant__c : "is applicant"
    Contact ||--o{ Job_Board_Application__c : "submits application"
    ATS_Stage__c ||--o{ ATS_Applicant__c : "contains applicants"

```

## Object Descriptions

### ATS_Applicant__c (Custom Object)
**Purpose**: Core object for managing job applicants within the ATS system
- **Color**: Primary object (Blue in diagram)
- **Key Relationships**:
  - Master-Detail with Job__c (child of Job)
  - Lookup to ATS_Stage__c (current application stage)
  - Lookup to Contact (applicant's contact record)

### Job_Board_Application__c (Custom Object)
**Purpose**: Manages external job board applications before conversion to ATS_Applicant__c
- **Color**: Secondary object (Green in diagram)
- **Key Relationships**:
  - Lookup to Contact (applicant contact)
  - Lookup to Job__c (job being applied for)

### Related Standard & Custom Objects

#### Job__c (Custom Object)
- Parent object for applicants
- Contains job posting details

#### ATS_Stage__c (Custom Object)
- Defines recruitment pipeline stages
- Referenced by ATS_Applicant__c

#### Contact (Standard Object)
- Salesforce standard Contact object
- Central record for applicant information

## Relationship Cardinality Notation

- `||--o{` : One to many (mandatory)
- `||--o{` : One to many (optional)
- `}o--||` : Many to one (mandatory)
- `||--||` : One to one

## Field Types Legend

- **PK**: Primary Key
- **FK**: Foreign Key
- **MasterDetail**: Master-Detail Relationship
- **Lookup**: Lookup Relationship
- **Text**: Text field with character limit
- **TextArea**: Long text area
- **LongTextArea**: Rich text area
- **Checkbox**: Boolean field
- **Currency**: Currency field
- **Number**: Numeric field with precision
- **Date/DateTime**: Date or DateTime field
- **Email**: Email field
- **Phone**: Phone field
- **URL**: URL field

## Data Flow

1. **Job Board Application** → **Contact** → **ATS Applicant**
   - External applications create/update Contact records
   - Contacts are linked to ATS_Applicant__c records

2. **ATS Applicant** → **Stage Movement**
   - Applicants progress through ATS_Stage__c records
   - Stage history tracked via Stage__c lookup

3. **Job** → **Applications**
   - One Job can have multiple ATS_Applicant__c records
   - One Job can receive multiple Job_Board_Application__c entries