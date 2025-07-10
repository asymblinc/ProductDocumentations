# AIN Custom Labels Documentation

## Overview

The AIN package includes 99 custom labels that provide internationalization support, user interface text, error messages, and system messages. These labels ensure consistent messaging across the integration platform and support multi-language deployments.

## Label Categories

| Category | Count | Purpose |
|---|---|---|
| UI Labels | 35 | Buttons, headers, field labels |
| Error Messages | 25 | Integration errors, validation messages |
| Success Messages | 10 | Confirmation and success notifications |
| Help Text | 15 | Instructions and tooltips |
| System Messages | 14 | Status indicators, system notifications |
| **Total** | **99** | Complete UI and messaging support |

## Detailed Label Documentation

### UI Labels (35)

#### Action Labels
- **AIN_Actions_Label**
  - Value: "Actions"
  - Usage: Column header for action buttons
  - Context: Data tables and list views

- **AIN_Cancel_Button_Label**
  - Value: "Cancel"
  - Usage: Standard cancel button
  - Context: All modal dialogs and forms

- **AIN_Save_Button_Label**
  - Value: "Save"
  - Usage: Save action button
  - Context: Configuration forms

- **AIN_Reset_Button_Label**
  - Value: "Reset"
  - Usage: Reset form data
  - Context: Configuration screens

- **AIN_ResetAll_Button_Label**
  - Value: "Reset All"
  - Usage: Clear all configurations
  - Context: Bulk operations

#### Publishing Actions
- **AIN_Publish_Button_Label**
  - Value: "Publish"
  - Usage: Publish job to external systems
  - Context: Job quick action

- **AIN_Unpublish_Button_Label**
  - Value: "Unpublish"
  - Usage: Remove job from external systems
  - Context: Published job actions

- **AIN_Retry_Publish_Button_Label**
  - Value: "Retry Publishing"
  - Usage: Retry failed publish attempts
  - Context: Error recovery

#### View Controls
- **AIN_View_All_Button_Label**
  - Value: "View All"
  - Usage: Expand collapsed lists
  - Context: Activity timeline

- **AIN_View_All_Detail_Button_Label**
  - Value: "View Full Details"
  - Usage: Show complete information
  - Context: Error details modal

- **AIN_ExpandAll_Button_Label**
  - Value: "Expand All"
  - Usage: Expand all collapsible sections
  - Context: Configuration screens

- **AIN_CollapseAll_Button_Label**
  - Value: "Collapse All"
  - Usage: Collapse all expanded sections
  - Context: Configuration screens

#### Import/Export
- **AIN_Import_Button_Label**
  - Value: "Import"
  - Usage: Import configuration
  - Context: Field mapping settings

- **AIN_Export_Button_Label**
  - Value: "Export"
  - Usage: Export configuration
  - Context: Field mapping settings

- **AIN_Show_API_Name_Button_Label**
  - Value: "Show API Names"
  - Usage: Toggle API name display
  - Context: Field mapping interface

#### Section Headers
- **AIN_Activity_Timeline_Heading_Label**
  - Value: "Integration Activity Timeline"
  - Usage: Timeline component header
  - Context: Job record page

- **AIN_Integrations_Admin_Setup**
  - Value: "Asymbl Integration Administration"
  - Usage: Main admin page title
  - Context: Setup tab

- **AIN_Beeline_Tab_Heading**
  - Value: "Beeline VMS Configuration"
  - Usage: Beeline settings section
  - Context: Admin setup

#### Field Labels
- **AIN_Job_Field_Label**
  - Value: "Salesforce Job Field"
  - Usage: Source field column
  - Context: Field mapping

- **AIN_Salesforce_Job_Field_Label**
  - Value: "Job Field"
  - Usage: Abbreviated field label
  - Context: Compact layouts

- **AIN_Indeed_Fields**
  - Value: "Indeed API Fields"
  - Usage: Target field column
  - Context: Indeed mapping

- **AIN_Vms_Job_Fields**
  - Value: "VMS Job Fields"
  - Usage: VMS field column
  - Context: Beeline mapping

### Error Messages (25)

#### Authentication Errors
- **AIN_Authentication_Error_Message**
  - Value: "Authentication failed. Please check your credentials and try again."
  - Usage: Generic auth failure
  - Severity: Error

- **AIN_Beeline_Authentication_Error**
  - Value: "Unable to authenticate with Beeline VMS. Error: {0}"
  - Usage: Beeline-specific auth error
  - Parameters: Error details

- **AIN_Unable_to_Save_Credentials**
  - Value: "Failed to save credentials. Please ensure all required fields are populated."
  - Usage: Credential save failure
  - Context: Auth configuration

#### Validation Errors
- **AIN_Complete_The_Field_Error**
  - Value: "Please complete all required fields before proceeding."
  - Usage: Form validation
  - Context: All forms

- **AIN_Missing_Required_Fields**
  - Value: "The following required fields are missing: {0}"
  - Usage: Specific field validation
  - Parameters: Field list

- **AIN_Duplicate_Field_Found**
  - Value: "Duplicate field mapping detected. Each field can only be mapped once."
  - Usage: Mapping validation
  - Context: Field configuration

#### Integration Errors
- **AIN_Job_Sync_Failure_Message**
  - Value: "Failed to sync job to {0}. Error: {1}"
  - Usage: Sync failure notification
  - Parameters: System name, error

- **AIN_Job_Publish_Error_Message**
  - Value: "Unable to publish job. Please check the error details and try again."
  - Usage: Generic publish error
  - Context: Quick action

- **AIN_Error_in_WebHook_Setup**
  - Value: "Failed to configure webhook endpoint. Please contact your administrator."
  - Usage: Webhook setup error
  - Severity: Critical

#### Data Errors
- **AIN_Object_Not_Found_Message**
  - Value: "The specified {0} could not be found."
  - Usage: Missing record error
  - Parameters: Object type

- **AIN_Invaild_URL**
  - Value: "The provided URL is invalid. Please check the format and try again."
  - Usage: URL validation
  - Context: Endpoint configuration

### Success Messages (10)

- **AIN_Success_Label**
  - Value: "Success"
  - Usage: Generic success indicator
  - Context: Toast messages

- **AIN_Token_Generated_Succesfully**
  - Value: "Authentication token generated successfully."
  - Usage: OAuth success
  - Context: Auth testing

- **AIN_Job_Sync_Success_Message**
  - Value: "Job synchronized successfully to {0}."
  - Usage: Sync completion
  - Parameters: System name

- **AIN_Job_Publish_Portal_Success_Message**
  - Value: "Job published successfully to {0}. External ID: {1}"
  - Usage: Single system publish
  - Parameters: Portal, ID

- **AIN_Job_Publish_All_Portal_Success_Message**
  - Value: "Job published to all selected portals successfully."
  - Usage: Multi-system publish
  - Context: Bulk operations

- **AIN_Custom_Metadata_Created_Success_Label**
  - Value: "Configuration saved successfully."
  - Usage: Metadata save
  - Context: Admin setup

- **AIN_Mapping_File_Downloaded_Success_Message**
  - Value: "Mapping configuration exported successfully."
  - Usage: Export completion
  - Context: Import/export

### Help Text (15)

- **AIN_External_Job_Posting_Help_Text**
  - Value: "Select the external systems where this job should be posted."
  - Usage: Field help
  - Context: Job publishing

- **AIN_Filter_Job_Help_Text_Label**
  - Value: "Use filters to find specific field mappings quickly."
  - Usage: Search help
  - Context: Mapping config

- **AIN_Job_Posting_Help_Text**
  - Value: "Published jobs will appear on selected external systems within 15 minutes."
  - Usage: Expectation setting
  - Context: Publishing UI

- **AIN_Job_Board_Mapping_Info**
  - Value: "Map incoming application fields to Salesforce objects and fields."
  - Usage: Section help
  - Context: Admin setup

- **AIN_Job_Sync_Field_Help_Text_Label**
  - Value: "Enable sync to automatically update external systems when job details change."
  - Usage: Feature explanation
  - Context: Sync settings

### System Messages (14)

#### Status Indicators
- **AIN_Published_Label**
  - Value: "Published"
  - Usage: Job status
  - Icon: Green checkmark

- **AIN_Unpublished_Label**
  - Value: "Not Published"
  - Usage: Job status
  - Icon: Gray dash

- **AIN_Synced_Field_Label**
  - Value: "Synchronized"
  - Usage: Sync status
  - Context: Field indicator

#### Configuration Labels
- **AIN_Base_Url**
  - Value: "API Base URL"
  - Usage: Config field
  - Context: Credentials

- **AIN_Client_Id**
  - Value: "Client ID"
  - Usage: OAuth field
  - Context: Authentication

- **AIN_Client_Secret**
  - Value: "Client Secret"
  - Usage: OAuth field
  - Security: Masked input

- **AIN_Employer_Id**
  - Value: "Employer ID"
  - Usage: External identifier
  - Context: VMS config

## Label Usage Guidelines

### Naming Conventions

1. **Prefix**: All labels start with "AIN_"
2. **Category Suffix**:
   - `_Label` for UI elements
   - `_Message` for notifications
   - `_Error` for error messages
   - `_Help_Text` for help content

### Implementation Patterns

#### In Apex Code
```apex
// Get label value
String errorMsg = Label.AIN_Authentication_Error_Message;

// Format with parameters
String syncError = String.format(
    Label.AIN_Job_Sync_Failure_Message,
    new String[]{'Indeed', 'Connection timeout'}
);
```

#### In Lightning Web Components
```javascript
import authError from '@salesforce/label/c.AIN_Authentication_Error_Message';
import saveButton from '@salesforce/label/c.AIN_Save_Button_Label';

export default class AinAuthSetting extends LightningElement {
    labels = {
        authError,
        saveButton
    };
    
    showError() {
        this.showToast('Error', this.labels.authError, 'error');
    }
}
```

#### In Visualforce
```xml
<apex:page>
    <apex:pageMessage severity="error" 
                      summary="{!$Label.AIN_Authentication_Error_Message}"/>
    
    <apex:commandButton value="{!$Label.AIN_Save_Button_Label}" 
                        action="{!save}"/>
</apex:page>
```

## Internationalization Support

### Translation Guidelines

1. **Key Considerations**:
   - Keep translations concise
   - Maintain parameter placeholders
   - Consider UI space constraints
   - Preserve technical terms

2. **Parameter Handling**:
   ```
   English: "Failed to sync job to {0}. Error: {1}"
   Spanish: "Error al sincronizar trabajo en {0}. Error: {1}"
   French: "Échec de synchronisation vers {0}. Erreur: {1}"
   ```

3. **Technical Terms**:
   - Don't translate: API, OAuth, VMS, URL
   - Localize: Job → Trabajo/Emploi
   - Keep consistent: Indeed, Beeline (brand names)

### Supported Languages

The AIN package is translation-ready for:
- English (en_US) - Default
- Spanish (es)
- French (fr)
- German (de)
- Japanese (ja)
- Portuguese (pt_BR)

## Label Categories by Component

### ainAdminSetup Component
- AIN_Integrations_Admin_Setup
- AIN_Save_Button_Label
- AIN_Cancel_Button_Label
- AIN_Reset_Button_Label

### ainPublishJobQuickAction Component
- AIN_Publish_Job_Quick_Action_Label
- AIN_Publish_Button_Label
- AIN_Job_Publish_Portals
- AIN_Publish_Portals_Count

### ainActivityTimeline Component
- AIN_Activity_Timeline_Heading_Label
- AIN_No_Recent_Activities_Heading_Label
- AIN_View_All_Button_Label
- AIN_Actions_Label

### ainObjectMappingSetting Component
- AIN_Job_Mapping_Label
- AIN_Import_Button_Label
- AIN_Export_Button_Label
- AIN_Show_API_Name_Button_Label

## Error Message Severity Levels

| Severity | Count | Usage | Example |
|---|---|---|---|
| Critical | 5 | System failures | AIN_Error_in_WebHook_Setup |
| Error | 15 | Operation failures | AIN_Authentication_Error_Message |
| Warning | 3 | Non-critical issues | AIN_Missing_Email_Error |
| Info | 2 | Informational | AIN_No_Job_Portal |

## Best Practices

### 1. Label Creation
- Use descriptive names
- Include context in name
- Keep messages user-friendly
- Avoid technical jargon

### 2. Error Messages
- Be specific about the problem
- Provide actionable solutions
- Include relevant context
- Use parameters for dynamic content

### 3. UI Labels
- Keep concise for buttons
- Use consistent terminology
- Follow Salesforce conventions
- Consider mobile display

### 4. Help Text
- Explain the "why"
- Provide examples
- Link to documentation
- Keep under 255 characters

## Maintenance Guidelines

### Adding New Labels

1. **Naming**: Follow AIN_[Category]_[Purpose]_[Type] pattern
2. **Description**: Add clear description in metadata
3. **Categories**: Assign to appropriate category
4. **Translation**: Mark for translation if needed

### Updating Existing Labels

1. **Impact Analysis**: Check all usage locations
2. **Parameter Changes**: Maintain backward compatibility
3. **Translation Updates**: Update all language versions
4. **Testing**: Verify in all contexts

### Deprecating Labels

1. **Mark Deprecated**: Add "(Deprecated)" to description
2. **Replace Usage**: Update all references
3. **Communication**: Notify development team
4. **Removal**: Delete after 2 releases

## Label Usage Report

| Component Type | Label Count | Most Used Labels |
|---|---|---|
| Apex Classes | 45 | Error messages, API labels |
| LWC | 35 | UI labels, buttons |
| Flows | 10 | Status messages |
| Validation Rules | 9 | Error messages |
| **Total Usage** | **99** | All labels actively used |