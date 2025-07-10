# Asymbl Time Package Documentation

## Overview

The Asymbl Time package is a comprehensive time and attendance management solution for Salesforce, providing advanced timesheet management, rate card configuration, and approval workflows. This package enables organizations to track employee time, manage complex pay rules, and automate timesheet processing.

## Package Components Summary

- **Total Components**: ~1,400+
- **Apex Classes**: 104
- **Triggers**: 7  
- **Custom Objects**: 20
- **Lightning Web Components**: 35
- **Custom Labels**: 790
- **Custom Fields**: 279
- **Metadata Types**: 6 custom metadata types
- **UI Components**: Tabs, Layouts, Quick Actions, FlexiPages

## Documentation Structure

This folder contains comprehensive documentation for the Asymbl Time package:

1. **Entity Relationship Diagrams (ERDs)**
   - [ERD_Time_Attendance_Core.md](./ERD_Time_Attendance_Core.md) - Core timesheet and time entry objects
   - [ERD_Rate_Card_Management.md](./ERD_Rate_Card_Management.md) - Rate card templates and configurations
   - [ERD_Calendar_Holiday_Management.md](./ERD_Calendar_Holiday_Management.md) - Calendar, holidays, and pay rules
   - [ERD_Configuration_Metadata.md](./ERD_Configuration_Metadata.md) - Settings and metadata configurations
   - [ERD_Approval_Adjustment.md](./ERD_Approval_Adjustment.md) - Approval workflows and adjustments

2. **Architecture Documentation**
   - [TIME_Package_Documentation.md](./TIME_Package_Documentation.md) - Complete technical documentation
   - [TIME_Apex_Architecture.md](./TIME_Apex_Architecture.md) - Apex classes and trigger documentation
   - [TIME_LWC_Architecture.md](./TIME_LWC_Architecture.md) - Lightning Web Component details
   - [TIME_UI_Components.md](./TIME_UI_Components.md) - UI components and user interface elements

3. **Additional Documentation**
   - [TIME_Integration_Architecture.md](./TIME_Integration_Architecture.md) - Integration points and email services
   - [TIME_Complete_Overview.md](./TIME_Complete_Overview.md) - Comprehensive package overview

## Key Features

### Time & Attendance Management
- Comprehensive timesheet tracking with multiple entry types
- Support for regular, overtime, and double-time hours
- Alternative time entries for holidays and time off
- Mobile and desktop interfaces for time entry

### Rate Card Management
- Flexible rate card templates with complex configurations
- Support for time-based, location-based, and shift-based rates
- Dynamic pay and bill rate calculations
- Burden and markup management

### Calendar & Holiday Management
- Multi-calendar support with country/state configurations
- Holiday tracking and automatic time-off calculations
- Pay rule engine for overtime calculations
- Configurable work week definitions

### Approval Workflows
- Multi-level approval processes
- Email-based approval capabilities
- Delegated approver support
- Timesheet rejection tracking with reasons

### Advanced Features
- Batch timesheet generation
- PDF generation for timesheets
- Void and adjustment capabilities
- Community portal integration
- Comprehensive audit trails

## Quick Navigation

- **For Developers**: Start with [TIME_Apex_Architecture.md](./TIME_Apex_Architecture.md) and [TIME_LWC_Architecture.md](./TIME_LWC_Architecture.md)
- **For Administrators**: Review [TIME_Package_Documentation.md](./TIME_Package_Documentation.md) and ERD diagrams
- **For Business Users**: See [TIME_Complete_Overview.md](./TIME_Complete_Overview.md) for feature overview

## Component Statistics

| Component Type | Count | Purpose |
|----------------|-------|---------|
| **ApexClass** | 104 | Business logic, controllers, utilities |
| **ApexTrigger** | 7 | Record lifecycle management |
| **CustomObject** | 20 | Data model for time tracking |
| **LightningComponentBundle** | 35 | Modern UI components |
| **CustomLabel** | 790 | Internationalization support |
| **CustomField** | 279 | Field extensions |
| **CustomTab** | 7 | Navigation elements |
| **QuickAction** | 2 | Quick timesheet actions |
| **EmailTemplate** | 6 | Approval notifications |

## Related Documentation

For methodology and documentation standards, see:
- [Claude.md](../Claude.md) - Documentation framework and ULTRATHINK methodology

---

*Last Updated*: ${new Date().toISOString().split('T')[0]}