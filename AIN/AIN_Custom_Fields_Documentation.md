# AIN Custom Fields Documentation

## Overview

The AIN package adds 56 custom fields across multiple objects to enable integration functionality. These fields control synchronization, store external identifiers, and track integration activities.

## Field Distribution

| Object | Field Count | Purpose |
|---|---|---|
| Job__c | 5 | Integration control and tracking |
| ATS_Applicant__c | 2 | VMS candidate identifiers |
| Custom Metadata Types | 49 | Configuration fields |
| **Total** | **56** | Complete integration support |

## Custom Fields by Object

### Job__c Object Fields (5 fields)

#### 1. AIN_Activity_History__c
- **Type**: Long Text Area (32768)
- **Purpose**: Stores JSON-formatted integration activity log
- **Usage**: Tracks all integration events for audit and debugging
- **Example Value**:
```json
[
  {
    "timestamp": "2024-01-15T10:30:00Z",
    "action": "job_published",
    "system": "Indeed",
    "status": "success",
    "externalId": "IND-123456"
  }
]
```

#### 2. AIN_Indeed_Apply__c
- **Type**: Checkbox
- **Purpose**: Enables Indeed Apply for this job posting
- **Default**: false
- **Impact**: When true, job accepts applications through Indeed Apply webhook
- **UI**: Displayed on Job layout, controlled via quick action

#### 3. AIN_Is_Synced__c
- **Type**: Checkbox
- **Purpose**: Indicates if job is currently synchronized with external systems
- **Default**: false
- **Read-Only**: Set by integration processes
- **Usage**: Visual indicator and filtering for sync status

#### 4. AIN_Source_Posting_Id__c
- **Type**: Text(255)
- **Purpose**: Stores external system job identifier
- **External ID**: Yes
- **Unique**: No (same job can be posted to multiple systems)
- **Example**: "BEE-2024-1234" (Beeline), "IND-987654" (Indeed)

#### 5. AIN_Sync_This_Record__c
- **Type**: Checkbox
- **Purpose**: Controls whether job should be synchronized
- **Default**: false
- **Trigger**: When true, initiates sync process
- **UI**: Admin-controlled field on page layout

---

### ATS_Applicant__c Object Fields (2 fields)

#### 1. AIN_BE_Candidate_Id__c
- **Type**: Text(50)
- **Purpose**: Beeline VMS candidate identifier
- **External ID**: Yes
- **Usage**: Links Salesforce applicant to Beeline candidate record
- **Format**: "BEE-CAND-XXXXXXXX"
- **Integration**: Set during candidate submission to VMS

#### 2. AIN_BE_Worker_Id__c
- **Type**: Text(50)
- **Purpose**: Beeline VMS worker identifier (for placed candidates)
- **External ID**: Yes
- **Usage**: Tracks candidates who become active workers
- **Format**: "BEE-WORK-XXXXXXXX"
- **Lifecycle**: Set when candidate is hired/placed

---

### Custom Metadata Type Fields (49 fields)

These fields are distributed across 6 custom metadata types to support runtime configuration:

#### AIN_Integration_Credentials__mdt (8 fields)

1. **AIN_Base_URL__c**
   - Type: Text(255)
   - Purpose: API endpoint base URL
   - Example: "https://api.beeline.com/v1"

2. **AIN_Client_Id__c**
   - Type: Text(255)
   - Purpose: OAuth client ID or API username
   - Security: Visible to admins only

3. **AIN_Client_Secret__c**
   - Type: Text(255)
   - Purpose: OAuth secret or API key
   - Security: Encrypted at rest

4. **AIN_Contact_Email__c**
   - Type: Email
   - Purpose: Support contact for integration

5. **AIN_Contact_Type__c**
   - Type: Text(50)
   - Purpose: Type of contact (Technical/Support/Business)

6. **AIN_Employer_Id__c**
   - Type: Text(100)
   - Purpose: External system employer identifier

7. **AIN_Encryption_Key__c**
   - Type: Text(255)
   - Purpose: Additional encryption for sensitive data
   - Security: Encrypted at rest

8. **AIN_Webhook_URL__c**
   - Type: URL(255)
   - Purpose: Incoming webhook endpoint

#### AIN_Job_Mapping__mdt (10 fields)

1. **AIN_Field_Api_Name__c** - External API field name
2. **AIN_Field_Label__c** - User-friendly label
3. **AIN_Field_Type__c** - Data type (Text/Number/Date/Boolean)
4. **AIN_Is_Active__c** - Enable/disable mapping
5. **AIN_Is_Required__c** - Required by external system
6. **AIN_Is_Sync__c** - Bidirectional sync flag
7. **AIN_Mapped_Field__c** - Salesforce field API name
8. **AIN_Mapped_Object__c** - Salesforce object API name
9. **AIN_Section__c** - UI grouping section
10. **AIN_Sync_Field_Api_Name__c** - Alternative sync field

#### AIN_Job_Board_Mapping__mdt (7 fields)

1. **AIN_Field_Api_Name__c** - Incoming field name
2. **AIN_Field_Label__c** - Display label
3. **AIN_Field_Type__c** - Expected data type
4. **AIN_Is_Active__c** - Mapping active status
5. **AIN_Is_Required__c** - Required for processing
6. **AIN_Mapped_Field__c** - Target Salesforce field
7. **AIN_Mapped_Object__c** - Target Salesforce object

#### AIN_Indeed_Apply_Mapping__mdt (8 fields)

1. **AIN_Field_Api_Name__c** - Indeed Apply field
2. **AIN_Field_Label__c** - UI label
3. **AIN_Field_Type__c** - Field data type
4. **AIN_IsMultiple__c** - Accepts multiple values
5. **AIN_Is_Active__c** - Configuration active
6. **AIN_Is_Required__c** - Required by Indeed
7. **AIN_Possible_Values__c** - JSON array of options
8. **AIN_Selected_Values__c** - Current selections

#### AIN_Vms_Job_Mapping__mdt (6 fields)

1. **AIN_Field_Api_Name__c** - VMS field identifier
2. **AIN_Field_Label__c** - Display name
3. **AIN_Is_Active__c** - Enable/disable
4. **AIN_Is_Required__c** - VMS requirement
5. **AIN_Mapped_Field__c** - Source field
6. **AIN_Mapped_Object__c** - Source object

#### AIN_Vms_Job_Board_Mapping__mdt (10 fields)

1. **AIN_Field_Api_Name__c** - VMS candidate field
2. **AIN_Field_Label__c** - Field label
3. **AIN_Field_Type__c** - Data type
4. **AIN_Is_Active__c** - Active status
5. **AIN_Is_Required__c** - Required field
6. **AIN_Is_Sync__c** - Sync enabled
7. **AIN_Mapped_Field__c** - Salesforce field
8. **AIN_Mapped_Object__c** - Salesforce object
9. **AIN_Sync_Field_Api_Name__c** - Sync field override
10. **Candidate_Id** - Special tracking field

## Field Security Model

### Field-Level Security Settings

| Field | AIN_Admin | AIN_Beeline_Admin | AIN_Guest_User | Standard User |
|---|---|---|---|---|
| AIN_Activity_History__c | Read/Write | Read | None | Read |
| AIN_Indeed_Apply__c | Read/Write | Read | None | Read |
| AIN_Is_Synced__c | Read | Read | Read | Read |
| AIN_Source_Posting_Id__c | Read/Write | Read | Read | Read |
| AIN_Sync_This_Record__c | Read/Write | None | None | None |
| AIN_BE_Candidate_Id__c | Read/Write | Read/Write | Read | Read |
| AIN_BE_Worker_Id__c | Read/Write | Read/Write | Read | Read |

### Metadata Field Access

- All metadata fields require "Customize Application" permission
- AIN_Admin permission set includes metadata access
- Encrypted fields masked in UI for non-admin users

## Field Usage Patterns

### Integration Control Pattern
```apex
// Check if job should sync
if (job.AIN_Sync_This_Record__c && !job.AIN_Is_Synced__c) {
    // Perform sync
    AIN_IntegrationService.syncJob(job);
    
    // Update status
    job.AIN_Is_Synced__c = true;
    job.AIN_Source_Posting_Id__c = externalId;
    
    // Log activity
    job.AIN_Activity_History__c = AIN_ActivityLogger.addEntry(
        job.AIN_Activity_History__c,
        'sync_completed',
        'success'
    );
}
```

### External ID Usage
```apex
// Find applicant by Beeline candidate ID
ATS_Applicant__c applicant = [
    SELECT Id, Name, Contact__c
    FROM ATS_Applicant__c
    WHERE AIN_BE_Candidate_Id__c = :beelineCandidateId
    LIMIT 1
];
```

### Activity Logging Pattern
```apex
public static String addActivityEntry(String existingLog, String action, String status) {
    List<Object> activities = String.isBlank(existingLog) 
        ? new List<Object>() 
        : (List<Object>)JSON.deserializeUntyped(existingLog);
    
    Map<String, Object> newEntry = new Map<String, Object>{
        'timestamp' => DateTime.now().formatGMT('yyyy-MM-dd\'T\'HH:mm:ss\'Z\''),
        'action' => action,
        'status' => status,
        'user' => UserInfo.getUserId()
    };
    
    activities.add(newEntry);
    
    // Keep only last 50 entries
    if (activities.size() > 50) {
        activities = activities.subList(activities.size() - 50, activities.size());
    }
    
    return JSON.serialize(activities);
}
```

## Field Validation Rules

### VR_AIN_External_ID_Format
**Object**: ATS_Applicant__c
**Logic**:
```
AND(
    NOT(ISBLANK(AIN_BE_Candidate_Id__c)),
    NOT(REGEX(AIN_BE_Candidate_Id__c, "^BEE-CAND-[A-Z0-9]{8}$"))
)
```
**Error**: "Beeline Candidate ID must follow format: BEE-CAND-XXXXXXXX"

### VR_AIN_Sync_Requirements
**Object**: Job__c
**Logic**:
```
AND(
    AIN_Sync_This_Record__c = true,
    OR(
        ISBLANK(Name),
        ISBLANK(Description__c),
        ISBLANK(Account__c)
    )
)
```
**Error**: "Job must have Name, Description, and Account to enable sync"

## Field Dependencies

### Workflow Rules

1. **WF_AIN_Clear_Sync_Status**
   - Fires when key job fields change
   - Sets AIN_Is_Synced__c = false
   - Triggers re-sync process

2. **WF_AIN_Enable_Indeed_Apply**
   - Sets AIN_Indeed_Apply__c = true
   - When job meets Indeed requirements
   - Validates required fields present

### Process Builder / Flow

1. **PB_AIN_Job_Sync_Automation**
   - Monitors AIN_Sync_This_Record__c
   - Initiates platform events
   - Handles async processing

2. **Flow_AIN_Applicant_Deduplication**
   - Uses AIN_BE_Candidate_Id__c
   - Prevents duplicate applicants
   - Merges existing records

## Best Practices

### 1. External ID Management
- Always populate external IDs during integration
- Use consistent formatting across systems
- Index external ID fields for performance
- Handle null values gracefully

### 2. Activity Logging
- Implement size limits to prevent field overflow
- Use structured JSON for consistency
- Include timestamp, user, and action
- Provide clear action descriptions

### 3. Sync Control
- Use checkbox fields for clear on/off states
- Implement validation before enabling sync
- Provide visual indicators of sync status
- Clear sync status when data changes

### 4. Field Security
- Limit write access to integration fields
- Make status fields read-only for users
- Encrypt sensitive identifiers
- Audit field access regularly

## Reporting Considerations

### Custom Report Types

1. **Integration Status Report**
   - Filter: AIN_Is_Synced__c = true/false
   - Group by: AIN_Source_Posting_Id__c prefix
   - Measure: Count of synced records

2. **Activity Analysis**
   - Parse AIN_Activity_History__c
   - Track success/failure rates
   - Identify integration bottlenecks

3. **VMS Candidate Tracking**
   - Filter: AIN_BE_Candidate_Id__c != null
   - Track placement rates
   - Monitor candidate pipeline

### Dashboard Components

1. **Sync Status Gauge**
   - Synced vs. unsynced jobs
   - Real-time status updates
   - Drill-down capability

2. **Integration Activity Timeline**
   - Parse activity JSON
   - Show recent events
   - Highlight errors

3. **External System Coverage**
   - Jobs by external system
   - Multi-system postings
   - System performance metrics