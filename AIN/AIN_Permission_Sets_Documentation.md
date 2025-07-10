# AIN Permission Sets Documentation

## Overview

The AIN package includes 3 permission sets that provide role-based access control for the integration platform. These permission sets follow the principle of least privilege while ensuring users have appropriate access to perform their responsibilities.

## Permission Set Summary

| Permission Set | API Name | License | Purpose | Target Users |
|---|---|---|---|---|
| **Asymbl Integration Admin** | AIN_Admin | Salesforce | Full administrative access | System Administrators, Integration Managers |
| **Beeline Admin** | AIN_Beeline_Admin | Salesforce | VMS-specific administration | VMS Coordinators, Recruiting Ops |
| **Integration Guest User** | AIN_IN_Integration_Guest_User | Guest User | API endpoint access | External Systems, Webhooks |

## Detailed Permission Set Documentation

### 1. AIN_Admin (Asymbl Integration Admin)

**Purpose**: Provides complete administrative access to all AIN integration features, configuration, and monitoring capabilities.

#### Object Permissions

| Object | Read | Create | Edit | Delete | View All | Modify All |
|---|---|---|---|---|---|---|
| Job__c | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| ATS_Applicant__c | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Contact | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ |
| Account | ✓ | ✗ | ✓ | ✗ | ✓ | ✗ |
| Activity_Log__c | ✓ | ✓ | ✗ | ✓ | ✓ | ✗ |

#### Field Permissions

**Job__c Fields**:
- AIN_Activity_History__c: Read, Edit
- AIN_Indeed_Apply__c: Read, Edit
- AIN_Is_Synced__c: Read, Edit
- AIN_Source_Posting_Id__c: Read, Edit
- AIN_Sync_This_Record__c: Read, Edit

**ATS_Applicant__c Fields**:
- AIN_BE_Candidate_Id__c: Read, Edit
- AIN_BE_Worker_Id__c: Read, Edit

#### Custom Metadata Access
- AIN_Integration_Credentials__mdt: Read, Create, Edit, Delete
- AIN_Job_Mapping__mdt: Read, Create, Edit, Delete
- AIN_Job_Board_Mapping__mdt: Read, Create, Edit, Delete
- AIN_Indeed_Apply_Mapping__mdt: Read, Create, Edit, Delete
- AIN_Vms_Job_Mapping__mdt: Read, Create, Edit, Delete
- AIN_Vms_Job_Board_Mapping__mdt: Read, Create, Edit, Delete

#### Apex Class Access
- All 41 AIN Apex classes: Enabled
- Key classes:
  - AIN_BeelineCalloutServices
  - AIN_IndeedCalloutService
  - AIN_MetadataService
  - All controller classes

#### Tab Visibility
- Asymbl_Integration_Admin: Default On

#### System Permissions
- API Enabled: ✓
- View Setup and Configuration: ✓
- Customize Application: ✓
- Manage Custom Metadata: ✓
- Author Apex: ✓
- Modify Metadata: ✓
- View All Data: ✓
- Modify All Data: ✗

#### Lightning Component Access
- All 16 AIN Lightning components: Enabled

---

### 2. AIN_Beeline_Admin (Beeline Admin)

**Purpose**: Provides focused access for users managing Beeline VMS integrations without full system administration privileges.

#### Object Permissions

| Object | Read | Create | Edit | Delete | View All | Modify All |
|---|---|---|---|---|---|---|
| Job__c | ✓ | ✗ | ✓ | ✗ | ✓ | ✗ |
| ATS_Applicant__c | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ |
| Contact | ✓ | ✓ | ✓ | ✗ | ✗ | ✗ |
| Account | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| Activity_Log__c | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |

#### Field Permissions

**Job__c Fields**:
- AIN_Activity_History__c: Read
- AIN_Indeed_Apply__c: Read
- AIN_Is_Synced__c: Read
- AIN_Source_Posting_Id__c: Read
- AIN_Sync_This_Record__c: No Access

**ATS_Applicant__c Fields**:
- AIN_BE_Candidate_Id__c: Read, Edit
- AIN_BE_Worker_Id__c: Read, Edit

#### Custom Metadata Access
- AIN_Integration_Credentials__mdt: Read (Beeline record only)
- AIN_Vms_Job_Mapping__mdt: Read
- AIN_Vms_Job_Board_Mapping__mdt: Read
- Other metadata types: No Access

#### Apex Class Access
- AIN_BeelineCalloutServices: Enabled
- AIN_BeelineAuthSettingController: Enabled
- AIN_BeelineObjectMappingController: Enabled
- AIN_PublishJobQuickActionController: Enabled
- AIN_Utility: Enabled
- AIN_Constants: Enabled
- Other classes: No Access

#### Tab Visibility
- Asymbl_Integration_Admin: Default Off

#### System Permissions
- API Enabled: ✓
- View Setup and Configuration: ✗
- Run Reports: ✓
- Export Reports: ✓
- View Dashboards: ✓

#### Lightning Component Access
- ainBeelineAuthenticationContainer: Enabled
- ainBeelineAuthenticationSetting: Enabled
- ainActivityTimeline: Enabled
- ainJobPortalCard: Enabled
- ainErrorDetailModal: Enabled
- Other components: No Access

---

### 3. AIN_IN_Integration_Guest_User (Integration Guest User)

**Purpose**: Provides minimal access required for external systems to interact with Salesforce via REST APIs and webhooks.

#### Object Permissions

| Object | Read | Create | Edit | Delete | View All | Modify All |
|---|---|---|---|---|---|---|
| Job__c | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| ATS_Applicant__c | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ |
| Contact | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ |
| Account | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ |

#### Field Permissions

**Job__c Fields**:
- AIN_Source_Posting_Id__c: Read
- AIN_Is_Synced__c: Read
- Other AIN fields: No Access

**ATS_Applicant__c Fields**:
- AIN_BE_Candidate_Id__c: Read
- AIN_BE_Worker_Id__c: Read

**Contact Fields** (Standard):
- FirstName: Read, Edit
- LastName: Read, Edit
- Email: Read, Edit
- Phone: Read, Edit

#### Apex Class Access
- AIN_IndeedApplyRestResource: Enabled
- AIN_RequestParser: Enabled
- AIN_JsonFlattener: Enabled
- Other classes: No Access

#### REST Endpoint Access
- /services/apexrest/ain/indeed/apply: Enabled
- Other endpoints: No Access

#### System Permissions
- API Enabled: ✓
- View All Data: ✗
- Modify All Data: ✗
- Create Audit Fields: ✗

## Permission Set Assignment Strategy

### Role-Based Assignment

| User Role | Recommended Permission Set | Justification |
|---|---|---|
| System Administrator | AIN_Admin | Full configuration and monitoring |
| Integration Manager | AIN_Admin | Manage all integrations |
| VMS Coordinator | AIN_Beeline_Admin | Beeline-specific operations |
| Recruiting Operations | AIN_Beeline_Admin | Candidate management |
| External Systems | AIN_IN_Integration_Guest_User | API access only |
| Standard Users | None | View-only via sharing |

### Assignment Best Practices

1. **Principle of Least Privilege**
   - Assign minimum required permissions
   - Regular access reviews
   - Remove unused assignments

2. **Segregation of Duties**
   - Separate configuration from usage
   - Different admins for different systems
   - Audit trail for changes

3. **Guest User Security**
   - Assign only to site guest users
   - Monitor API usage
   - Implement IP restrictions

## Security Model

### Data Access Levels

```
AIN_Admin (Full Access)
    ↓
AIN_Beeline_Admin (VMS Operations)
    ↓
Standard Users (View via Sharing)
    ↓
AIN_Guest_User (API Only)
```

### Field-Level Security Matrix

| Field | Admin | Beeline Admin | Guest | Standard User |
|---|---|---|---|---|
| Credentials | R/W | R | - | - |
| Sync Controls | R/W | - | - | - |
| External IDs | R/W | R/W | R | R |
| Activity Logs | R/W | R | - | R |

### Apex Class Security

```apex
// Example: Class-level security check
public with sharing class AIN_AdminService {
    public static void performAdminAction() {
        if (!FeatureManagement.checkPermission('AIN_Admin')) {
            throw new AIN_SecurityException('Insufficient privileges');
        }
        // Admin action logic
    }
}
```

## Sharing Rules Integration

### Recommended Sharing Rules

1. **Job Integration Sharing**
   - Share Job__c records with AIN_Beeline_Admin users
   - Criteria: AIN_Is_Synced__c = true
   - Access Level: Read/Write

2. **Applicant VMS Sharing**
   - Share ATS_Applicant__c with VMS coordinators
   - Criteria: AIN_BE_Candidate_Id__c != null
   - Access Level: Read/Write

3. **Activity Log Visibility**
   - Share Activity_Log__c with all internal users
   - Criteria: Related to Job or Applicant
   - Access Level: Read Only

## Permission Set Groups

### Recommended Groups

1. **Integration Administrators**
   - Includes: AIN_Admin
   - Additional: Modify All Data, View Setup
   - Purpose: Complete system control

2. **VMS Operators**
   - Includes: AIN_Beeline_Admin
   - Additional: Run Reports, Export Reports
   - Purpose: Operational management

3. **API Integration Users**
   - Includes: AIN_IN_Integration_Guest_User
   - Additional: API Enabled
   - Purpose: System integration

## Audit and Compliance

### Permission Usage Tracking

```apex
// Track permission usage
public static void logPermissionUsage(String action) {
    Permission_Usage__c usage = new Permission_Usage__c();
    usage.User__c = UserInfo.getUserId();
    usage.Action__c = action;
    usage.Permission_Set__c = getCurrentPermissionSet();
    usage.Timestamp__c = DateTime.now();
    insert usage;
}
```

### Compliance Checks

1. **Quarterly Reviews**
   - Validate active assignments
   - Remove inactive users
   - Check for privilege creep

2. **Access Certification**
   - Manager approval required
   - Annual recertification
   - Document justification

3. **Audit Reports**
   - Permission set assignments
   - High-privilege usage
   - Failed access attempts

## Implementation Guidelines

### Initial Setup

1. **Deploy Permission Sets**
   ```bash
   sfdx force:source:deploy -m PermissionSet:AIN_Admin,AIN_Beeline_Admin,AIN_IN_Integration_Guest_User
   ```

2. **Assign to Users**
   ```apex
   PermissionSetAssignment psa = new PermissionSetAssignment();
   psa.AssigneeId = userId;
   psa.PermissionSetId = permissionSetId;
   insert psa;
   ```

3. **Configure Sharing Rules**
   - Create via Setup UI
   - Test with different users
   - Document exceptions

### Testing Strategy

1. **Positive Testing**
   - Verify expected access
   - Test all features
   - Validate data visibility

2. **Negative Testing**
   - Confirm access restrictions
   - Test boundary conditions
   - Verify error handling

3. **Integration Testing**
   - Test with actual users
   - Verify API access
   - Check performance impact

## Troubleshooting

### Common Issues

1. **"Insufficient Privileges" Error**
   - Check permission set assignment
   - Verify object/field permissions
   - Review sharing rules

2. **Missing Tab Access**
   - Confirm tab visibility settings
   - Check user's app assignment
   - Verify license compatibility

3. **API Access Denied**
   - Validate API Enabled permission
   - Check connected app settings
   - Review IP restrictions

### Debug Queries

```apex
// Check user's permission sets
SELECT PermissionSet.Name 
FROM PermissionSetAssignment 
WHERE AssigneeId = :userId

// Verify field access
SELECT Field, PermissionsRead, PermissionsEdit 
FROM FieldPermissions 
WHERE ParentId IN (SELECT Id FROM PermissionSet WHERE Name LIKE 'AIN%')
```

## Summary

The three AIN permission sets provide a comprehensive security model that:

1. **Enables Proper Access**: Users have necessary permissions
2. **Maintains Security**: Follows least privilege principle
3. **Supports Compliance**: Audit trail and access reviews
4. **Scales Effectively**: Clear role-based model
5. **Integrates Safely**: Secure API access for external systems