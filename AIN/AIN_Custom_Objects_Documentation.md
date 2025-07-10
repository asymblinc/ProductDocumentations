# AIN Custom Objects Documentation

## Overview

The AIN package leverages 6 Custom Metadata Types to provide a flexible, runtime-configurable integration framework. These metadata types enable dynamic field mapping, credential management, and integration configuration without requiring code deployments.

## Custom Metadata Types

### 1. AIN_Integration_Credentials__mdt

**Purpose**: Stores authentication credentials and connection settings for external systems.

**Record Count**: 3 (Beeline, Indeed, Indeed_Apply)

#### Fields

| Field API Name | Type | Description | Encrypted |
|---|---|---|---|
| `AIN_Base_URL__c` | Text(255) | Base URL for the external system API | No |
| `AIN_Client_Id__c` | Text(255) | OAuth client ID or API username | No |
| `AIN_Client_Secret__c` | Text(255) | OAuth client secret or API key | Yes |
| `AIN_Contact_Email__c` | Email | Support contact email for the integration | No |
| `AIN_Contact_Type__c` | Text(50) | Type of contact (Support, Technical, Business) | No |
| `AIN_Employer_Id__c` | Text(100) | External system employer/company identifier | No |
| `AIN_Encryption_Key__c` | Text(255) | Additional encryption key for sensitive data | Yes |
| `AIN_Webhook_URL__c` | URL | Webhook endpoint for incoming data | No |

#### Configuration Records

1. **Beeline**
   - OAuth 2.0 credentials for VMS integration
   - Production and sandbox endpoints
   
2. **Indeed**
   - API key-based authentication
   - Job publishing endpoint configuration
   
3. **Indeed_Apply**
   - Webhook security credentials
   - Application processing settings

---

### 2. AIN_Job_Mapping__mdt

**Purpose**: Maps Salesforce Job fields to Indeed job board API fields.

**Record Count**: 61

#### Fields

| Field API Name | Type | Description |
|---|---|---|
| `AIN_Field_Api_Name__c` | Text(255) | External API field name |
| `AIN_Field_Label__c` | Text(255) | User-friendly field label |
| `AIN_Field_Type__c` | Picklist | Data type (Text, Number, Date, Boolean, Picklist) |
| `AIN_Is_Active__c` | Checkbox | Whether this mapping is currently active |
| `AIN_Is_Required__c` | Checkbox | Whether this field is required by the external system |
| `AIN_Is_Sync__c` | Checkbox | Whether to sync this field bidirectionally |
| `AIN_Mapped_Field__c` | Text(255) | Salesforce field API name |
| `AIN_Mapped_Object__c` | Text(50) | Salesforce object API name |
| `AIN_Section__c` | Text(100) | Grouping section for UI display |
| `AIN_Sync_Field_Api_Name__c` | Text(255) | Alternative field for sync operations |

#### Key Mappings

- **Job Details**: title, description, company_name
- **Location**: location_city_region_postal, location_country
- **Compensation**: minimum_minor, maximum_minor, salary_currency
- **Employment Type**: job_type, remote_type, working_hours
- **Posting Details**: date_published, expiration_date

---

### 3. AIN_Job_Board_Mapping__mdt

**Purpose**: Maps application data from job boards to Salesforce objects.

**Record Count**: 28

#### Fields

| Field API Name | Type | Description |
|---|---|---|
| `AIN_Field_Api_Name__c` | Text(255) | Incoming field name from job board |
| `AIN_Field_Label__c` | Text(255) | Display label |
| `AIN_Field_Type__c` | Picklist | Expected data type |
| `AIN_Is_Active__c` | Checkbox | Active status |
| `AIN_Is_Required__c` | Checkbox | Required for processing |
| `AIN_Mapped_Field__c` | Text(255) | Target Salesforce field |
| `AIN_Mapped_Object__c` | Text(50) | Target Salesforce object |

#### Application Processing Mappings

- **Applicant Data**: Full name, email, phone, location
- **Resume Information**: Skills, experience, education
- **Application Metadata**: Applied date, source, job reference
- **Resume JSON**: Structured resume data parsing

---

### 4. AIN_Indeed_Apply_Mapping__mdt

**Purpose**: Configuration for Indeed Apply webhook integration.

**Record Count**: 6

#### Fields

| Field API Name | Type | Description |
|---|---|---|
| `AIN_Field_Api_Name__c` | Text(255) | Indeed Apply field identifier |
| `AIN_Field_Label__c` | Text(255) | Field label for UI |
| `AIN_Field_Type__c` | Text(50) | Field data type |
| `AIN_IsMultiple__c` | Checkbox | Whether field accepts multiple values |
| `AIN_Is_Active__c` | Checkbox | Active status |
| `AIN_Is_Required__c` | Checkbox | Required by Indeed Apply |
| `AIN_Possible_Values__c` | Long Text Area | JSON array of possible values |
| `AIN_Selected_Values__c` | Long Text Area | Currently selected values |

#### Configuration Types

1. **Resume Requirements**: Resume required/optional settings
2. **Cover Letter**: Cover letter configuration
3. **Phone Requirements**: Phone number collection
4. **Name Format**: Name field formatting rules
5. **Optional Fields**: Additional data collection
6. **Required Fields**: Mandatory information

---

### 5. AIN_Vms_Job_Mapping__mdt

**Purpose**: Maps Salesforce Job data to VMS (Beeline) job posting format.

**Record Count**: 50

#### Fields

| Field API Name | Type | Description |
|---|---|---|
| `AIN_Field_Api_Name__c` | Text(255) | VMS API field name |
| `AIN_Field_Label__c` | Text(255) | User-friendly label |
| `AIN_Is_Active__c` | Checkbox | Mapping active status |
| `AIN_Is_Required__c` | Checkbox | Required by VMS |
| `AIN_Mapped_Field__c` | Text(255) | Source Salesforce field |
| `AIN_Mapped_Object__c` | Text(50) | Source Salesforce object |

#### VMS-Specific Mappings

- **Job Information**: title, description, skills, location
- **Client Details**: Client_Name, Client_Unique_Id
- **Financial**: Bill rates, cost centers, currency
- **Work Location**: Physical and tax location details
- **Compliance**: Industry classifications, request types
- **Supplier Info**: Supplier connections and UIDs

---

### 6. AIN_Vms_Job_Board_Mapping__mdt

**Purpose**: Maps candidate submission data for VMS systems.

**Record Count**: 42

#### Fields

| Field API Name | Type | Description |
|---|---|---|
| `AIN_Field_Api_Name__c` | Text(255) | VMS candidate field |
| `AIN_Field_Label__c` | Text(255) | Display label |
| `AIN_Field_Type__c` | Picklist | Data type |
| `AIN_Is_Active__c` | Checkbox | Active status |
| `AIN_Is_Required__c` | Checkbox | Required for submission |
| `AIN_Is_Sync__c` | Checkbox | Sync bidirectionally |
| `AIN_Mapped_Field__c` | Text(255) | Salesforce field |
| `AIN_Mapped_Object__c` | Text(50) | Salesforce object |
| `AIN_Sync_Field_Api_Name__c` | Text(255) | Sync field override |

#### Candidate Submission Mappings

- **Personal Info**: First/Last name, DOB, gender, SSN (partial)
- **Contact**: Email, phone, address components
- **Rates**: Bill rates, pay rates, currency
- **Availability**: Date available, resource type
- **History**: Former employee/consultant flags
- **IDs**: Candidate_Id, Worker_Id for tracking

---

## Metadata Architecture Benefits

### 1. Runtime Configuration
- No code deployment needed for field mapping changes
- Business users can modify mappings via UI
- Instant activation of configuration changes

### 2. Multi-System Support
- Each system has dedicated metadata types
- Consistent pattern across integrations
- Easy to add new systems

### 3. Security & Compliance
- Encrypted storage for sensitive fields
- Field-level security on metadata
- Audit trail through SetupAuditTrail

### 4. Scalability
- Supports unlimited field mappings
- Hierarchical configuration possible
- Environment-specific settings

## Configuration Management

### Best Practices

1. **Naming Conventions**
   - Use consistent prefixes (AIN_)
   - Descriptive field names
   - Clear labeling for UI

2. **Version Control**
   - Export metadata regularly
   - Track changes in source control
   - Document configuration changes

3. **Testing Strategy**
   - Test mappings in sandbox first
   - Validate data types match
   - Check required field compliance

4. **Performance**
   - Index frequently queried fields
   - Limit active mappings to necessary fields
   - Cache metadata for better performance

## Usage Examples

### Retrieving Active Job Mappings
```apex
List<AIN_Job_Mapping__mdt> activeJobMappings = [
    SELECT AIN_Field_Api_Name__c, 
           AIN_Mapped_Field__c,
           AIN_Field_Type__c
    FROM AIN_Job_Mapping__mdt
    WHERE AIN_Is_Active__c = true
    ORDER BY AIN_Section__c, MasterLabel
];
```

### Dynamic Field Transformation
```apex
public static Map<String, Object> transformJobData(Job__c job) {
    Map<String, Object> transformedData = new Map<String, Object>();
    
    for (AIN_Job_Mapping__mdt mapping : activeJobMappings) {
        if (job.get(mapping.AIN_Mapped_Field__c) != null) {
            transformedData.put(
                mapping.AIN_Field_Api_Name__c,
                convertFieldValue(
                    job.get(mapping.AIN_Mapped_Field__c),
                    mapping.AIN_Field_Type__c
                )
            );
        }
    }
    
    return transformedData;
}
```

## Maintenance & Monitoring

### Regular Tasks
1. Review inactive mappings quarterly
2. Validate external system field changes
3. Monitor mapping usage statistics
4. Clean up obsolete configurations

### Troubleshooting
1. Check field type mismatches
2. Verify required fields are mapped
3. Ensure credentials are current
4. Review activity logs for errors