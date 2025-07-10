# AIN Lightning Web Components Documentation

## Overview

The AIN package includes 16 Lightning Web Components (LWCs) that provide the administrative user interface for configuring and managing integrations. These components follow modern Lightning Web Component standards and provide a responsive, intuitive experience.

## Component Summary

| Component | Purpose | Used In |
|---|---|---|
| ainAdminSetup | Main admin dashboard | Asymbl Integration Admin tab |
| ainAuthenticationContainer | Authentication management wrapper | Admin setup |
| ainBeelineAuthenticationContainer | Beeline-specific auth wrapper | Admin setup |
| ainObjectMappingSetting | Field mapping configuration | Admin setup |
| ainIndeedApplySettings | Indeed Apply configuration | Admin setup |
| ainActivityTimeline | Integration activity viewer | Job record pages |
| ainPublishJobQuickAction | Job publishing interface | Job quick action |
| ainUtility | Shared utility functions | Multiple components |
| ainMultiselectCombobox | Multi-select dropdown | Field mapping |
| ainFieldMappingEntry | Single field mapping row | Object mapping |
| ainJobPortalCard | Portal status display | Job publishing |
| ainErrorDetailModal | Error details viewer | Multiple components |
| ainAuthenticationSetting | Generic auth configuration | Auth containers |
| ainBeelineAuthenticationSetting | Beeline auth specifics | Beeline container |
| ainIndeedFieldEntry | Indeed field configuration | Indeed Apply settings |
| ainMultiSelectComboboxItem | Multi-select item component | Multi-select combobox |

## Detailed Component Documentation

### 1. ainAdminSetup

**Purpose**: Main administrative dashboard for AIN configuration

**Features**:
- Tab-based navigation for different configuration areas
- Real-time validation of settings
- Import/export functionality
- Connection testing

**Key Properties**:
```javascript
@api recordId;              // Context record ID
@track activeTab = 'auth';  // Current active tab
@track isLoading = false;   // Loading state
```

**Events Dispatched**:
- `settingsupdated` - When any configuration changes
- `connectiontested` - After testing external connections

**Parent Components**: None (top-level)

**Child Components**:
- ainAuthenticationContainer
- ainObjectMappingSetting
- ainIndeedApplySettings

---

### 2. ainAuthenticationContainer

**Purpose**: Container for managing all authentication settings

**Features**:
- System selection (Beeline, Indeed, etc.)
- Credential management
- Connection testing
- OAuth flow initiation

**Configuration Structure**:
```javascript
systems = [
    {
        name: 'Beeline',
        type: 'oauth',
        icon: 'standard:partner_marketing_budget'
    },
    {
        name: 'Indeed', 
        type: 'apikey',
        icon: 'standard:job_profile'
    }
];
```

**Apex Controller**: `AIN_AuthenticationController`

---

### 3. ainBeelineAuthenticationContainer

**Purpose**: Beeline VMS-specific authentication configuration

**Features**:
- OAuth 2.0 configuration
- Client ID/Secret management
- Callback URL display
- Token refresh settings

**Security Features**:
- Encrypted credential storage
- Masked secret display
- Secure token handling

---

### 4. ainObjectMappingSetting

**Purpose**: Configure field mappings between Salesforce and external systems

**Features**:
- Dynamic field discovery
- Drag-and-drop mapping
- Bulk mapping operations
- Mapping validation
- Import/export mappings

**Key Methods**:
```javascript
loadFieldMappings() {
    // Loads existing mappings from metadata
}

saveMappings() {
    // Saves mappings to custom metadata
}

validateMapping(sourceField, targetField) {
    // Validates field compatibility
}
```

**Child Components**:
- ainFieldMappingEntry (multiple instances)

---

### 5. ainIndeedApplySettings

**Purpose**: Configure Indeed Apply integration settings

**Features**:
- Resume requirement settings
- Field collection configuration
- Webhook URL generation
- Test application sending

**Configuration Options**:
```javascript
settings = {
    resumeRequired: true,
    coverLetterRequired: false,
    phoneRequired: true,
    additionalFields: ['skills', 'experience']
};
```

**Child Components**:
- ainIndeedFieldEntry (for field configuration)

---

### 6. ainActivityTimeline

**Purpose**: Display integration activity history for a record

**Features**:
- Chronological activity display
- Activity filtering
- Error highlighting
- Expandable details
- Real-time updates

**Activity Types**:
- Job Published
- Application Received
- Sync Completed
- Error Occurred
- Status Updated

**Data Structure**:
```javascript
activities = [
    {
        id: 'activity-001',
        type: 'publish',
        timestamp: '2024-01-15T10:30:00Z',
        status: 'success',
        system: 'Indeed',
        details: {...}
    }
];
```

---

### 7. ainPublishJobQuickAction

**Purpose**: Quick action interface for publishing jobs to external systems

**Features**:
- Multi-system selection
- Pre-flight validation
- Progress tracking
- Error handling
- Retry failed publishes

**Publishing Flow**:
1. System selection
2. Field validation
3. Preview transformed data
4. Confirm and publish
5. Track status

**Child Components**:
- ainJobPortalCard (for each system)

---

### 8. ainUtility

**Purpose**: Shared utility functions for all components

**Exported Functions**:
```javascript
// Format currency values
export function formatCurrency(amount, currencyCode) { }

// Parse and validate JSON
export function parseJSON(jsonString) { }

// Date formatting
export function formatDate(dateValue, format) { }

// Field type validation
export function validateFieldType(value, expectedType) { }

// Error message formatting
export function formatError(error) { }
```

---

### 9. ainMultiselectCombobox

**Purpose**: Enhanced multi-select dropdown component

**Features**:
- Search/filter options
- Select all/none
- Keyboard navigation
- Pills for selected items
- Accessible (ARIA compliant)

**Properties**:
```javascript
@api label;          // Field label
@api options;        // Available options
@api value;          // Selected values
@api placeholder;    // Placeholder text
@api disabled;       // Disabled state
```

**Child Components**:
- ainMultiSelectComboboxItem

---

### 10. ainFieldMappingEntry

**Purpose**: Single row in field mapping configuration

**Features**:
- Source field selection
- Target field selection
- Transformation options
- Required field indicator
- Delete mapping

**Data Binding**:
```javascript
@api mapping = {
    sourceField: '',
    targetField: '',
    transformation: 'none',
    isRequired: false
};
```

---

### 11. ainJobPortalCard

**Purpose**: Display job portal status and actions

**Features**:
- Portal connection status
- Last sync timestamp
- Publish/unpublish actions
- Error details
- Performance metrics

**Status Indicators**:
- 🟢 Published
- 🟡 Pending
- 🔴 Error
- ⚫ Not Published

---

### 12. ainErrorDetailModal

**Purpose**: Modal for displaying detailed error information

**Features**:
- Error stack trace
- Timestamp
- Affected records
- Suggested actions
- Copy error details

**Error Structure**:
```javascript
error = {
    code: 'AIN_001',
    message: 'Authentication failed',
    details: 'OAuth token expired',
    timestamp: '2024-01-15T10:30:00Z',
    stackTrace: '...',
    suggestedAction: 'Refresh authentication tokens'
};
```

---

### 13. ainAuthenticationSetting

**Purpose**: Generic authentication setting component

**Features**:
- Credential input fields
- Test connection
- Save credentials
- View/hide sensitive data

**Supported Auth Types**:
- OAuth 2.0
- API Key
- Basic Auth
- Custom Headers

---

### 14. ainBeelineAuthenticationSetting

**Purpose**: Beeline-specific authentication configuration

**Features**:
- OAuth client configuration
- Employer ID setting
- Environment selection
- Advanced OAuth settings

**Beeline-Specific Fields**:
- Client ID
- Client Secret
- Employer ID
- Environment (Production/Sandbox)
- Scope configuration

---

### 15. ainIndeedFieldEntry

**Purpose**: Configure individual Indeed Apply field requirements

**Features**:
- Field requirement toggle
- Optional/required setting
- Field type selection
- Validation rules

**Field Configuration**:
```javascript
field = {
    name: 'experience',
    label: 'Years of Experience',
    type: 'number',
    required: true,
    validation: {
        min: 0,
        max: 50
    }
};
```

---

### 16. ainMultiSelectComboboxItem

**Purpose**: Individual item in multi-select combobox

**Features**:
- Checkbox selection
- Hover state
- Click handling
- Accessibility support

**Properties**:
```javascript
@api item;           // Item data
@api isSelected;     // Selection state
```

## Component Communication Patterns

### Event-Driven Architecture

```javascript
// Child component dispatches event
this.dispatchEvent(new CustomEvent('fieldmapped', {
    detail: {
        sourceField: 'Job__c.Name',
        targetField: 'title'
    },
    bubbles: true,
    composed: true
}));

// Parent component handles event
handleFieldMapped(event) {
    const { sourceField, targetField } = event.detail;
    this.updateMapping(sourceField, targetField);
}
```

### Property Binding

```javascript
<!-- Parent component -->
<c-ain-field-mapping-entry
    mapping={mappingRecord}
    onmappingchange={handleMappingChange}>
</c-ain-field-mapping-entry>

<!-- Child component -->
@api 
get mapping() {
    return this._mapping;
}
set mapping(value) {
    this._mapping = value;
    this.processMapping();
}
```

## Styling and Theming

### CSS Architecture

```css
/* Component-specific styling */
.ain-component {
    --ain-primary-color: #0070d2;
    --ain-error-color: #d4504c;
    --ain-success-color: #4bca81;
}

/* Responsive design */
@media (max-width: 768px) {
    .ain-component {
        flex-direction: column;
    }
}
```

### SLDS Compliance

All components follow Salesforce Lightning Design System:
- Use SLDS classes for consistency
- Follow SLDS spacing tokens
- Implement SLDS color palette
- Support density settings

## Performance Optimization

### 1. Lazy Loading
```javascript
import { loadStyle } from 'lightning/platformResourceLoader';
import AIN_RESOURCES from '@salesforce/resourceUrl/AIN_Resources';

connectedCallback() {
    if (!this.stylesLoaded) {
        loadStyle(this, AIN_RESOURCES + '/styles/ain-components.css')
            .then(() => {
                this.stylesLoaded = true;
            });
    }
}
```

### 2. Debouncing
```javascript
handleSearchChange(event) {
    clearTimeout(this.searchTimeout);
    this.searchTimeout = setTimeout(() => {
        this.performSearch(event.target.value);
    }, 300);
}
```

### 3. Wire Service Optimization
```javascript
@wire(getFieldMappings, { systemType: '$systemType' })
wiredMappings({ error, data }) {
    if (data) {
        this.processMappings(data);
    } else if (error) {
        this.handleError(error);
    }
}
```

## Accessibility Features

### ARIA Support
```html
<div role="combobox" 
     aria-expanded={isExpanded}
     aria-haspopup="listbox"
     aria-labelledby="label-id">
    <!-- Component content -->
</div>
```

### Keyboard Navigation
```javascript
handleKeyDown(event) {
    switch(event.key) {
        case 'ArrowDown':
            this.selectNextItem();
            break;
        case 'ArrowUp':
            this.selectPreviousItem();
            break;
        case 'Enter':
            this.confirmSelection();
            break;
        case 'Escape':
            this.closeDropdown();
            break;
    }
}
```

## Testing Considerations

### Jest Test Example
```javascript
import { createElement } from 'lwc';
import AinObjectMappingSetting from 'c/ainObjectMappingSetting';

describe('c-ain-object-mapping-setting', () => {
    afterEach(() => {
        while (document.body.firstChild) {
            document.body.removeChild(document.body.firstChild);
        }
    });

    it('displays field mappings correctly', () => {
        const element = createElement('c-ain-object-mapping-setting', {
            is: AinObjectMappingSetting
        });
        
        element.mappings = mockMappings;
        document.body.appendChild(element);
        
        const mappingRows = element.shadowRoot.querySelectorAll('c-ain-field-mapping-entry');
        expect(mappingRows.length).toBe(mockMappings.length);
    });
});
```

## Best Practices

### 1. Error Handling
```javascript
try {
    const result = await saveFieldMappings({ mappings: this.mappings });
    this.showSuccess('Mappings saved successfully');
} catch (error) {
    this.showError('Failed to save mappings', error);
    console.error('Save error:', error);
}
```

### 2. Loading States
```javascript
<template if:true={isLoading}>
    <lightning-spinner alternative-text="Loading"></lightning-spinner>
</template>
<template if:false={isLoading}>
    <!-- Component content -->
</template>
```

### 3. Confirmation Dialogs
```javascript
async handleDelete() {
    const result = await LightningConfirm.open({
        message: 'Are you sure you want to delete this mapping?',
        variant: 'destructive',
        label: 'Delete Mapping'
    });
    
    if (result) {
        this.deleteMapping();
    }
}
```

## Component Deployment

### Metadata Structure
```xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>59.0</apiVersion>
    <isExposed>true</isExposed>
    <targets>
        <target>lightning__RecordPage</target>
        <target>lightning__AppPage</target>
        <target>lightning__HomePage</target>
    </targets>
    <targetConfigs>
        <targetConfig targets="lightning__RecordPage">
            <property name="showActivityTimeline" type="Boolean" default="true"/>
        </targetConfig>
    </targetConfigs>
</LightningComponentBundle>
```

## Summary

The 16 Lightning Web Components in the AIN package provide a comprehensive, user-friendly interface for managing complex integrations. Key features include:

1. **Modular Design**: Reusable components for common patterns
2. **Responsive UI**: Works across desktop and mobile
3. **Accessibility**: WCAG 2.1 AA compliant
4. **Performance**: Optimized for large data sets
5. **Security**: Proper data handling and validation
6. **Maintainability**: Clear component boundaries and documentation