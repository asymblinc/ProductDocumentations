# ATS Package - Action and Template Configuration ERD

## Entity Relationship Diagram

```mermaid
erDiagram
    %% Color coding for different object types
    ATS_Action__c {
        string Id PK
        string Name
        string Component_Name__c
        string Flow_Name__c
        string Type__c "Picklist: Flow/Component/Email"
        string Email_Template__c
        boolean Is_Active__c
        string Description__c
        string Icon__c
        string Label__c
        string Parameters__c "Long Text Area"
        string Permissions__c "Text Area"
        string Record_Type__c
        string Target_Object__c
        datetime CreatedDate
        datetime LastModifiedDate
        string CreatedById
        string LastModifiedById
    }

    ATS_Action_Link__c {
        string Id PK
        string Name
        string ATS_Action__c FK "Lookup to ATS_Action__c"
        string ATS_Stage__c FK "Lookup to ATS_Stage__c"
        string ATS_Template__c FK "Lookup to ATS_Template__c"
        string ATS_Template_Stage__c FK "Lookup to ATS_Template_Stage__c"
        number Sequence__c
        boolean Is_Active__c
        string Condition_Logic__c "Text Area"
        string Display_Condition__c "Formula"
        boolean Is_Required__c
        string Button_Label__c
        string Button_Style__c
        datetime CreatedDate
        datetime LastModifiedDate
        string CreatedById
        string LastModifiedById
    }

    ATS_Template_Stage__c {
        string Id PK
        string Name
        string ATS_Template__c FK "Master-Detail to ATS_Template__c"
        number Sequence__c
        string Stage_Type__c "Picklist"
        string Stage_Name__c
        string Stage_Label__c
        string Description__c "Text Area"
        boolean Is_Active__c
        boolean Is_Required__c
        string Completion_Criteria__c "Formula"
        string Display_Condition__c "Formula"
        number Days_To_Complete__c
        string Icon__c
        string Color__c
        string Help_Text__c "Text Area"
        datetime CreatedDate
        datetime LastModifiedDate
        string CreatedById
        string LastModifiedById
    }

    ATS_Template_Filter__c {
        string Id PK
        string Name
        string ATS_Template__c FK "Master-Detail to ATS_Template__c"
        string Object_Name__c
        string Operator__c "Picklist"
        string Target_Field__c
        string Value__c
        string Field_Type__c "Picklist"
        number Filter_Order__c
        boolean Is_Active__c
        string Logic_Group__c
        string Parent_Field_Path__c
        string Related_Object__c
        string Filter_Logic__c "Formula"
        datetime CreatedDate
        datetime LastModifiedDate
        string CreatedById
        string LastModifiedById
    }

    ATS_Template__c {
        string Id PK
        string Name
        string Object_Name__c
        string Template_Type__c "Picklist"
        boolean Is_Active__c
        string Description__c "Text Area"
        string Category__c
        string Icon__c
        number Version__c
        string Status__c "Picklist"
        date Effective_Date__c
        date End_Date__c
        string Owner_Field__c
        string Parent_Template__c FK "Lookup to ATS_Template__c"
        datetime CreatedDate
        datetime LastModifiedDate
        string CreatedById
        string LastModifiedById
    }

    ATS_Stage__c {
        string Id PK
        string Name
        string Stage_Name__c
        string Object_Name__c
        string Stage_Type__c "Picklist"
        boolean Is_Active__c
        string Description__c "Text Area"
        number Default_Sequence__c
        string Icon__c
        string Color__c
        datetime CreatedDate
        datetime LastModifiedDate
        string CreatedById
        string LastModifiedById
    }

    %% Relationships with cardinality
    ATS_Action__c ||--o{ ATS_Action_Link__c : "has many"
    ATS_Stage__c ||--o{ ATS_Action_Link__c : "linked to"
    ATS_Template__c ||--o{ ATS_Action_Link__c : "contains"
    ATS_Template_Stage__c ||--o{ ATS_Action_Link__c : "associated with"
    
    ATS_Template__c ||--|{ ATS_Template_Stage__c : "has stages (Master-Detail)"
    ATS_Template__c ||--|{ ATS_Template_Filter__c : "has filters (Master-Detail)"
    ATS_Template__c ||--o{ ATS_Template__c : "parent template (Self-Lookup)"

    %% Additional relationships
    ATS_Stage__c ||--o{ ATS_Template_Stage__c : "references"

    %% Styling
    classDef actionClass fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef linkClass fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef templateClass fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px
    classDef stageClass fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef filterClass fill:#fce4ec,stroke:#880e4f,stroke-width:2px

    class ATS_Action__c actionClass
    class ATS_Action_Link__c linkClass
    class ATS_Template__c,ATS_Template_Stage__c templateClass
    class ATS_Stage__c stageClass
    class ATS_Template_Filter__c filterClass
```

## Object Descriptions

### ATS_Action__c (Actions)
- **Purpose**: Stores reusable actions that can be triggered at different stages
- **Key Fields**:
  - `Type__c`: Determines the action type (Flow, Component, or Email)
  - `Component_Name__c`: Lightning component name for Component type actions
  - `Flow_Name__c`: Flow API name for Flow type actions
  - `Email_Template__c`: Email template ID for Email type actions
  - `Parameters__c`: JSON parameters passed to the action

### ATS_Action_Link__c (Action Links)
- **Purpose**: Junction object that links actions to stages and templates
- **Key Relationships**:
  - Links to `ATS_Action__c` (the action to execute)
  - Links to `ATS_Stage__c` (generic stage reference)
  - Links to `ATS_Template__c` (template context)
  - Links to `ATS_Template_Stage__c` (specific template stage)
- **Key Fields**:
  - `Sequence__c`: Order of execution when multiple actions exist
  - `Display_Condition__c`: Formula to determine visibility
  - `Is_Required__c`: Whether the action must be completed

### ATS_Template_Stage__c (Template Stages)
- **Purpose**: Defines stages within a template with Master-Detail to ATS_Template__c
- **Key Fields**:
  - `Sequence__c`: Order of stages in the template
  - `Stage_Type__c`: Type of stage (e.g., Approval, Review, Action)
  - `Completion_Criteria__c`: Formula to determine when stage is complete
  - `Days_To_Complete__c`: SLA for stage completion

### ATS_Template_Filter__c (Template Filters)
- **Purpose**: Defines filtering criteria for template applicability
- **Key Fields**:
  - `Object_Name__c`: Target object for the filter
  - `Target_Field__c`: Field to evaluate
  - `Operator__c`: Comparison operator (equals, not equals, contains, etc.)
  - `Value__c`: Value to compare against
  - `Filter_Order__c`: Order of filter evaluation

### ATS_Template__c (Templates)
- **Purpose**: Master template object that defines the overall process
- **Key Fields**:
  - `Object_Name__c`: Primary object the template applies to
  - `Template_Type__c`: Type of template
  - `Parent_Template__c`: Self-lookup for template inheritance
  - `Version__c`: Template version number

### ATS_Stage__c (Stages)
- **Purpose**: Generic stage definitions that can be reused across templates
- **Key Fields**:
  - `Stage_Name__c`: Unique identifier for the stage
  - `Object_Name__c`: Object this stage applies to
  - `Default_Sequence__c`: Default ordering when added to templates

## Relationship Summary

1. **Templates to Stages**: One-to-Many (Master-Detail)
   - A template has multiple stages defined through `ATS_Template_Stage__c`

2. **Templates to Filters**: One-to-Many (Master-Detail)
   - A template has multiple filters to determine applicability

3. **Actions to Templates/Stages**: Many-to-Many (via Action Links)
   - Actions can be reused across different templates and stages
   - The `ATS_Action_Link__c` junction object manages these relationships

4. **Stage Reusability**:
   - `ATS_Stage__c` provides reusable stage definitions
   - `ATS_Template_Stage__c` creates template-specific stage instances

## Color Coding Legend
- 🔵 **Blue**: Action objects (ATS_Action__c)
- 🟣 **Purple**: Link/Junction objects (ATS_Action_Link__c)
- 🟢 **Green**: Template objects (ATS_Template__c, ATS_Template_Stage__c)
- 🟠 **Orange**: Stage objects (ATS_Stage__c)
- 🔴 **Pink**: Filter objects (ATS_Template_Filter__c)