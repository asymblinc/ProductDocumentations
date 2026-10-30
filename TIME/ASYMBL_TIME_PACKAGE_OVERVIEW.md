# Asymbl Time Package - Comprehensive Technical Overview

## Executive Summary

The Asymbl Time package (`ASYMBL_Time` namespace) is a Salesforce-native time and attendance management solution designed for staffing and contingent workforce management. It provides time tracking, rate card management, calendar/holiday and pay-rule driven overtime calculation, and approval workflows, and is designed to integrate with a configurable external ATS/CRM Placement and Job object (Bullhorn `bpats` is a soft-coded, detectable default — not a hard package dependency; see the Integration section below).

### Key Highlights
- **Purpose**: Time and attendance management for staffing organizations, built around a `Timesheet__c` per candidate per pay period
- **Platform**: Native Salesforce managed package (API version 58.0 per `sfdx-project.json`; `.claude/CLAUDE.md` references 55.0 as the historical baseline)
- **Architecture**: Handler/Helper trigger framework with Custom Metadata-driven field mapping for ATS/CRM integration
- **Scale**: Single default calendar/pay-period model per Account, extendable via Rate Card Templates with filter-based eligibility

## Component Statistics

Verified against `manifest/package.xml` on `master` (commit `38a8d3eb`, 2026-10-30):

| Component Type | Count | Description |
|----------------|-------|-------------|
| **ApexClass** | 135 | Business logic, controllers, batch jobs, and test classes |
| **ApexTrigger** | 8 | Object triggers for automation (Timesheet, Time Entry, Alternative Time, Candidate Rate Card, Candidate Rate Card Detail, Rate Card Template, Calendar, Community User) |
| **CustomObject** | 25 | 17 true custom objects + 7 Custom Metadata Types + 1 Platform Event |
| **LightningComponentBundle** | 53 | Modern UI components (desktop, mobile, admin) |
| **AuraDefinitionBundle** | 2 | Legacy Aura components (`astEligibleRateCardsOnJobContainer`, `astTimeEntryContainer`) |
| **CustomLabel** | 648 | Internationalization and configurable text |
| **CustomField** | 290 | Field extensions across all objects |
| **CustomMetadata** | 30 | Configuration records across 6 of the 7 metadata types (`AST_Generic_Setting__mdt` ships with 0 packaged records — populated post-install via `AST_PostInstaller`) |
| **GlobalValueSet** | 6 | Shared picklists (Pay Type, Bill Type, Timesheet Type, Alternative Timesheet Types, Attestation Type, Pay Period Days) |
| **ValidationRule** | 22 | Data integrity rules |
| **Flow** | 1 | `Generate_new_Timesheet` |
| **PermissionSet** | not separately packaged in `manifest/package.xml` under a dedicated `PermissionSet` type on `master`; access is configured via `AST_ConfigurePermissions` LWC + `AST_MetadataService` at install/runtime |

## Key Features and Capabilities

### 1. Time Tracking and Attendance

#### Core Functionality
- **Flexible Time Entry Interfaces** (`Timesheet_Interface__c` / `Timesheet_Secondary_Interface__c` on Rate Card Template): Start/End, Time In/Time Out, Total Hours, Unit, Daily, Start/End (Flexible), Total Hours (Flexible)
- Break time tracking (`Time_Entry__c.Break__c`)
- Secondary start/end time window per entry (split shifts)
- Alternative Time entries for Sick, PTO, and Holiday (`Alternative_Timesheet_Types` global value set)
- Attestation support: `Is_Attested__c`, `Attestation_Type__c` (All Breaks Taken / No Break - Voluntary / No Break - Involuntary), `Attestation_Comments__c`

#### Automatic Calculations
- Regular/Overtime/Double-Time hour calculations performed in `AST_TimeEntryTriggerHelper.updateHoursOnTimeEntries()`
- Pay Rule types: Weekly, Weekly + Daily, Weekly + Daily (Double Time) — configured per `Pay_Rule__c` record (`Type__c` picklist), with Daily/Weekly limits, Double Time start threshold, and 7th-day rule (`Seventh_Day__c`: No / Overtime / Double Time / Both)
- Attestation and break rules captured per entry, not calculated from a rules engine

### 2. Rate Card Management

#### Template-Based System
- `Rate_Card_Template__c` defines reusable rate structures per `Account__c`, with `Priority__c`-based selection and `Is_Default__c` fallback
- `Rate_Card_Template_Detail__c` (master-detail child) defines time-window-based rate variations (`Start_Time__c`/`End_Time__c`)
- Rate Card Types: Hourly, Daily, Unit, Location, Shift, Time Based (`Rate_Card_Type__c`); Detail Types: Hourly, Daily, Unit (`AST_Rate_Card_Detail_Type__c`)
- Pay/Bill calculation types (shared `Pay_Type`/`Bill_Type` global value sets): Standard, Range, Fixed, Markup (Bill Type additionally supports Non-Billable)

#### Candidate-Specific Rate Cards
- `Candidate_Rate_Card__c` is generated from (lookup to) a `Rate_Card_Template__c`; its `Status__c` is a **formula field** (`Active`/`Upcoming`/`Expired`/`Void`) computed from `Start_Date__c`/`End_Date__c`/`Void__c` — not a user-editable picklist
- `Manual_Override__c` allows bypassing template-driven rate values
- No direct lookup exists from `Candidate_Rate_Card__c` to `Contact`; the candidate relationship is established indirectly through `Timesheet__c.Candidate__c` and `Timesheet__c.Candidate_Rate_Card__c`
- `Candidate_Rate_Card_Detail__c` mirrors `Rate_Card_Template_Detail__c` at the candidate level (master-detail to `Candidate_Rate_Card__c`)

#### Dynamic Assignment
- `AST_Rate_Card_Template_Filter__c` (lookup, not master-detail, to `Rate_Card_Template__c`) defines field-based eligibility criteria (`Object_Name__c`, `Target_Field__c`, `Operator__c`, `Value__c`), evaluated by `AST_RateCardTemplateFilterController` / `AST_BooleanEvaluation`
- Enabled only when `AST_Generic_Setting__mdt.Enable_Complex_Rate_Card_Management__c = true`

### 3. Calendar and Holiday Management

#### Calendar System (significantly simpler than hierarchical designs used elsewhere in the Asymbl product suite)
- `Calendar__c` has only: `Is_Default__c`, `Country_Code__c`, `State_Code__c`, `Year__c` (text), `Color__c` (UI display color), plus rollups `Total_Holidays__c` and `Total_Linked_Account__c`
- No parent/child calendar hierarchy, no working-days/standard-hours configuration, and no time zone field exist on `Calendar__c`
- `Calendar_Link__c` is a **dual master-detail junction object** (master-detail to both `Account` and `Calendar__c`) with no other custom fields — there is no effective-dating, primary/secondary flag, or override JSON on the link itself

#### Holiday Features
- `Holiday__c` is a master-detail child of `Calendar__c` with just `Date__c` and `Description__c` (long text) — no floating-holiday calculation, recurrence pattern, observance rule, or holiday-pay-multiplier fields exist on the object
- A baseline set of US holidays ships as a JSON blob in `AST_Holidays_Setting__mdt.US_Holidays_JSON__c` (single field), used to seed/suggest holidays rather than drive a rules engine

#### Pay Rule Engine
- `Pay_Rule__c` is a standalone object (no lookup to `Calendar__c`) with `Country_Code__c`, `State_Code__c`, `Type__c`, `Daily_Limit__c`, `Weekly_Limit__c`, `Double_Time_Start__c`, `Seventh_Day__c`, `Seventh_Day_Double_Time_Start__c`, `Enable_Attestation__c`
- `Timesheet__c.Pay_Rule__c` is a direct lookup — each timesheet is calculated against exactly one Pay Rule record
- A base set of pay rules also ships as JSON in `AST_Pay_Rule__mdt.Pay_Rule_JSON__c` (single field)

#### Pay Period Management
- `Account.Pay_Period_Type__c`: Weekly, Bi-Weekly, Bi-Monthly
- `Account.Pay_Period_Start_Day__c`: day-of-week, controlled by `Pay_Period_Type__c` (not applicable to Bi-Monthly in the same way)
- Next-period generation handled by `AST_CreateTimesheetsBatchable` / `AST_GenerateNextTimesheet`, not by calendar metadata

### 4. Approval Workflows

#### Internal Approval Chain
- Status flow: `New → Draft → Submitted for Approval → Approved by Branch → Approved` (or `Rejected` / `Void`) — exactly 7 `Status__c` picklist values, restricted
- `Timesheet_Approver__c` and `Timesheet_Delegated_Approver__c` are both direct `Contact` lookups on `Timesheet__c`
- Branch-level fields (`Branch_Approver__c`, `Branch_Approved_On__c`, `Approved_By_Branch__c`) exist directly on `Timesheet__c`, not as a separate approval-level object

#### Email-Based (Reply-to-Email) Approvals
- `Email_Approval_Request__c` has only two custom fields: `Date_Range__c` and `Internal_Approver__c` — there is no security token, IP address, expiry, or user-agent tracking on this object
- `Timesheet_Approval_Request__c` is the junction between a timesheet and an email approval batch: master-detail to `Email_Approval_Request__c`, lookup to `Timesheet__c`
- `AST_EmailServiceHandler` (Salesforce Inbound Email Service) parses the reply body for "approve"/"approved"/"approve all" or "reject {reason}"/"reject all {reason}", resolves the `Email_Approval_Request__c` Id embedded in the email HTML, and bulk-updates all linked timesheets — no token/link-click flow exists in the current codebase

#### Adjustments
- `Timesheet_Adjustment__c` is master-detail to `Timesheet__c`, lookup to `Candidate_Rate_Card__c`, with only `Active__c`, `Change_in_Bill__c`, `Change_in_Pay__c`, `Date_of_Change__c`, `Is_Voided_Timesheet_Adjustment__c` — there is no separate adjustment approval workflow, approver lookup, or original/adjusted-hours audit fields on this object

### 5. Reporting and Analytics
- Standard Salesforce reporting against `Timesheet__c`/`Time_Entry__c` rollup and formula fields (e.g., `Total_Hours_Logged__c`, `Gross_Profit__c`, `Gross_Margin_New__c`, `Adjusted_Gross_Profit__c`)
- No bespoke BI/export objects ship in the package; CSV import/export mapping is handled via `AST_CSV_Column_Mapping__mdt` (maps spreadsheet column labels to `Time_Entry__c`/`Alternative_Time__c` API names for bulk upload)

## Technical Architecture

### 1. Data Model Architecture

```
Core Objects:
├── Timesheet__c (Master; lookup to Contact candidate/approvers, lookup to Pay_Rule__c and Candidate_Rate_Card__c)
├── Time_Entry__c (Master-Detail child of Timesheet__c)
├── Alternative_Time__c (Master-Detail child of Timesheet__c)
├── Candidate_Rate_Card__c (lookup to Rate_Card_Template__c; no direct Contact lookup)
└── Candidate_Rate_Card_Detail__c (Master-Detail child of Candidate_Rate_Card__c)

Rate Card Objects:
├── Rate_Card_Template__c (lookup to Account)
├── Rate_Card_Template_Detail__c (Master-Detail child of Rate_Card_Template__c)
└── AST_Rate_Card_Template_Filter__c (lookup to Rate_Card_Template__c)

Calendar/Pay Rule Objects:
├── Calendar__c
├── Calendar_Link__c (Master-Detail to both Account and Calendar__c)
├── Holiday__c (Master-Detail child of Calendar__c)
└── Pay_Rule__c (standalone; referenced by lookup from Timesheet__c)

Approval/Adjustment Objects:
├── Timesheet_Adjustment__c (Master-Detail child of Timesheet__c)
├── Timesheet_Approval_Request__c (Master-Detail child of Email_Approval_Request__c; lookup to Timesheet__c)
└── Email_Approval_Request__c

Configuration Objects (Custom Metadata Types):
├── AST_Generic_Setting__mdt (global feature flags/settings)
├── AST_Placement_Object_Mapping__mdt / AST_Job_Object_Mapping__mdt (external object field mapping rows)
├── AST_External_User_Mapping__mdt (Experience Cloud user auto-provisioning)
├── AST_Holidays_Setting__mdt (single JSON field: US_Holidays_JSON__c)
├── AST_Pay_Rule__mdt (single JSON field: Pay_Rule_JSON__c)
├── AST_CSV_Column_Mapping__mdt (CSV import/export column mapping)
└── AST_Time_Type__c (true custom object, not __mdt — per-state time-off type picklist config)

Operational Objects:
├── AST_Log__c (debug/error log: Class_Name__c, Method_Name__c, Line_Number__c, stack trace)
└── AST_Record_Action__e (Platform Event: Event_Type__c, Object_Api_Name__c, Payload__c, Record_Id__c)
```

### 2. Trigger Framework
- Centralized `AST_TriggerHandler` virtual base class; every trigger instantiates a Handler that routes to a Helper
- 8 triggers: Timesheet, Time Entry, Alternative Time, Candidate Rate Card, Candidate Rate Card Detail, Rate Card Template, Calendar, Community User
- Recursion prevention via static flags on `AST_Constants` plus a `bypass`/`clearBypass` mechanism on `AST_TriggerHandler`

### 3. Security Architecture
- `AST_SecureDml` wraps all DML with `Security.stripInaccessible()` CRUD/FLS enforcement
- Manual Apex-managed sharing on `Timesheet__c` (sharing model `Read`) via `Timesheet__Share`, granting the candidate, approver, and delegated approver read/write access (`AST_TimesheetTriggerHelper.shareTimesheetRecords()`)
- `Time_Entry__c`, `Alternative_Time__c`, `Candidate_Rate_Card_Detail__c`, `Rate_Card_Template_Detail__c`, `Calendar_Link__c`, `Holiday__c`, `Timesheet_Adjustment__c`, `Timesheet_Approval_Request__c` are all `ControlledByParent`

### 4. Integration Architecture

#### Bullhorn / ASYMBL-ATS (`bpats`) Integration — Verified, Not a Hard Dependency
- `sfdx-project.json` declares **no package dependency** on `bpats`
- `AST_Constants.ATS_NAMESPACE = 'bpats'` and `AST_Utility.checkATSpackageInstalled()` call `UserInfo.isCurrentUserLicensed('bpats')` to softly detect whether ASYMBL-ATS is installed in the org
- When detected, `AST_Constants` provides default field-mapping targets (`bpats__Placement__c`, `bpats__Job__c`, `bpats__ATS_Candidate__c`, and related rate/approver/pay-rule fields) that pre-populate the admin mapping UI
- All actual integration logic routes through `AST_Placement_Object_Mapping__mdt` / `AST_Job_Object_Mapping__mdt`, which are populated by an admin against **any** ATS/CRM Placement and Job object — bpats is a convenience default, not a requirement

#### Email Integration
- Inbound Email Service → `AST_EmailServiceHandler` for reply-based approve/reject
- Outbound notification via 6 packaged Email Templates (send-to-approver, approved, rejected, submitted-for-approval, submitted-to-delegated-approver, plus the template folder record)
- `AST_SendTimesheetsEmailBatchable` sends pending-approval emails with PDF attachments (`AST_TimesheetPDFController`)

#### Experience Cloud / Community Integration
- `AST_CommunityUserTrigger` / `AST_CommunityUserTriggerHelper` auto-provision portal users based on `AST_External_User_Mapping__mdt.Contact_Mapping__c` and `Filter_Mapping_JSON__c`, gated by `Enable_User_Creation__c`

### 5. Event-Driven Architecture
- Single Platform Event, `AST_Record_Action__e`, with 4 fields (`Event_Type__c`, `Object_Api_Name__c`, `Payload__c`, `Record_Id__c`) and a Lightning Message Channel (`ASTMessageChannel`) for same-page LWC-to-LWC communication — no multi-event taxonomy or change-data-capture layer exists beyond this

## Conclusion

Asymbl Time is a purpose-built, relatively lean timesheet management package: the core data model (15 true custom objects) is deliberately simple compared to the elaborate, highly parameterized calendar/holiday/pay-rule/approval engine that earlier documentation (pre-2026-10-30) incorrectly described. Several previously documented fields, objects, and workflows (token-based email security, floating holidays, hierarchical calendars, multi-level approval routing beyond branch/final, structured pay-rule metadata) do not exist in the `master` branch and have been removed from this documentation set. The package's real strength is its configuration-driven ATS/CRM integration layer (Custom Metadata field mapping) layered on a compact, well-factored trigger framework.

---

*Document Version: 2.0 (rewritten from verified `master` branch metadata)*
*Last Updated: 2026-10-30*
*Previous version last updated: 2025-01-05 — superseded due to significant drift from shipped metadata*
