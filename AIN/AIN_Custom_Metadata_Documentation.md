# AIN Custom Metadata Documentation

## Overview

The AIN package contains 184+ custom metadata records across 6 metadata types, providing a comprehensive runtime-configurable integration framework. This documentation details all configuration records and their purposes.

## Metadata Record Distribution

| Metadata Type | Active Records | Total Records | Purpose |
|---|---|---|---|
| AIN_Integration_Credentials__mdt | 3 | 3 | System authentication |
| AIN_Job_Mapping__mdt | 61 | 61 | Indeed job mappings |
| AIN_Job_Board_Mapping__mdt | 28 | 28 | Application mappings |
| AIN_Indeed_Apply_Mapping__mdt | 6 | 6 | Indeed Apply config |
| AIN_Vms_Job_Mapping__mdt | 50 | 50 | VMS job mappings |
| AIN_Vms_Job_Board_Mapping__mdt | 42 | 42 | VMS candidate mappings |
| **Total** | **190** | **190** | **Complete configuration** |

## Detailed Metadata Records

### 1. AIN_Integration_Credentials__mdt (3 records)

#### Beeline
```yaml
MasterLabel: Beeline
DeveloperName: Beeline
Purpose: Beeline VMS OAuth 2.0 authentication
Fields:
  - AIN_Base_URL__c: https://api.beeline.com/v1
  - AIN_Client_Id__c: [OAuth Client ID]
  - AIN_Client_Secret__c: [Encrypted]
  - AIN_Employer_Id__c: [Company ID in Beeline]
  - AIN_Contact_Email__c: integration@company.com
  - AIN_Contact_Type__c: Technical
```

#### Indeed
```yaml
MasterLabel: Indeed
DeveloperName: Indeed
Purpose: Indeed Job Board API authentication
Fields:
  - AIN_Base_URL__c: https://secure.indeed.com/v2
  - AIN_Client_Id__c: [API Key]
  - AIN_Client_Secret__c: [Encrypted API Secret]
  - AIN_Employer_Id__c: [Indeed Employer ID]
  - AIN_Contact_Email__c: indeed-support@company.com
  - AIN_Contact_Type__c: Support
```

#### Indeed_Apply
```yaml
MasterLabel: Indeed Apply
DeveloperName: Indeed_Apply
Purpose: Indeed Apply webhook authentication
Fields:
  - AIN_Base_URL__c: https://employers.indeed.com/api
  - AIN_Webhook_URL__c: /services/apexrest/ain/indeed/apply
  - AIN_Client_Secret__c: [Webhook signing secret]
  - AIN_Encryption_Key__c: [Additional encryption]
```

### 2. AIN_Job_Mapping__mdt (61 records)

#### Core Job Fields
```yaml
# Basic Information
- title: Job__c.Name → Required
- description: Job__c.Description__c → Required
- company_name: Account.Name → Required
- job_requisition_id: Job__c.Job_ID__c → Required

# Location Fields
- location_city_region_postal: Job__c.City__c + State + Zip
- location_country: Job__c.Country__c (default: US)
- location_street_address: Job__c.Street_Address__c
- location_latitude: Job__c.Latitude__c
- location_longitude: Job__c.Longitude__c

# Compensation
- minimum_minor: Job__c.Min_Salary__c (in cents)
- maximum_minor: Job__c.Max_Salary__c (in cents)
- salary_currency: Job__c.Currency__c (default: USD)
- period: Job__c.Pay_Period__c (HOURLY/SALARY)

# Employment Details
- job_type: Job__c.Employment_Type__c
- remote_type: Job__c.Remote_Type__c
- experience: Job__c.Experience_Required__c
- education: Job__c.Education_Level__c
- benefits: Job__c.Benefits__c

# Scheduling
- date_published: Job__c.Posted_Date__c
- expiration_date: Job__c.Expiration_Date__c
- working_hours: Job__c.Working_Hours__c

# Indeed Specific
- indeedApply: Job__c.AIN_Indeed_Apply__c
- placement: Job__c.Placement_Type__c
- number_of_hires: Job__c.Number_of_Openings__c
```

#### Advanced Job Mappings
```yaml
# Skills and Requirements
- categories: Job__c.Job_Categories__c (multi-select)
- occupations: Job__c.Occupation_Codes__c
- attributes: Job__c.Required_Skills__c

# Probationary Period (if applicable)
- probationary: Job__c.Has_Probation__c
- probationary_duration: Job__c.Probation_Duration__c
- probationary_conditions: Job__c.Probation_Terms__c
- probationary_minimum_minor: Job__c.Probation_Min_Salary__c
- probationary_maximum_minor: Job__c.Probation_Max_Salary__c

# Overtime Configuration
- fixed_over_time_salary: Job__c.OT_Rate_Fixed__c
- statutory_over_time_salary: Job__c.OT_Rate_Statutory__c
- total_over_time_salary: Job__c.OT_Rate_Total__c
```

### 3. AIN_Job_Board_Mapping__mdt (28 records)

#### Applicant Information
```yaml
# Personal Details
- Applicant_First_Name: Contact.FirstName
- Applicant_Last_Name: Contact.LastName
- Applicant_Full_Name: Contact.Name
- Applicant_Email: Contact.Email → Required
- Applicant_Phone_Number: Contact.Phone

# Application Data
- Applied_On_Milliseconds: ATS_Applicant__c.Applied_Date__c
- Applicant_Cover_Letter: ATS_Applicant__c.Cover_Letter__c
- Applicant_Verified: ATS_Applicant__c.Email_Verified__c

# Job Reference
- Job_Id: Job__c.Id
- Job_Key: Job__c.AIN_Source_Posting_Id__c
- Job_Title: Job__c.Name
- Job_Company: Job__c.Account__r.Name
- Job_Location: Job__c.Full_Location__c
- Job_URL: Job__c.External_URL__c

# Resume JSON Parsing
- Resume_JSON_First_Name: Parse from resume.firstName
- Resume_JSON_Last_Name: Parse from resume.lastName
- Resume_JSON_Email: Parse from resume.email
- Resume_JSON_Phone_Number: Parse from resume.phone
- Resume_JSON_Location_City: Parse from resume.location.city
- Resume_JSON_Location_Country: Parse from resume.location.country
- Resume_JSON_Skills: Parse from resume.skills[]
- Resume_JSON_Summary: Parse from resume.summary
- Resume_JSON_Headline: Parse from resume.headline
```

### 4. AIN_Indeed_Apply_Mapping__mdt (6 records)

```yaml
1. Indeed_Apply_Resume_Required
   - Field: resume_required
   - Type: Boolean
   - Required: true
   - Description: Whether resume is mandatory

2. Indeed_Apply_Cover_Letter_Required
   - Field: cover_letter_required
   - Type: Boolean
   - Required: false
   - Description: Whether cover letter is required

3. Indeed_Apply_Phone_Required
   - Field: phone_required
   - Type: Boolean
   - Required: true
   - Description: Phone number collection requirement

4. Indeed_Apply_Name_Format
   - Field: name_format
   - Type: Picklist
   - Values: ["fullName", "firstLast", "separateFields"]
   - Selected: "firstLast"

5. Indeed_Apply_Resume_Fields_Required
   - Field: required_fields
   - Type: MultiSelect
   - Possible Values: ["education", "experience", "skills"]
   - Selected: ["experience", "skills"]

6. Indeed_Apply_Resume_Fields_Optional
   - Field: optional_fields
   - Type: MultiSelect
   - Possible Values: ["certifications", "languages", "references"]
   - Selected: ["certifications", "languages"]
```

### 5. AIN_Vms_Job_Mapping__mdt (50 records)

#### VMS Job Requirements
```yaml
# Job Identification
- Job_Id: Job__c.Id
- Enterprise_Job_Posting_Number: Job__c.VMS_Job_Number__c
- title: Job__c.Name
- description: Job__c.Full_Description__c

# Client Information
- Client_Name: Job__c.Account__r.Name
- Client_Unique_Id: Job__c.Account__r.VMS_Client_ID__c
- Enterprise_Name: Job__c.Account__r.Parent.Name

# Classification
- Classification_Name: Job__c.Job_Classification__c
- Industry_Name: Job__c.Industry__c
- Request_Type: Job__c.Request_Type__c

# Location Details
- Physical_Work_Location_Street1: Job__c.Street_Address_1__c
- Physical_Work_Location_Street2: Job__c.Street_Address_2__c
- Physical_Work_Location_City: Job__c.City__c
- Physical_Work_Location_State: Job__c.State_Province__c
- Physical_Work_Location_Postal_Code: Job__c.Postal_Code__c
- Physical_Work_Location_Country_Code: Job__c.Country_Code__c

# Cost Centers
- Admin_Cost_Center_Number: Job__c.Admin_Cost_Center__c
- Bill_To_Cost_Center_Number: Job__c.Billing_Cost_Center__c

# Dates and Duration
- Start_Date: Job__c.Start_Date__c
- endDate: Job__c.End_Date__c
- Expiry_Date: Job__c.Expiration_Date__c

# Supplier Management
- Supplier_Name: Job__c.Preferred_Supplier__c
- Supplier_Uid: Job__c.Supplier_ID__c
- Supplier_Enterprise_Connections: Job__c.Supplier_Networks__c
```

### 6. AIN_Vms_Job_Board_Mapping__mdt (42 records)

#### Candidate Submission Fields
```yaml
# Personal Information
- First_Name: Contact.FirstName → Required
- Last_Name: Contact.LastName → Required
- Middle_Name: Contact.MiddleName
- Date_Of_Birth: Contact.Birthdate
- gender: Contact.Gender__c

# Identification
- Candidate_Id: ATS_Applicant__c.AIN_BE_Candidate_Id__c
- Worker_Id: ATS_Applicant__c.AIN_BE_Worker_Id__c
- Partial_National_Id: Contact.SSN_Last_Four__c
- fullNationalId: [Encrypted field]

# Contact Information
- email: Contact.Email → Required
- Phone: Contact.Phone → Required
- Phone_Type: Contact.Phone_Type__c

# Address
- Address_Line: Contact.MailingStreet
- Address_City: Contact.MailingCity
- Address_State: Contact.MailingState
- Address_PostCode: Contact.MailingPostalCode
- Address_Country: Contact.MailingCountry

# Rates and Compensation
- Bill_Rates_Amount: ATS_Applicant__c.Bill_Rate__c
- Pay_Rates_Amount: ATS_Applicant__c.Pay_Rate__c
- Rate_Currency: ATS_Applicant__c.Currency__c
- Rate_Period: ATS_Applicant__c.Rate_Period__c

# Availability
- Date_Available: ATS_Applicant__c.Available_Date__c
- ResourceType: ATS_Applicant__c.Resource_Type__c

# History
- Former_Employee: Contact.Former_Employee__c
- Former_Consultant: Contact.Former_Consultant__c

# Submission Details
- Job_Posting_Id: Job__c.AIN_Source_Posting_Id__c
- Enterprise_Job_Posting_Number: Job__c.VMS_Job_Number__c
- Supplier_Submittal_Comments: ATS_Applicant__c.Submission_Notes__c
```

## Configuration Patterns

### Field Type Distribution
```yaml
Text Fields: 45%
Boolean Fields: 15%
Number Fields: 20%
Date Fields: 10%
Picklist Fields: 10%
```

### Required Field Analysis
```yaml
Job Publishing:
  - Required Fields: 12
  - Optional Fields: 49
  - Conditional Fields: 5

Candidate Submission:
  - Required Fields: 8
  - Optional Fields: 34
  - System Fields: 4
```

## Metadata Management

### Activation Control
- All mappings have `AIN_Is_Active__c` field
- Allows runtime enable/disable without deletion
- Supports A/B testing of mappings

### Sync Configuration
- `AIN_Is_Sync__c` controls bidirectional sync
- Available on VMS mappings only
- Prevents sync loops

### Field Grouping
- `AIN_Section__c` groups related fields
- Used for UI organization
- Improves configuration management

## Best Practices

### 1. Naming Conventions
```yaml
Pattern: {System}_{Entity}_{Field}
Examples:
  - Indeed_Job_Title
  - Beeline_Candidate_FirstName
  - Apply_Resume_Required
```

### 2. Field Mapping Strategy
- Map to most specific field available
- Use formula fields for complex transformations
- Document any data type conversions

### 3. Performance Optimization
- Only activate necessary mappings
- Index frequently queried metadata
- Cache metadata in memory

### 4. Version Control
- Export metadata regularly
- Track changes in source control
- Document configuration changes

## Troubleshooting Guide

### Common Issues

1. **Missing Required Fields**
   - Check `AIN_Is_Required__c` = true mappings
   - Verify source field has data
   - Check field-level security

2. **Data Type Mismatches**
   - Review `AIN_Field_Type__c` settings
   - Implement type conversion in code
   - Use formula fields for transformations

3. **Inactive Mappings**
   - Verify `AIN_Is_Active__c` = true
   - Check for duplicate mappings
   - Review section groupings

4. **Sync Issues**
   - Check `AIN_Is_Sync__c` settings
   - Verify bidirectional mappings exist
   - Monitor for sync loops