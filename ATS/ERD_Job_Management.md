# Job Management ERD - ATS Package

## Entity Relationship Diagram

```mermaid
erDiagram
    %% Color coding for different object types
    Job__c {
        ID Id PK "Record ID"
        Text Job_Title__c "Job Title"
        Lookup Account__c FK "Account Reference"
        Text Account_Name__c "Account Name"
        Text Applicants_View__c "Applicants View"
        Lookup ATS_Template__c FK "ATS Template Reference"
        LongTextArea Benefits_Perks__c "Benefits and Perks"
        Currency Bill_Rate__c "Bill Rate"
        Currency Bonus__c "Bonus"
        Percent Conversion_Percentage__c "Conversion Percentage"
        Lookup Delivery_Lead__c FK "Delivery Lead (User)"
        Text Department__c "Department"
        LongTextArea Description__c "Job Description"
        Date End_Date__c "End Date"
        Currency Estimated_Revenue__c "Estimated Revenue"
        Text Experience__c "Experience"
        Picklist Experience_Level__c "Experience Level"
        Text External_Job_ID__c "External Job ID"
        LongTextArea Fields_to_Display_on_Cards__c "Fields to Display on Cards"
        Currency Fixed_Fee__c "Fixed Fee"
        Number Guarantee_Period_Weeks__c "Guarantee Period (Weeks)"
        Lookup Hiring_Manager__c FK "Hiring Manager (Contact)"
        Text Hiring_Manager_Email__c "Hiring Manager Email"
        Text Hiring_Manager_Name__c "Hiring Manager Name"
        Checkbox Hiring_Multiple_Candidates__c "Hiring Multiple Candidates"
        Number Index__c "Index"
        Checkbox Is_Active__c "Is Active"
        Checkbox Is_Closed__c "Is Closed"
        Checkbox Is_Confidential__c "Is Confidential"
        Checkbox Is_Remote__c "Is Remote Position"
        RichTextArea Job_Description__c "Job Description (Rich Text)"
        Text Job_Number__c "Job Number"
        Picklist Job_Type__c "Job Type (Full-time/Part-time/Contract)"
        Text Location__c "Job Location"
        Currency Max_Salary__c "Maximum Salary"
        Currency Min_Salary__c "Minimum Salary"
        Number Number_Of_Mass_Actions__c "Number of Mass Actions"
        Number Number_of_Openings__c "Number of Openings"
        Number Number_of_Positions__c "Number of Positions"
        Number Openings_Filled__c "Openings Filled"
        Lookup Opportunity__c FK "Opportunity Reference"
        Text Pay_Range__c "Pay Range"
        Currency Pay_Rate__c "Pay Rate"
        Currency Placement_Fee__c "Placement Fee"
        Checkbox Post_Externally__c "Post Externally"
        Date Posted_Date__c "Posted Date"
        Picklist Priority__c "Priority"
        LongTextArea Related_Lists_to_Display__c "Related Lists to Display"
        MultiSelectPicklist Required_Skills__c "Required Skills"
        Currency Salary__c "Salary"
        Lookup Sales_Lead__c FK "Sales Lead (User)"
        Text Shift__c "Shift"
        TextArea Short_Description__c "Short Description"
        Checkbox Show_ATS_Stages__c "Show ATS Stages"
        Date Start_Date__c "Start Date"
        Picklist Status__c "Job Status"
        Text Tags__c "Tags"
        Number Today_Goal__c "Today Goal"
        Checkbox Urgent_Hiring__c "Urgent Hiring"
        Text Job_Board_Field_Set_API_Name__c "Job Board Field Set API Name"
        Date Application_Deadline__c "Application Deadline"
        Number Days_Open__c "Days Open (Formula)"
        Lookup Created_By__c FK "Created By (User)"
        DateTime Created_Date__c "Created Date"
        Lookup Last_Modified_By__c FK "Last Modified By (User)"
        DateTime Last_Modified_Date__c "Last Modified Date"
    }

    ATS_Template__c {
        ID Id PK "Record ID"
        Text Name "Template Name"
        Checkbox Is_Active__c "Is Active"
        LongTextArea Filter_Criteria__c "Filter Criteria"
        Text Template_Type__c "Template Type"
        LongTextArea Stage_Configuration__c "Stage Configuration JSON"
        Number Default_Days_To_Fill__c "Default Days to Fill"
        Picklist Approval_Process__c "Approval Process Type"
        MultiSelectPicklist Required_Documents__c "Required Documents"
        Text Email_Template_Set__c "Email Template Set"
        Checkbox Auto_Post_To_Portal__c "Auto Post to Portal"
        Number Interview_Rounds__c "Number of Interview Rounds"
        Text Scoring_Criteria__c "Scoring Criteria"
        Checkbox Background_Check_Required__c "Background Check Required"
        Checkbox Reference_Check_Required__c "Reference Check Required"
        Text Department_Specific__c "Department Specific"
        Picklist Priority_Level__c "Priority Level"
        Number SLA_Days__c "SLA Days"
        LongTextArea Custom_Instructions__c "Custom Instructions"
        Lookup Created_By__c FK "Created By (User)"
        DateTime Created_Date__c "Created Date"
        Lookup Last_Modified_By__c FK "Last Modified By (User)"
        DateTime Last_Modified_Date__c "Last Modified Date"
    }

    ATS_Stage__c {
        ID Id PK "Record ID"
        MasterDetail Job__c FK "Job (Master-Detail)"
        Number Sequence__c "Stage Sequence"
        Picklist Stage_Type__c "Stage Type"
        Text Stage_Name__c "Stage Name"
        Checkbox Is_Active__c "Is Active"
        Number Duration_Days__c "Duration in Days"
        Picklist Status__c "Stage Status"
        Date Start_Date__c "Start Date"
        Date End_Date__c "End Date"
        LongTextArea Stage_Criteria__c "Stage Criteria"
        MultiSelectPicklist Required_Actions__c "Required Actions"
        Text Assigned_To__c "Assigned To (User/Queue)"
        Checkbox Send_Notifications__c "Send Notifications"
        Text Email_Template__c "Email Template"
        Number Candidates_In_Stage__c "Candidates in Stage"
        Percent Pass_Rate__c "Pass Rate"
        Checkbox Is_Interview_Stage__c "Is Interview Stage"
        Text Interview_Type__c "Interview Type"
        Number Interview_Duration_Minutes__c "Interview Duration (Minutes)"
        MultiSelectPicklist Interviewers__c "Interviewers"
        Checkbox Auto_Schedule__c "Auto Schedule Interviews"
        Text Meeting_Location__c "Meeting Location"
        LongTextArea Stage_Instructions__c "Stage Instructions"
        Checkbox Is_Elimination_Stage__c "Is Elimination Stage"
        Number Min_Score_Required__c "Minimum Score Required"
        Lookup Created_By__c FK "Created By (User)"
        DateTime Created_Date__c "Created Date"
        Lookup Last_Modified_By__c FK "Last Modified By (User)"
        DateTime Last_Modified_Date__c "Last Modified Date"
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
    Job__c ||--o{ ATS_Stage__c : "has stages (1:N)"
    Job__c }o--|| ATS_Template__c : "uses template (N:1)"
    Job__c }o--|| Account : "belongs to (N:1)"
    Job__c }o--|| Contact : "hiring manager (N:1)"
    Job__c }o--|| User : "delivery lead (N:1)"
    Job__c }o--|| User : "sales lead (N:1)"
    Job__c }o--|| User : "created by (N:1)"
    Job__c }o--|| User : "modified by (N:1)"
    Job__c }o--|| Opportunity : "related to (N:1)"
    ATS_Template__c }o--|| User : "created by (N:1)"
    ATS_Template__c }o--|| User : "modified by (N:1)"
    ATS_Stage__c }o--|| User : "created by (N:1)"
    ATS_Stage__c }o--|| User : "modified by (N:1)"
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
- **MultiSelectPicklist**: Multi-select picklist field
- **Percent**: Percentage field

### Object Type Color Coding
- **Job__c**: Primary business object (darker shade)
- **ATS_Template__c**: Configuration/Template object (medium shade)
- **ATS_Stage__c**: Child/Detail object (lighter shade)
- **Account/Contact/Opportunity/User**: Standard Salesforce objects (lightest shade)

## Key Relationships

1. **Job__c to ATS_Stage__c** (1:N Master-Detail)
   - A Job can have multiple stages
   - Stages cannot exist without a parent Job
   - Cascade delete applies

2. **Job__c to ATS_Template__c** (N:1 Lookup)
   - Multiple Jobs can use the same template
   - Template defines the standard process for jobs

3. **Job__c to Account** (N:1 Lookup)
   - Multiple Jobs can belong to one Account
   - Represents the client/company relationship

4. **Job__c to Contact** (N:1 Lookup via Hiring_Manager__c)
   - Each Job has a Hiring Manager who is a Contact
   - Represents the person responsible for hiring decisions

5. **Job__c to User** (Multiple N:1 Lookups)
   - Delivery_Lead__c: User responsible for delivery
   - Sales_Lead__c: User responsible for sales
   - Created_By__c/Last_Modified_By__c: Audit fields

6. **Job__c to Opportunity** (N:1 Lookup)
   - Jobs can be linked to sales opportunities
   - Tracks revenue potential and sales pipeline

7. **Audit Fields**
   - All custom objects track Created By/Date and Last Modified By/Date
   - These link to the User object for audit trail

## Business Logic Notes

1. **ATS_Stage__c** records are automatically created when a Job__c is created, based on the selected ATS_Template__c
2. The **Sequence__c** field in ATS_Stage__c determines the order of stages in the hiring process
3. **Is_Active__c** fields allow for soft deletion and template versioning
4. **Filter_Criteria__c** in ATS_Template__c likely contains JSON or structured text for dynamic filtering
5. Stage progression is tracked through Status__c fields and date stamps