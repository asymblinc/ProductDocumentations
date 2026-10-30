# Asymbl Time Package - Core Time & Attendance ERD

## Overview

This document provides an Entity Relationship Diagram (ERD) for the core Time & Attendance objects in the Asymbl Time package. The three main objects are:

1. **Timesheet__c** - Main timesheet object that aggregates time entries for a pay period
2. **Time_Entry__c** - Individual time entries recording daily work hours (Master-Detail child of Timesheet__c)
3. **Alternative_Time__c** - Time off, holidays, and other non-working time entries (Master-Detail child of Timesheet__c)

All field names and relationship types below were verified directly against `force-app/main/default/objects/*/fields/*.field-meta.xml` on the `master` branch (commit `38a8d3eb`, 2026-10-30).

## Entity Relationship Diagram

```mermaid
erDiagram
    Timesheet__c {
        string Status__c "New/Draft/Submitted for Approval/Approved by Branch/Approved/Rejected/Void"
        date Pay_Period_Start_Date__c
        date Pay_Period_End_Date__c
        lookup Candidate__c "Contact"
        lookup Candidate_Rate_Card__c
        lookup Pay_Rule__c
        lookup Timesheet_Approver__c "Contact"
        lookup Timesheet_Delegated_Approver__c "Contact"
        lookup Approved_Rejected_By__c
        lookup Branch_Approver__c
        boolean Approved_By_Branch__c
        datetime Branch_Approved_On__c
        string Rejection_Reason__c
        text Rejection_History__c
        string Timesheet_Type__c
        currency Total_Bill__c
        currency Total_Pay__c
        currency Total_Candidate_Pay__c
        currency Total_Billable_Amount__c
        currency Total_Adjusted_Bill__c
        currency Total_Adjusted_Pay__c
        number Regular_Hours__c
        number Overtime_Hours__c
        number Double_Time_Hours__c
        currency Bill_Rate__c
        currency Pay_Rate__c
        currency Overtime_Bill_Rate__c
        currency Overtime_Pay_Rate__c
        currency Double_Time_Bill_Rate__c
        currency Double_Time_Pay_Rate__c
        percent Burden__c
        currency Gross_Profit__c
        currency Adjusted_Gross_Profit__c
        percent Gross_Margin_New__c
        percent Adjusted_Gross_Margin__c
        number Total_Hours_Logged__c
        number Total_Units_Worked__c
        number Total_Days_Worked__c
        number Number_of_Adjustments__c
        text Comments__c
        datetime AST_Submitted_On__c
        datetime AST_Approved_On__c
        string Prior_Timesheet_Status__c
        boolean Voided__c
        lookup Original_Timesheet__c "Timesheet__c (self)"
    }

    Time_Entry__c {
        date Date__c
        time Start_Time__c
        time End_Time__c
        time Secondary_Start_Time__c
        time Secondary_End_Time__c
        number Break__c
        boolean Is_Holiday_Entry__c
        boolean Did_Not_Work__c
        boolean Worked_on_this_Day__c
        masterdetail Timesheet__c
        lookup Candidate_Rate_Card__c
        lookup Candidate_Rate_Card_Detail__c
        number Regular_Hours__c
        number Overtime_Hours__c
        number Double_Time_Hours__c
        currency Bill_Rate__c
        currency Pay_Rate__c
        currency Overtime_Bill_Rate__c
        currency Overtime_Pay_Rate__c
        currency Double_Time_Bill_Rate__c
        currency Double_Time_Pay_Rate__c
        currency Regular_Pay__c
        currency Overtime_Pay__c
        currency Double_Time_Pay__c
        currency Regular_Billable_Amount__c
        currency Overtime_Billable_Amount__c
        currency Double_Time_Billable_Amount__c
        number Total_Hours_Worked__c
        number Total_Hrs__c
        number Total_Units_Worked__c
        number Number_of_Days_Worked__c
        text Description__c
        boolean Is_Attested__c
        string Attestation_Type__c "All Breaks Taken/No Break - Voluntary/No Break - Involuntary"
        text Attestation_Comments__c
        boolean Is_Entry_Update_from_UI__c
        number AST_Sequence__c
        string AST_Unique_Key__c
        boolean AST_Updated_In_Resubmission__c
    }

    Alternative_Time__c {
        date Date__c
        time Start_Time__c
        time End_Time__c
        lookup Candidate__c "Contact"
        masterdetail Timesheet__c
        lookup Candidate_Rate_Card__c
        lookup Candidate_Rate_Card_Detail__c
        boolean Is_Holiday_Entry__c
        boolean Is_Billable__c
        boolean Is_Payable__c
        string Status__c "Draft/Submitted/Approved/Rejected"
        text Description__c
        text Rejection_Reason__c
        currency Bill_Rate__c
        currency Pay_Rate__c
        currency Billable_Amount__c
        currency Payable_Amount__c
        number Total_Hours__c
        number Total_Time_Off_Hours__c
        boolean Is_Attested__c
        string Attestation_Type__c
        text Attestation_Comments__c
        number AST_Sequence__c
        string AST_Unique_Key__c
        boolean AST_Updated_In_Resubmission__c
    }

    Candidate_Rate_Card__c {
        date Start_Date__c
        date End_Date__c
        string Status__c "FORMULA: Active/Expired/Upcoming/Void"
        boolean Void__c
        boolean Manual_Override__c
        string Rate_Card_Type__c
        string Rate_Card_Detail_Type__c
        currency Pay_Rate__c
        currency Bill_Rate__c
        currency OT_Pay_Rate__c
        currency OT_Bill_Rate__c
        currency DT_Pay_Rate__c
        currency DT_Bill_Rate__c
        percent Burden__c
        currency Burden_Amount__c
        lookup Rate_Card_Template__c
    }

    Pay_Rule__c {
        string Country_Code__c
        string State_Code__c
        string Type__c "Weekly/Weekly + Daily/Weekly + Daily (Double Time)"
        number Daily_Limit__c
        number Weekly_Limit__c
        number Double_Time_Start__c
        string Seventh_Day__c "No/Overtime/Double Time/Both"
        number Seventh_Day_Double_Time_Start__c
        boolean Enable_Attestation__c
    }

    Contact {
        string Employment_Classification__c "Exempt/Non-Exempt"
    }

    Rate_Card_Template__c {
        string Rate_Card_Type__c
        boolean Active__c
        boolean Is_Default__c
    }

    %% Relationships
    Timesheet__c ||--o{ Time_Entry__c : "contains (Master-Detail)"
    Timesheet__c ||--o{ Alternative_Time__c : "contains (Master-Detail)"
    Timesheet__c }o--o| Contact : "Candidate__c"
    Timesheet__c }o--o| Contact : "Timesheet_Approver__c"
    Timesheet__c }o--o| Contact : "Timesheet_Delegated_Approver__c"
    Timesheet__c }o--o| Candidate_Rate_Card__c : "Candidate_Rate_Card__c"
    Timesheet__c }o--o| Pay_Rule__c : "Pay_Rule__c"
    Time_Entry__c }o--o| Candidate_Rate_Card__c : "Candidate_Rate_Card__c"
    Alternative_Time__c }o--o| Contact : "Candidate__c"
    Alternative_Time__c }o--o| Candidate_Rate_Card__c : "Candidate_Rate_Card__c"
    Candidate_Rate_Card__c }o--o| Rate_Card_Template__c : "Rate_Card_Template__c (lookup, not MD)"
    Timesheet__c }o--o| Timesheet__c : "Original_Timesheet__c (self lookup)"

    %% Color coding
    classDef coreObject fill:#4A90E2,stroke:#333,stroke-width:2px,color:#fff
    classDef supportObject fill:#7ED321,stroke:#333,stroke-width:2px,color:#fff
    classDef externalObject fill:#F5A623,stroke:#333,stroke-width:2px,color:#fff

    class Timesheet__c,Time_Entry__c,Alternative_Time__c coreObject
    class Candidate_Rate_Card__c,Rate_Card_Template__c,Pay_Rule__c supportObject
    class Contact externalObject
```

## Object Descriptions

### 1. Timesheet__c (Core Object)
The main timesheet object representing a candidate's pay period. Aggregates all time entries and calculates totals for hours, pay, and billing. Sharing model is `Read` with Apex-managed sharing granting the candidate, approver, and delegated approver read/write access.

**Key Characteristics:**
- 7-value restricted `Status__c` picklist: `New`, `Draft`, `Submitted for Approval`, `Approved by Branch`, `Approved`, `Rejected`, `Void` (default `New`)
- Direct lookups to `Contact` for candidate, primary approver, and delegated approver
- Direct lookup to `Pay_Rule__c` — one Pay Rule governs OT/DT calculation for the whole timesheet
- Calculates Regular/Overtime/Double-Time hours and associated pay/bill amounts, including "Adjusted" variants reflecting `Timesheet_Adjustment__c` records
- Supports voiding via `Voided__c` + `Original_Timesheet__c` self-lookup

### 2. Time_Entry__c (Master-Detail child of Timesheet__c)
Individual daily time entries. **Cannot exist without a parent Timesheet** (sharing model `ControlledByParent`).

**Key Characteristics:**
- Primary and secondary start/end time windows (split-shift support)
- `Break__c` (number) for break duration
- Attestation fields: `Is_Attested__c`, `Attestation_Type__c`, `Attestation_Comments__c`
- `AST_Sequence__c` / `AST_Unique_Key__c` used for idempotent upsert during resubmission (`AST_Updated_In_Resubmission__c`)
- Links to `Candidate_Rate_Card__c` and, for time-based/location/shift rate cards, `Candidate_Rate_Card_Detail__c`

### 3. Alternative_Time__c (Master-Detail child of Timesheet__c)
Time off and holiday tracking, separate from regular work hours. **Cannot exist without a parent Timesheet**.

**Key Characteristics:**
- Own `Status__c` lifecycle independent of the Timesheet's status: `Draft`, `Submitted`, `Approved`, `Rejected` (no `New`/`Void` values here, unlike `Timesheet__c`)
- Direct lookup to `Contact` (`Candidate__c`) in addition to the parent Timesheet
- `Is_Billable__c` / `Is_Payable__c` flags with corresponding `Billable_Amount__c` / `Payable_Amount__c`
- Alternative time type values (`Alternative_Timesheet_Types` global value set) are `Sick`, `PTO`, `Holiday`

## Field Details

### Timesheet__c Key Fields

| Field | Type | Description |
|-------|------|-------------|
| Status__c | Picklist (restricted) | `New`, `Draft`, `Submitted for Approval`, `Approved by Branch`, `Approved`, `Rejected`, `Void` |
| Pay_Period_Start_Date__c / Pay_Period_End_Date__c | Date | Pay period boundaries |
| Candidate__c | Lookup(Contact) | The worker/candidate |
| Candidate_Rate_Card__c | Lookup(Candidate_Rate_Card__c) | Rate card applied to the whole timesheet |
| Pay_Rule__c | Lookup(Pay_Rule__c) | Governs OT/DT bucketing |
| Timesheet_Approver__c / Timesheet_Delegated_Approver__c | Lookup(Contact) | Primary / backup approver |
| Branch_Approver__c / Approved_By_Branch__c / Branch_Approved_On__c | Lookup / Checkbox / DateTime | Branch-level approval stage |
| Total_Hours_Logged__c | Number | Total hours across all entries |
| Total_Bill__c / Total_Pay__c / Total_Candidate_Pay__c | Currency | Billing/pay totals |
| Voided__c / Original_Timesheet__c | Checkbox / Lookup (self) | Voiding and replacement tracking |

### Time_Entry__c Key Fields

| Field | Type | Description |
|-------|------|-------------|
| Date__c | Date | Date of the time entry |
| Start_Time__c / End_Time__c | Time | Primary clock-in/out |
| Secondary_Start_Time__c / Secondary_End_Time__c | Time | Split-shift secondary window |
| Timesheet__c | Master-Detail(Timesheet__c) | Parent timesheet |
| Regular_Hours__c / Overtime_Hours__c / Double_Time_Hours__c | Number | Hour buckets |
| Bill_Rate__c / Pay_Rate__c | Currency | Rates applied to regular hours |
| Is_Attested__c / Attestation_Type__c | Checkbox / Picklist | Break attestation |

### Alternative_Time__c Key Fields

| Field | Type | Description |
|-------|------|-------------|
| Date__c | Date | Date of the time off/holiday |
| Candidate__c | Lookup(Contact) | The worker taking time off |
| Timesheet__c | Master-Detail(Timesheet__c) | Parent timesheet |
| Is_Holiday_Entry__c | Checkbox | Indicates if this is a holiday |
| Is_Billable__c / Is_Payable__c | Checkbox | Billing/pay eligibility |
| Status__c | Picklist | `Draft`, `Submitted`, `Approved`, `Rejected` |
| Total_Hours__c / Total_Time_Off_Hours__c | Number | Total hours of time off |

## Relationship Explanations

### 1. Timesheet to Time Entry (1:M — Master-Detail)
Time entries cannot exist without a parent timesheet. Deletion of a timesheet cascades to all related time entries.

### 2. Timesheet to Alternative Time (1:M — Master-Detail)
Alternative time is also master-detail (not a loose lookup as previously documented) — it maintains its own `Status__c` but is still deleted if the parent timesheet is deleted.

### 3. Timesheet to Contact (M:1 — Lookups)
Three separate lookups: `Candidate__c` (worker), `Timesheet_Approver__c` (primary approver), `Timesheet_Delegated_Approver__c` (backup approver). There is a fourth approval-related lookup, `Branch_Approver__c`, also to `Contact`.

### 4. Rate Card and Pay Rule Relationships
`Timesheet__c` references both a `Candidate_Rate_Card__c` and a `Pay_Rule__c` directly. `Time_Entry__c` and `Alternative_Time__c` also reference `Candidate_Rate_Card__c` (and its detail) directly — `Candidate_Rate_Card__c` itself has no lookup back to `Contact`; the candidate association flows through the Timesheet.

### 5. Timesheet Voiding Relationship
`Original_Timesheet__c` creates a self-referencing lookup used by `AST_VoidTimesheetController` to track voided/replacement timesheets.

## Business Rules and Constraints

1. **Status Workflow**: `New → Draft → Submitted for Approval → Approved by Branch → Approved`, with `Rejected` looping back to `Draft`/resubmission and `Void` as a terminal state reachable from `Approved`.
2. **OT/DT Calculation**: Performed entirely in `AST_TimeEntryTriggerHelper.updateHoursOnTimeEntries()` against the single `Pay_Rule__c` linked to the Timesheet (Daily limit, Weekly limit, Double Time start, 7th-day rule).
3. **Rate Card Validity**: `Candidate_Rate_Card__c.Status__c` is a formula (`Active`/`Expired`/`Upcoming`/`Void`) — there is no manual status field to set; validity is always date- and void-flag-derived.
4. **Sharing**: Apex-managed sharing (`Timesheet__Share`) grants candidate + both approvers read/write; `Time_Entry__c`/`Alternative_Time__c` inherit sharing from the parent Timesheet.
5. **Voiding**: Voided timesheets are tracked via `Voided__c` + `Original_Timesheet__c`. There is no validation rule on `Timesheet__c` itself enforcing immutability; the one relevant validation rule in this domain is `Time_Entry__c.Prevent_Update_When_Voided`, which blocks edits to a time entry once its timesheet has been voided. All 21 other package validation rules live on `Rate_Card_Template__c` / `Rate_Card_Template_Detail__c` (rate range/markup integrity checks — see the Rate Card Management ERD).

This ERD represents the core time tracking functionality of the Asymbl Time package as implemented on `master` as of 2026-10-30.
