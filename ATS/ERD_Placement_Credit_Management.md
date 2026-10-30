# Placement, Credit, and Target Management ERD

> **Revision note (2026-10-30)**: Rebuilt against real field metadata. The prior revision used generic/plausible-sounding but fabricated fields on every object in this file (`Placement__c.GP_Percentage__c`/`GP_Amount__c`/`Credit_Allocated__c`, `Placement_Credit__c.Credit_Amount__c`/`Is_Primary__c`, `Placement_Credit_Template__c.Template_Type__c`/`Department__c`, `Placement_Credit_Template_Entry__c.Role__c`/`Sort_Order__c`) — none of these exist. It also entirely omitted the new **Target / Target Template** KPI domain, which has no placement-credit overlap in the real schema but is documented here alongside it since both are compensation/performance-adjacent and were introduced together in the same package revision cycle.

## Entity Relationship Diagram

```mermaid
erDiagram
    %% Color coding for different object types
    Placement__c {
        Id id PK
        Lookup Account__c FK "Account (client)"
        Lookup ATS_Candidate__c FK "Contact (placed candidate)"
        Lookup ATS_Job__c FK "Job"
        Lookup Branch__c FK "Branch"
        Lookup Placement_Credit_Template__c FK "Credit Template"
        Lookup Timesheet_Approver__c FK "User"
        Lookup Timesheet_Delegated_Approver__c FK "User"
        Lookup Work_Site_Location__c FK "Work Site Location"
        Checkbox Assignment_Extended__c
        Currency Bill_Rate__c
        Currency Burden_Amount__c
        Percent Burden_Percentage__c
        Percent Conversion_Percentage__c
        Date Estimated_End_Date__c
        Checkbox Is_Both_Profit_Populated__c
        Currency Pay_Rate__c
        Text Pay_Rule_Country_Code__c
        Text Pay_Rule_State_Code__c
        Checkbox Placement_Complete__c
        Date Placement_Completed_On__c
        Percent Placement_Percentage__c
        Currency Salary__c
        Date Start_Date__c
        Currency Total_Billable_Amount__c
        Currency Total_Gross_Profit__c
        Currency Total_Payable_Amount__c
    }

    Placement_Credit__c {
        Id id PK
        MasterDetail Placement__c FK "Placement (Master-Detail)"
        Lookup ATS_Branch_Receiving_Credit__c FK "Branch"
        Lookup Placement_Credit_Template_Entry__c FK "Template Entry"
        Lookup User__c FK "User (Credit Recipient)"
        Text ATS_Role_Type__c
        Percent Credit_Percentage__c
        Text Credit_Recipient__c
        Checkbox Is_Void__c
        Checkbox Manual_Override__c
        Currency Profit_Amount__c
        Currency Revenue_Amount__c
        Number Total_Credits__c
        Picklist Type__c
    }

    Placement_Credit_Template__c {
        Id id PK
        Lookup Placement__c FK "Placement"
        Text Placement_Credit_Template__c "Template name"
        Checkbox Active__c
        Date Effective_Date__c
        Date End_Date__c
        LongTextArea Filter_Criteria__c
        Number Priority__c
        Picklist Status__c
        Picklist Type__c
        Checkbox Void__c
    }

    Placement_Credit_Template_Entry__c {
        Id id PK
        MasterDetail Placement_Credit_Template__c FK "Template (Master-Detail)"
        Lookup ATS_Branch_Receiving_Credit__c FK "Branch"
        Lookup User_Receiving_Credit__c FK "User"
        Picklist ATS_Placement_Credit_Name__c
        Percent Credit_Percentage__c
        Picklist Type__c
    }

    Target_Template__c {
        Id id PK
        Lookup Asymbl_Assigned_Role__c FK "UserRole"
        Lookup Asymbl_Assigned_User__c FK "User"
        Checkbox Asymbl_Active__c
        Picklist Asymbl_Applies_To__c
        LongTextArea Asymbl_Assignment_Criteria__c
        Picklist Asymbl_Assignment_Type__c
        Checkbox Asymbl_Auto_Rollover__c
        LongTextArea Asymbl_Filter_Criteria__c
        Picklist Asymbl_Metric_Type__c
        Picklist Asymbl_Period_Type__c
        Number Asymbl_Priority__c
        Number Asymbl_Target_Value__c
        Picklist Asymbl_Threshold_Type__c
        Number Asymbl_Window_Days__c
    }

    Target_Template_Entry__c {
        Id id PK
        MasterDetail Asymbl_Target_Template__c FK "Target Template (Master-Detail)"
        Checkbox Asymbl_Active__c
        Picklist Asymbl_Metric_Type__c
        Number Asymbl_Sequence__c
        Number Asymbl_Target_Value__c
        Picklist Asymbl_Threshold_Type__c
        Number Asymbl_Window_Days__c
    }

    Target__c {
        Id id PK
        Lookup Asymbl_Job__c FK "Job"
        Lookup Asymbl_Set_By__c FK "User"
        Lookup Asymbl_Template__c FK "Target Template"
        Lookup Asymbl_User__c FK "User (assignee)"
        Checkbox Asymbl_Active__c
        Picklist Asymbl_Metric_Type__c
        Date Asymbl_Period_Start__c
        Date Asymbl_Period_End__c
        Picklist Asymbl_Period_Type__c
        Number Asymbl_Target_Value__c
        Picklist Asymbl_Threshold_Type__c
        Picklist Asymbl_Values_Source__c
    }

    Account {
        Id id PK
        Text Name
    }

    Contact {
        Id id PK
        Text Name
    }

    Job__c {
        Id id PK
        Text Job_Title__c
    }

    Branch__c {
        Id id PK
        Text Name
    }

    User {
        Id id PK
        Text Name
    }

    UserRole {
        Id id PK
        Text Name
    }

    %% Relationships
    Placement__c ||--o{ Placement_Credit__c : "has many (Master-Detail)"
    Placement__c }o--|| Account : "belongs to"
    Placement__c }o--|| Contact : "for candidate"
    Placement__c }o--|| Job__c : "fills"
    Placement__c }o--o| Branch__c : "branch"
    Placement__c }o--o| Placement_Credit_Template__c : "uses template"

    Placement_Credit__c }o--|| User : "assigned to"
    Placement_Credit__c }o--o| Placement_Credit_Template_Entry__c : "created from entry"
    Placement_Credit__c }o--o| Branch__c : "branch receiving credit"

    Placement_Credit_Template__c ||--o{ Placement_Credit_Template_Entry__c : "contains (Master-Detail)"
    Placement_Credit_Template__c }o--o| Placement__c : "default for placement"

    Placement_Credit_Template_Entry__c }o--o| User : "for user"
    Placement_Credit_Template_Entry__c }o--o| Branch__c : "for branch"

    Target_Template__c ||--o{ Target_Template_Entry__c : "has entries (Master-Detail)"
    Target_Template__c }o--o| UserRole : "assigned role"
    Target_Template__c }o--o| User : "assigned user"
    Target_Template__c ||--o{ Target__c : "generates"

    Target__c }o--|| User : "assigned to"
    Target__c }o--o| Job__c : "for job"
    Target__c }o--|| User : "set by"
```

## Credit Allocation Flow

```mermaid
flowchart TB
    Start([New Placement Created]) --> Check{Credit Template<br/>Selected?}

    Check -->|Yes| Template[Load Template Entries]
    Check -->|No| Manual[Manual Credit Entry]

    Template --> CreateCredits[Create Placement Credits<br/>from Template Entries]
    Manual --> CreateSingle[Create Individual<br/>Placement Credit]

    CreateCredits --> Calc[Calculate Profit/Revenue Amounts<br/>via ATS_PlacementCreditTrigger]
    CreateSingle --> Calc

    Calc --> Save[Save All Records]
    Save --> End([Credit Allocation Complete])

    %% Styling
    classDef process fill:#e3f2fd,stroke:#1976d2,stroke-width:2px
    classDef decision fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    classDef terminal fill:#e8f5e9,stroke:#388e3c,stroke-width:2px

    class Start,End terminal
    class Check decision
    class Template,Manual,CreateCredits,CreateSingle,Calc,Save process
```

> **Note**: the prior revision's flow diagram depicted a `Total = 100%?` validation gate and a `Credit_Allocated__c`/`Credit_Allocation_Date__c` completion step. Neither of those fields exists on `Placement__c` or `Placement_Credit__c` in the current schema; percentage totals are not formally validated by a dedicated field in this object model. This diagram has been corrected to reflect the actual trigger-driven calculation flow (`ATS_PlacementCreditTrigger`/`ATS_PlacementCreditTriggerHandler`).

## Target Assignment Flow (new domain)

```mermaid
flowchart TB
    Start([Target Template Created]) --> Assign{Assignment Type?}
    Assign -->|Role-based| Role[Resolve Users via Asymbl_Assigned_Role__c]
    Assign -->|User-based| DirectUser[Use Asymbl_Assigned_User__c]
    Assign -->|Criteria-based| Criteria[Evaluate Asymbl_Assignment_Criteria__c]

    Role --> Materialize[Create Target__c per User/Period]
    DirectUser --> Materialize
    Criteria --> Materialize

    Materialize --> JobAuto{New Job Matches<br/>Auto-Apply Rule?}
    JobAuto -->|Yes| AutoApply[ATS_JobTargetAutoApply<br/>creates job-scoped Target__c]
    JobAuto -->|No| Skip[No Job-Scoped Target]

    Materialize --> PeriodEnd{Period Ends &<br/>Auto_Rollover__c = true?}
    PeriodEnd -->|Yes| Rollover[ATS_TargetRolloverSchedulable<br/>creates next-period Target__c]
    PeriodEnd -->|No| Close[Target Period Closes]

    classDef process fill:#ede7f6,stroke:#5e35b1,stroke-width:2px
    classDef decision fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    classDef terminal fill:#e8f5e9,stroke:#388e3c,stroke-width:2px

    class Start,Skip,Close terminal
    class Assign,JobAuto,PeriodEnd decision
    class Role,DirectUser,Criteria,Materialize,AutoApply,Rollover process
```

## Object Descriptions

### Placement__c
- **Purpose**: Represents a candidate placement for a job at a client company
- **Key Features**: tracks placement dates/rates/status, burden amount/percentage, gross profit totals (`Total_Gross_Profit__c`), timesheet approver chain, and links to Account (client), Contact (candidate via `ATS_Candidate__c`), and Job (via `ATS_Job__c`)

### Placement_Credit__c
- **Purpose**: Represents credit allocation for a specific user on a placement
- **Key Features**: percentage- and amount-based credit (`Credit_Percentage__c`, `Profit_Amount__c`, `Revenue_Amount__c`), can be created from a `Placement_Credit_Template_Entry__c` or manually, supports voiding (`Is_Void__c`) and manual override

### Placement_Credit_Template__c / Placement_Credit_Template_Entry__c
- **Purpose**: Defines reusable credit allocation templates and their per-recipient entries
- **Key Features**: active/void/status lifecycle, filter criteria for automatic template selection, entries assign a named credit role (`ATS_Placement_Credit_Name__c`) to a specific user and/or branch

### Target_Template__c / Target_Template_Entry__c / Target__c *(new domain)*
- **Purpose**: Defines and materializes recruiter/team KPI goals
- **Key Features**:
  - `Target_Template__c` defines the metric type, period cadence, assignment rule (by role, user, or filter criteria), and whether unmet targets auto-roll into the next period
  - `Target_Template_Entry__c` defines graduated threshold/value breakpoints within a template
  - `Target__c` is the materialized, assigned instance for a specific user and period, optionally scoped to a specific job (auto-applied via `ATS_JobTargetAutoApply` when a new job matches a template's criteria)

## Relationship Cardinality

- **Placement to Placement Credit**: One-to-Many (Master-Detail) — one placement can have multiple credit allocations; each credit belongs to exactly one placement
- **Placement Credit Template to Template Entries**: One-to-Many (Master-Detail)
- **Placement Credit to Template Entry**: Many-to-One (Optional) — credits can be created from a template entry or manually
- **Target Template to Target Template Entry**: One-to-Many (Master-Detail)
- **Target Template to Target**: One-to-Many — a template can generate many materialized Target records across users/periods

## Business Rules

1. **Credit Calculation**: Profit/Revenue amounts on `Placement_Credit__c` are calculated via `ATS_PlacementCreditTriggerHandler`, not a dedicated validation field
2. **Template Usage**: `Active__c`/`Void__c` on `Placement_Credit_Template__c` control whether a template is eligible for new placements
3. **Credit Types**: `Type__c` on both `Placement_Credit__c` and `Placement_Credit_Template_Entry__c` determines reporting and commission category
4. **Target Rollover**: `Asymbl_Auto_Rollover__c` on `Target_Template__c` drives `ATS_TargetRolloverSchedulable`, which creates the next period's `Target__c` automatically for templates that opt in
5. **Target Auto-Apply**: `ATS_JobTargetAutoApply` evaluates new `Job__c` records against active Target Template assignment criteria to automatically create job-scoped targets
