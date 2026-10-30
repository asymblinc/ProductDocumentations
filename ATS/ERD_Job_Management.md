# Job Management ERD - ATS Package

> **Revision note (2026-10-30)**: This ERD was completely rebuilt against the real `Job__c`, `ATS_Template__c`, and `ATS_Stage__c` field metadata on `master`. The prior revision contained numerous fabricated fields (`Department__c`, `Min_Salary__c`/`Max_Salary__c`, `Is_Confidential__c`, `Is_Remote__c`, `External_Job_ID__c`, `Application_Deadline__c`, `Stage_Configuration__c`, `Approval_Process__c`, and more) that do not exist anywhere in the package. All fields below are verified against `force-app/main/default/objects/{Job__c,ATS_Template__c,ATS_Stage__c}/fields`.

## Entity Relationship Diagram

```mermaid
erDiagram
    %% Color coding for different object types
    Job__c {
        ID Id PK "Record ID"
        Text Job_Title__c "Job Title"
        Lookup Account__c FK "Account Reference"
        Text Account_Name__c "Account Name"
        Text Applicants_View__c "Applicants View config"
        Lookup ATS_Template__c FK "ATS Template Reference"
        Number Asymbl_Hours_Per_Week__c "Expected weekly hours"
        LongTextArea Benefits_Perks__c "Benefits and Perks"
        Currency Bill_Rate__c "Bill Rate"
        Currency Bonus__c "Bonus"
        Checkbox Budgeted__c "Is the opening budgeted"
        Text Card_Action_to_Display__c "Kanban card action config"
        Percent Conversion_Percentage__c "Conversion Percentage"
        Lookup Delivery_Lead__c FK "Delivery Lead (User)"
        Date End_Date__c "End Date"
        Currency Estimated_Revenue__c "Estimated Revenue"
        Text Experience__c "Experience"
        LongTextArea Fields_to_Display_on_Cards__c "Fields to Display on Cards (JSON)"
        Currency Fixed_Fee__c "Fixed Fee"
        Number Guarantee_Period_Weeks__c "Guarantee Period (Weeks)"
        Checkbox Hide_Delete_Action__c "Hide delete action on card"
        Checkbox Hide_Edit_Action__c "Hide edit action on card"
        Checkbox Hide_Reject_Action__c "Hide reject action on card"
        Lookup Hiring_Manager__c FK "Hiring Manager (Contact)"
        Email Hiring_Manager_Email__c "Hiring Manager Email"
        Text Hiring_Manager_Name__c "Hiring Manager Name"
        Checkbox Hiring_Multiple_Candidates__c "Hiring Multiple Candidates"
        Number Index__c "Index for sorting"
        Checkbox Is_Active__c "Is Active"
        Checkbox Is_Closed__c "Is Closed"
        Text Job_Board_Field_Set_API_Name__c "Job Board Field Set API Name"
        RichTextArea Job_Description__c "Job Description (Rich Text)"
        Picklist Job_Level__c "Job Level (1-5)"
        Text Job_Number__c "Job Number (Auto Number)"
        Text Location__c "Job Location"
        DateTime Matching_Candidates_Last_Refreshed__c "Last matching-intelligence refresh"
        Picklist New_or_Replacement__c "Addition / Replacement"
        Number Number_Of_Mass_Actions__c "Number of Mass Actions"
        Number Number_of_Openings__c "Number of Openings"
        Picklist Onsite_or_Remote__c "Onsite Position / Remote Position"
        Number Openings_Filled__c "Openings Filled"
        Lookup Opportunity__c FK "Opportunity Reference"
        Text Pay_Range__c "Pay Range"
        Currency Pay_Rate__c "Pay Rate"
        Currency Placement_Fee__c "Placement Fee"
        Checkbox Post_Externally__c "Post Externally"
        Date Posted_Date__c "Posted Date"
        Date Posting_End_Date__c "External posting expiry"
        Picklist Priority__c "Priority"
        LongTextArea Related_Lists_to_Display__c "Related Lists to Display"
        Text Replacement_for_whom__c "Who is being replaced"
        Currency Salary__c "Salary"
        Number Salary_Min__c "Salary range minimum"
        Number Salary_Max__c "Salary range maximum"
        Lookup Sales_Lead__c FK "Sales Lead (User)"
        Text Shift__c "Shift"
        TextArea Short_Description__c "Short Description"
        Checkbox Show_ATS_Stages__c "Show ATS Stages"
        Checkbox Show_Rejected_Applicants__c "Show rejected applicants in views"
        Date Start_Date__c "Start Date"
        Picklist Status__c "Job Status"
        Text Tags__c "Tags"
        Number Today_Goal__c "Today Goal"
        Picklist Travel_Required__c "Travel requirement (up to 25/50/75/100%)"
        Picklist Type__c "Job Type"
        Checkbox Urgent_Hiring__c "Urgent Hiring"
        Lookup Work_Site_Location__c FK "Work Site Location"
    }

    ATS_Template__c {
        ID Id PK "Record ID"
        Text Name "Template Name"
        Text ATS_Template_External_Id__c "External ID"
        Checkbox Add_To_Job__c "Auto-add-to-job behavior"
        Text Card_Action_to_Display__c "Card UI config"
        Checkbox Email_Applicant__c "Auto-email applicant"
        Text Field_to_Display_on_Job_Board__c "Job board display config"
        LongTextArea Fields_to_Display_on_Cards__c "Fields to Display on Cards (JSON)"
        LongTextArea Filter_Criteria__c "Filter Criteria"
        Checkbox Hide_Delete_Action__c "Hide delete action"
        Checkbox Hide_Edit_Action__c "Hide edit action"
        Checkbox Hide_Reject_Action__c "Hide reject action"
        Checkbox Is_Active__c "Is Active"
        Checkbox Is_Default__c "Is Default template"
        Number Priority__c "Template Priority"
        Checkbox Reject__c "Rejection behavior toggle"
        LongTextArea Related_Lists_to_Display__c "Related Lists to Display"
        LongTextArea Screening_Questions__c "Screening question config/JSON"
    }

    ATS_Stage__c {
        ID Id PK "Record ID"
        MasterDetail Job__c FK "Job (Master-Detail)"
        Lookup ATS_Template_Stage__c FK "Template Stage reference"
        Checkbox Block_Multiple_Candidates__c "Block multiple candidates"
        Checkbox Interview_Stage__c "Marks this as an interview stage"
        Checkbox Is_Synced__c "Synced with template"
        Text Job_Type__c "Job Type reference"
        Number Sequence__c "Stage Sequence"
        Text Sequence_Name__c "Display name (sequence + stage)"
        Picklist Stage_Type__c "Stage Type"
    }

    ATS_Job_Screening_Question__c {
        ID Id PK "Record ID"
        Lookup Job__c FK "Parent Job"
        Lookup ATS_Question__c FK "Screening Question"
        Checkbox Digital_Recruiter__c "Shown in Digital Recruiter flow"
        Checkbox External_Site__c "Shown on external career site"
        Checkbox Is_Mandatory__c "Required question"
        Text Question__c "Denormalized question text"
        Number Sequence__c "Display order"
    }

    Account {
        ID Id PK "Account ID"
        Text Name "Account Name"
    }

    Contact {
        ID Id PK "Contact ID"
        Text Name "Contact Name"
    }

    Opportunity {
        ID Id PK "Opportunity ID"
        Text Name "Opportunity Name"
    }

    User {
        ID Id PK "User ID"
        Text Name "User Name"
    }

    %% Relationships
    Job__c ||--o{ ATS_Stage__c : "has stages (1:N, Master-Detail)"
    Job__c }o--|| ATS_Template__c : "uses template (N:1)"
    Job__c }o--|| Account : "belongs to (N:1)"
    Job__c }o--|| Contact : "hiring manager (N:1)"
    Job__c }o--|| User : "delivery lead (N:1)"
    Job__c }o--|| User : "sales lead (N:1)"
    Job__c }o--|| Opportunity : "related to (N:1)"
    Job__c ||--o{ ATS_Job_Screening_Question__c : "has screening questions (1:N)"
    ATS_Stage__c }o--|| ATS_Template_Stage__c : "synced from (N:1)"
```

## Legend

### Relationship Notation
- `||--o{` : One-to-Many (1:N) relationship
- `}o--||` : Many-to-One (N:1) relationship
- `||--||` : One-to-One (1:1) relationship
- `}o--o{` : Many-to-Many (M:N) relationship

### Field Type Abbreviations
- **PK**: Primary Key
- **FK**: Foreign Key
- **Text**: Text field (255 characters)
- **TextArea**: Text Area field (4,096 characters)
- **LongTextArea**: Long Text Area field (32,768 characters)
- **RichTextArea**: Rich Text Area field (supports HTML formatting)
- **Lookup**: Lookup relationship field
- **MasterDetail**: Master-Detail relationship field
- **Currency**: Currency field
- **Number**: Number field
- **Date**: Date field
- **DateTime**: Date/Time field
- **Checkbox**: Boolean/Checkbox field
- **Picklist**: Single-select picklist field
- **Percent**: Percentage field

### Object Type Color Coding
- **Job__c**: Primary business object (darker shade)
- **ATS_Template__c**: Configuration/Template object (medium shade)
- **ATS_Stage__c**: Child/Detail object (lighter shade)
- **ATS_Job_Screening_Question__c**: Screening configuration object (new domain)
- **Account/Contact/Opportunity/User**: Standard Salesforce objects (lightest shade)

## Key Relationships

1. **Job__c to ATS_Stage__c** (1:N Master-Detail)
   - A Job can have multiple stages
   - Stages cannot exist without a parent Job; cascade delete applies

2. **Job__c to ATS_Template__c** (N:1 Lookup)
   - Multiple Jobs can use the same template
   - Template defines the standard process for jobs, including screening questions (`Screening_Questions__c`) and card/UI action toggles

3. **Job__c to Account** (N:1 Lookup)
   - Multiple Jobs can belong to one Account — represents the client/company relationship

4. **Job__c to Contact** (N:1 Lookup via Hiring_Manager__c)
   - Each Job has a Hiring Manager who is a Contact

5. **Job__c to User** (Multiple N:1 Lookups)
   - `Delivery_Lead__c`: User responsible for delivery
   - `Sales_Lead__c`: User responsible for sales

6. **Job__c to Opportunity** (N:1 Lookup)
   - Jobs can be linked to sales opportunities

7. **Job__c to ATS_Job_Screening_Question__c** (1:N Lookup)
   - A Job can have multiple configured screening questions, each independently toggleable for the Digital Recruiter flow and/or the external career site

## Business Logic Notes

1. **ATS_Stage__c** records are automatically created when a Job__c is created, based on the selected ATS_Template__c; `Is_Synced__c` tracks whether the job's stages still match the template
2. The **Sequence__c** field in ATS_Stage__c determines the order of stages in the hiring process; `Interview_Stage__c` flags stages that represent an interview step
3. **Is_Active__c** fields allow for soft deactivation and template versioning
4. **Filter_Criteria__c** in ATS_Template__c contains structured text/JSON for dynamic template-to-job matching, evaluated against `ATS_Template_Filter__c` records
5. **Salary_Min__c / Salary_Max__c** replace what the prior documentation revision incorrectly called `Min_Salary__c`/`Max_Salary__c`; there is no `Department__c`, `Is_Confidential__c`, or `External_Job_ID__c` field on Job__c
6. **Matching_Candidates_Last_Refreshed__c** is populated by the candidate/job matching intelligence layer (see `ATS_Match_Cache__c` and `Qualification_Run__c` in the [Missing Objects Complete ERD](ERD_Missing_Objects_Complete.md))
