# Asymbl Time Package Documentation

## Overview

The Asymbl Time package (namespace `ASYMBL_Time`) is a comprehensive time and attendance management solution for Salesforce, providing timesheet management, rate card configuration, calendar/holiday tracking, pay-rule driven overtime calculations, and approval workflows. This package enables staffing/recruiting organizations to track candidate time, manage complex pay rules, and automate timesheet processing against any configurable external ATS/CRM (Bullhorn `bpats` is a soft-coded, detectable default — see note below).

## Package Components Summary

Counts below are sourced directly from `manifest/package.xml` on the `master` branch (verified 2026-10-30):

- **Apex Classes**: 135
- **Apex Triggers**: 8
- **Custom Objects** (package.xml `CustomObject` type, includes 7 Custom Metadata Types + 1 Platform Event): 25
  - True custom sObjects (records-based, `__c`): 17
  - Custom Metadata Types (`__mdt`): 7
  - Platform Events (`__e`): 1
- **Lightning Web Components**: 53 (packaged; 57 bundles exist on disk — 4 are not currently listed in package.xml)
- **Custom Labels**: 648 (packaged members; 702 label entries exist in the labels file on disk)
- **Custom Fields**: 290
- **Custom Metadata Records**: 30
- **Validation Rules**: 22
- **Compact Layouts**: 13
- **Layouts**: 23
- **Global Value Sets**: 6
- **UI Components**: 7 Custom Tabs, 2 Quick Actions, 5 FlexiPages, 2 Aura bundles (legacy), 1 Flow, 1 Lightning Message Channel, 1 Sharing Reason

## Documentation Structure

This folder contains the following documentation for the Asymbl Time package:

1. **Package Overview**
   - [ASYMBL_TIME_PACKAGE_OVERVIEW.md](./ASYMBL_TIME_PACKAGE_OVERVIEW.md) — Executive summary, architecture, and capability overview

2. **Entity Relationship Diagrams (ERDs)**
   - [ERD_Time_Attendance_Core.md](./ERD_Time_Attendance_Core.md) — Timesheet, Time Entry, and Alternative Time objects
   - [ERD_Calendar_Holiday_Management.md](./ERD_Calendar_Holiday_Management.md) — Calendar, Calendar Link, Holiday, and Pay Rule objects
   - [ERD_Approval_Adjustment_System.md](./ERD_Approval_Adjustment_System.md) — Timesheet approval requests, email-based approval, and adjustments
   - [ERD_Rate_Card_Management.md](./ERD_Rate_Card_Management.md) — Rate card templates and candidate-specific rate cards
   - [ERD_Configuration_Metadata.md](./ERD_Configuration_Metadata.md) — Custom Metadata Types, logging, and platform events

3. **Verification**
   - [ULTRATHINK_Verification_Report.md](./ULTRATHINK_Verification_Report.md) — Component-count verification matrix against `manifest/package.xml`
   - [ULTRATHINK_Gap_Analysis_Report.md](./ULTRATHINK_Gap_Analysis_Report.md) — Documentation completeness gap analysis

## Key Features

### Time & Attendance Management
- Timesheet aggregation by pay period with Regular/Overtime/Double-Time hour tracking
- Daily time entries with primary and secondary start/end time windows, break tracking
- Alternative Time entries for holidays and time-off (separate approval lifecycle from the parent timesheet)
- Desktop and mobile Lightning Web Component interfaces

### Rate Card Management
- Reusable Rate Card Templates per Account with Fixed/Range/Markup pay and bill types
- Time-based rate variations via Rate Card Template Detail records
- Candidate-specific rate card instances generated from templates, independently overridable
- Dynamic template-eligibility filtering via `AST_Rate_Card_Template_Filter__c`

### Calendar & Holiday Management
- Simple per-Account calendar linkage (`Calendar_Link__c`, a dual master-detail junction between `Account` and `Calendar__c`)
- Holidays are master-detail child records of a Calendar (date + description only — no floating-holiday engine)
- Country/state-aware Pay Rules (`Pay_Rule__c`) independent of Calendar, defining Daily/Weekly/7th-day overtime and double-time thresholds
- US holiday defaults and base pay-rule defaults are shipped as JSON blobs in Custom Metadata (`AST_Holidays_Setting__mdt`, `AST_Pay_Rule__mdt`), not structured field-based configuration

### Approval Workflows
- Branch-then-final two-stage internal approval on `Timesheet__c.Status__c`
- Reply-to-email approval via `AST_EmailServiceHandler` (parses "approve"/"reject {reason}" from inbound email body) tied to `Email_Approval_Request__c` / `Timesheet_Approval_Request__c`
- Delegated approver support via `Timesheet_Delegated_Approver__c`
- Rejection reason + rejection history tracking; voiding and adjustment tracking via `Timesheet_Adjustment__c`

### Advanced Features
- Batch timesheet generation (`AST_CreateTimesheetsBatchable`) and PDF generation for approver emails
- Void/replace capability for approved timesheets (`AST_VoidTimesheetController`, `Original_Timesheet__c` self-lookup)
- Experience Cloud / Community portal user auto-provisioning (`AST_External_User_Mapping__mdt`, `AST_CommunityUserTrigger`)
- Centralized debug logging (`AST_Log__c`) and real-time event publishing (`AST_Record_Action__e` platform event)

## Dependency Note (bpats / ASYMBL-ATS)

`sfdx-project.json` on `master` declares **no formal 2GP package dependency** on `bpats` (ASYMBL-ATS) — there is no `dependencies` array referencing it. However, `AST_Constants.cls` hardcodes `bpats__Placement__c` / `bpats__Job__c` / `bpats__ATS_Candidate__c` field-mapping defaults that are only used when `AST_Utility.checkATSpackageInstalled()` detects the `bpats` namespace is licensed in the org (`UserInfo.isCurrentUserLicensed('bpats')`). All actual Placement/Job field mapping is soft-coded through `AST_Placement_Object_Mapping__mdt` / `AST_Job_Object_Mapping__mdt`, meaning AST is designed to integrate with any ATS/CRM, with bpats as a detectable, convenience default rather than a hard package requirement.

## Quick Navigation

- **For Developers**: Start with the repo's own `CLAUDE.md` (trigger framework, Apex class inventory) and the ERD docs for data model detail
- **For Administrators**: Review [ASYMBL_TIME_PACKAGE_OVERVIEW.md](./ASYMBL_TIME_PACKAGE_OVERVIEW.md) and the Configuration Metadata ERD for mapping/setup
- **For Business Users**: See the Feature sections above and [ASYMBL_TIME_PACKAGE_OVERVIEW.md](./ASYMBL_TIME_PACKAGE_OVERVIEW.md)

## Related Documentation

For methodology and documentation standards, see:
- [Claude.md](../Claude.md) — Documentation framework and ULTRATHINK methodology

---

*Last Updated*: 2026-10-30
*Source Branch Verified*: `master` (asymbl-time, commit `38a8d3eb`)
