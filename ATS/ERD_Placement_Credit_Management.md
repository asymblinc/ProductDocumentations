# Placement and Credit Management ERD

## Entity Relationship Diagram

```mermaid
erDiagram
    %% Color coding for different object types
    Placement__c {
        Id id PK
        string Name
        reference Account__c FK "Account"
        reference Contact__c FK "Contact (Candidate)"
        reference Job__c FK "Job"
        date Start_Date__c
        date End_Date__c
        currency Bill_Rate__c
        currency Pay_Rate__c
        picklist Status__c "Active/Inactive/On Hold/Completed"
        percent GP_Percentage__c
        currency GP_Amount__c
        textarea Notes__c
        boolean Credit_Allocated__c
        datetime Credit_Allocation_Date__c
        reference Owner FK "User"
        datetime CreatedDate
        datetime LastModifiedDate
        reference CreatedById FK "User"
        reference LastModifiedById FK "User"
    }

    Placement_Credit__c {
        Id id PK
        string Name
        reference Placement__c FK "Placement"
        reference User__c FK "User (Credit Recipient)"
        percent Credit_Percentage__c
        currency Credit_Amount__c
        picklist Credit_Type__c "Sales/Recruiting/Delivery/Support"
        textarea Notes__c
        boolean Is_Primary__c
        date Effective_Date__c
        date End_Date__c
        picklist Status__c "Active/Inactive"
        reference Created_From_Template__c FK "Placement_Credit_Template__c"
        reference Owner FK "User"
        datetime CreatedDate
        datetime LastModifiedDate
        reference CreatedById FK "User"
        reference LastModifiedById FK "User"
    }

    Placement_Credit_Template__c {
        Id id PK
        string Name
        string Template_Name__c
        textarea Description__c
        boolean Is_Active__c
        boolean Is_Default__c
        picklist Template_Type__c "Standard/Custom/Department"
        reference Department__c FK "Custom Object"
        reference Owner FK "User"
        datetime CreatedDate
        datetime LastModifiedDate
        reference CreatedById FK "User"
        reference LastModifiedById FK "User"
    }

    Placement_Credit_Template_Entry__c {
        Id id PK
        string Name
        reference Placement_Credit_Template__c FK "Placement_Credit_Template__c"
        reference User__c FK "User"
        reference Role__c FK "UserRole"
        percent Credit_Percentage__c
        picklist Credit_Type__c "Sales/Recruiting/Delivery/Support"
        boolean Is_Primary__c
        number Sort_Order__c
        boolean Is_Active__c
        reference Owner FK "User"
        datetime CreatedDate
        datetime LastModifiedDate
        reference CreatedById FK "User"
        reference LastModifiedById FK "User"
    }

    Account__c {
        Id id PK
        string Name
        %% Other standard Account fields
    }

    Contact__c {
        Id id PK
        string Name
        %% Other standard Contact fields
    }

    Job__c {
        Id id PK
        string Name
        %% Other Job fields
    }

    User {
        Id id PK
        string Name
        %% Other standard User fields
    }

    UserRole {
        Id id PK
        string Name
        %% Other standard UserRole fields
    }

    %% Relationships
    Placement__c ||--o{ Placement_Credit__c : "has many"
    Placement__c }o--|| Account__c : "belongs to"
    Placement__c }o--|| Contact__c : "for candidate"
    Placement__c }o--|| Job__c : "fills"
    Placement__c }o--|| User : "owned by"
    
    Placement_Credit__c }o--|| User : "assigned to"
    Placement_Credit__c }o--o| Placement_Credit_Template__c : "created from"
    Placement_Credit__c }o--|| User : "owned by"
    
    Placement_Credit_Template__c ||--o{ Placement_Credit_Template_Entry__c : "contains"
    Placement_Credit_Template__c }o--|| User : "owned by"
    
    Placement_Credit_Template_Entry__c }o--o| User : "for user"
    Placement_Credit_Template_Entry__c }o--o| UserRole : "for role"
    Placement_Credit_Template_Entry__c }o--|| User : "owned by"

    %% Styling
    classDef placement fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef credit fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef template fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef external fill:#f5f5f5,stroke:#616161,stroke-width:1px
    
    class Placement__c placement
    class Placement_Credit__c credit
    class Placement_Credit_Template__c,Placement_Credit_Template_Entry__c template
    class Account__c,Contact__c,Job__c,User,UserRole external
```

## Credit Allocation Flow

```mermaid
flowchart TB
    Start([New Placement Created]) --> Check{Credit Template<br/>Selected?}
    
    Check -->|Yes| Template[Load Template Entries]
    Check -->|No| Manual[Manual Credit Entry]
    
    Template --> CreateCredits[Create Placement Credits<br/>from Template Entries]
    Manual --> CreateSingle[Create Individual<br/>Placement Credit]
    
    CreateCredits --> Validate{Total = 100%?}
    CreateSingle --> Validate
    
    Validate -->|Yes| Allocate[Mark Credit Allocated<br/>Set Allocation Date]
    Validate -->|No| Error[Show Validation Error]
    
    Allocate --> Calculate[Calculate Credit Amounts<br/>Based on GP]
    Calculate --> Save[Save All Records]
    Save --> End([Credit Allocation Complete])
    
    Error --> Fix[Fix Percentages]
    Fix --> Validate
    
    %% Styling
    classDef process fill:#e3f2fd,stroke:#1976d2,stroke-width:2px
    classDef decision fill:#fce4ec,stroke:#c2185b,stroke-width:2px
    classDef terminal fill:#e8f5e9,stroke:#388e3c,stroke-width:2px
    classDef error fill:#ffebee,stroke:#d32f2f,stroke-width:2px
    
    class Start,End terminal
    class Check,Validate decision
    class Template,Manual,CreateCredits,CreateSingle,Allocate,Calculate,Save,Fix process
    class Error error
```

## Object Descriptions

### Placement__c
- **Purpose**: Represents a candidate placement for a job at a client company
- **Key Features**:
  - Tracks placement details (dates, rates, status)
  - Calculates GP (Gross Profit) percentage and amount
  - Links to Account (client), Contact (candidate), and Job
  - Tracks credit allocation status and date

### Placement_Credit__c
- **Purpose**: Represents credit allocation for a specific user on a placement
- **Key Features**:
  - Assigns percentage or amount-based credit to users
  - Supports different credit types (Sales, Recruiting, Delivery, Support)
  - Can be created from templates or manually
  - Tracks effective and end dates for credit periods
  - Supports primary credit designation

### Placement_Credit_Template__c
- **Purpose**: Defines reusable credit allocation templates
- **Key Features**:
  - Creates standard credit allocation patterns
  - Can be department-specific or company-wide
  - Supports default template designation
  - Active/inactive status for template management

### Placement_Credit_Template_Entry__c
- **Purpose**: Individual entries within a credit template
- **Key Features**:
  - Defines credit percentage for specific users or roles
  - Supports role-based credit allocation
  - Maintains sort order for display purposes
  - Can be activated/deactivated independently

## Relationship Cardinality

- **Placement to Placement Credit**: One-to-Many
  - One placement can have multiple credit allocations
  - Each credit must belong to exactly one placement

- **Placement Credit Template to Template Entries**: One-to-Many
  - One template contains multiple entries
  - Each entry belongs to exactly one template

- **Placement Credit to Template**: Many-to-One (Optional)
  - Credits can be created from a template
  - Credits can also be created manually without a template

## Business Rules

1. **Credit Allocation Validation**:
   - Total credit percentages for a placement should equal 100%
   - Primary credit designation should be unique per placement

2. **Template Usage**:
   - Only active templates can be used for new credit allocations
   - Default templates are automatically suggested for new placements

3. **Credit Types**:
   - Each credit entry must have a valid credit type
   - Credit types determine reporting and commission calculations

4. **Temporal Validity**:
   - Credits can have effective and end dates
   - Only active credits within valid date ranges are considered for calculations