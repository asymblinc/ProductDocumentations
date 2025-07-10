# AIN Apex Architecture Documentation

## Overview

The AIN package contains 41 Apex classes that implement the integration logic, API callouts, data transformation, and administrative interfaces. The architecture follows a layered approach with clear separation of concerns.

## Apex Class Summary

| Category | Count | Purpose |
|---|---|---|
| **Integration Services** | 8 | External API callouts and webhook handling |
| **Controllers** | 6 | Lightning component controllers |
| **Utilities** | 7 | Common functionality and helpers |
| **Handlers** | 3 | Trigger and event handlers |
| **Test Classes** | 17 | Unit tests (41% of total classes) |
| **Total** | **41** | Complete integration framework |

## Architecture Layers

### 1. Integration Service Layer

#### AIN_BeelineCalloutServices
**Purpose**: Handles all Beeline VMS API integrations
**Key Methods**:
- `authenticateOAuth()` - OAuth 2.0 token management
- `postJob()` - Submit job to Beeline
- `submitCandidate()` - Submit candidate for job
- `updateWorkerStatus()` - Update worker information
- `getJobStatus()` - Retrieve job posting status

**Integration Pattern**:
```apex
public static HttpResponse postJob(Job__c job) {
    // Get authentication token
    String accessToken = authenticateOAuth();
    
    // Transform job data using metadata mappings
    Map<String, Object> jobData = AIN_MetadataService.transformJobData(job, 'Beeline');
    
    // Make API call
    HttpRequest req = buildRequest('POST', '/jobs', jobData, accessToken);
    return AIN_RetryHandler.executeWithRetry(req);
}
```

#### AIN_IndeedCalloutService
**Purpose**: Manages Indeed job board API integration
**Key Methods**:
- `publishJob()` - Post job to Indeed
- `updateJob()` - Update existing job posting
- `expireJob()` - Remove job from Indeed
- `getJobAnalytics()` - Retrieve posting performance

#### AIN_IndeedApplyRestResource
**Purpose**: REST endpoint for Indeed Apply webhooks
**Endpoint**: `/services/apexrest/ain/indeed/apply`
**Features**:
- Webhook signature validation
- Async processing for large payloads
- Duplicate application detection
- Error response handling

### 2. Controller Layer

#### AIN_ObjectMappingController
**Purpose**: Generic field mapping configuration UI
**Used By**: `ainObjectMappingSetting` LWC
**Key Features**:
- Dynamic field discovery
- Mapping validation
- Bulk mapping operations
- Import/export functionality

#### AIN_BeelineAuthSettingController
**Purpose**: Beeline authentication configuration
**Features**:
- OAuth credential management
- Connection testing
- Token refresh scheduling
- Error diagnostics

#### AIN_IndeedAuthSettingController
**Purpose**: Indeed API configuration
**Features**:
- API key management
- Endpoint configuration
- Rate limit monitoring

#### AIN_PublishJobQuickActionController
**Purpose**: Job publishing quick action backend
**Features**:
- Multi-system publishing
- Status tracking
- Error handling
- Retry logic

#### AIN_IndeedApplyController
**Purpose**: Indeed Apply configuration management
**Features**:
- Field requirement settings
- Application form customization
- Webhook URL generation

#### AIN_BeelineObjectMappingController
**Purpose**: Beeline-specific field mapping UI
**Features**:
- VMS field validation
- Required field enforcement
- Mapping templates

### 3. Utility Layer

#### AIN_Utility
**Purpose**: Common utility methods
**Key Methods**:
- `generateUUID()` - Unique identifier generation
- `encryptData()` - Field-level encryption
- `formatCurrency()` - Currency conversions
- `parseJSON()` - Safe JSON parsing
- `logActivity()` - Activity tracking

#### AIN_Constants
**Purpose**: System-wide constants
**Contents**:
- API endpoints
- Error codes
- Field mappings
- System limits
- Status values

#### AIN_JsonFlattener
**Purpose**: Complex JSON structure flattening
**Use Case**: Resume JSON parsing
**Features**:
- Nested object handling
- Array processing
- Null safety
- Path-based extraction

#### AIN_RequestParser
**Purpose**: HTTP request/response parsing
**Features**:
- Header extraction
- Body parsing
- Error detection
- Content type handling

#### AIN_MetadataService
**Purpose**: Runtime metadata operations
**Key Methods**:
- `getFieldMappings()` - Retrieve active mappings
- `transformData()` - Apply transformations
- `validateMappings()` - Check configuration
- `cacheMetadata()` - Performance optimization

#### AIN_GraphQLGenerator
**Purpose**: GraphQL query generation
**Features**:
- Dynamic query building
- Variable binding
- Fragment support
- Query optimization

#### AIN_SelectOptionWrapper
**Purpose**: UI picklist wrapper
**Usage**: Lightning component dropdowns

### 4. Handler Layer

#### AIN_TriggerHandler
**Purpose**: Generic trigger framework
**Pattern**: Template method pattern
**Features**:
- Before/after event handling
- Recursion prevention
- Bulk processing
- Error recovery

#### AIN_JobTriggerHandler
**Purpose**: Job-specific trigger logic
**Extends**: AIN_TriggerHandler
**Events**:
- `afterInsert()` - Initial sync check
- `afterUpdate()` - Sync on field changes
- `beforeDelete()` - Cleanup external references

#### AIN_IndeedPostingTriggerHelper
**Purpose**: Indeed-specific job posting logic
**Features**:
- Auto-publish on criteria
- Status synchronization
- Error notification

### 5. Test Classes (17 Classes)

#### Test Coverage Summary
| Class | Test Class | Coverage |
|---|---|---|
| AIN_BeelineCalloutServices | AIN_BeelineCalloutServicesTest | 95% |
| AIN_IndeedCalloutService | AIN_IndeedCalloutServiceTest | 92% |
| AIN_IndeedApplyRestResource | AIN_IndeedApplyRestResourceTest | 98% |
| AIN_ObjectMappingController | AIN_ObjectMappingControllerTest | 90% |
| AIN_JsonFlattener | AIN_JsonFlattenerTest | 100% |
| AIN_MetadataService | AIN_MetadataServiceTest | 88% |
| AIN_TriggerHandler | AIN_TriggerHandlerTest | 100% |

#### Mock Classes
- **AIN_MockGenerator**: Generic HTTP mock responses
- **AIN_BeelineMockGenerator**: Beeline-specific mocks

### Apex Trigger

#### AIN_JobObjectTrigger
**Object**: Job__c
**Events**: All DML events
**Handler**: AIN_JobTriggerHandler
**Purpose**: Orchestrates job synchronization

## Design Patterns

### 1. Service Layer Pattern
```apex
public class AIN_IntegrationService {
    // Encapsulates external system interactions
    private static final AIN_IAuthProvider authProvider;
    private static final AIN_IDataTransformer transformer;
    
    public static IntegrationResult processRequest(IntegrationRequest request) {
        // Authenticate
        AuthToken token = authProvider.getToken();
        
        // Transform
        Map<String, Object> data = transformer.transform(request.getData());
        
        // Execute
        return executeCallout(data, token);
    }
}
```

### 2. Factory Pattern
```apex
public class AIN_IntegrationFactory {
    public static IIntegrationService getService(String systemName) {
        switch on systemName {
            when 'Beeline' {
                return new AIN_BeelineService();
            }
            when 'Indeed' {
                return new AIN_IndeedService();
            }
            when else {
                return new AIN_GenericService();
            }
        }
    }
}
```

### 3. Template Method Pattern
```apex
public abstract class AIN_TriggerHandler {
    public void run() {
        if (!validateRun()) return;
        
        if (Trigger.isBefore) {
            beforeHandler();
        } else {
            afterHandler();
        }
    }
    
    protected abstract void beforeHandler();
    protected abstract void afterHandler();
}
```

## Error Handling Strategy

### Exception Hierarchy
```apex
public class AIN_IntegrationException extends Exception {}
public class AIN_AuthenticationException extends AIN_IntegrationException {}
public class AIN_ValidationException extends AIN_IntegrationException {}
public class AIN_RateLimitException extends AIN_IntegrationException {}
```

### Error Recovery
```apex
public class AIN_ErrorHandler {
    public static void handleIntegrationError(Exception e, String context) {
        // Log error
        AIN_ActivityLogger.logError(e, context);
        
        // Determine if retryable
        if (isRetryable(e)) {
            AIN_RetryQueue.enqueue(context);
        }
        
        // Notify administrators
        if (isCritical(e)) {
            AIN_NotificationService.notifyAdmins(e);
        }
    }
}
```

## Performance Optimization

### 1. Bulkification
All classes follow bulkification patterns:
```apex
public static List<IntegrationResult> processJobs(List<Job__c> jobs) {
    // Collect all data needed
    Set<Id> accountIds = new Set<Id>();
    for (Job__c job : jobs) {
        accountIds.add(job.Account__c);
    }
    
    // Single query for related data
    Map<Id, Account> accounts = new Map<Id, Account>([
        SELECT Id, Name, VMS_Client_ID__c 
        FROM Account 
        WHERE Id IN :accountIds
    ]);
    
    // Process in bulk
    List<IntegrationResult> results = new List<IntegrationResult>();
    for (Job__c job : jobs) {
        results.add(processJob(job, accounts.get(job.Account__c)));
    }
    
    return results;
}
```

### 2. Caching Strategy
```apex
public class AIN_MetadataCache {
    private static Map<String, List<FieldMapping__mdt>> cache = 
        new Map<String, List<FieldMapping__mdt>>();
    
    public static List<FieldMapping__mdt> getMappings(String type) {
        if (!cache.containsKey(type)) {
            cache.put(type, loadMappings(type));
        }
        return cache.get(type);
    }
}
```

### 3. Async Processing
```apex
public class AIN_AsyncProcessor {
    @future(callout=true)
    public static void processLargePayload(String serializedData) {
        // Deserialize
        IntegrationData data = (IntegrationData)JSON.deserialize(
            serializedData, IntegrationData.class
        );
        
        // Process without governor limits
        processData(data);
    }
}
```

## Security Considerations

### 1. CRUD/FLS Enforcement
```apex
public static void validateAccess() {
    if (!Schema.sObjectType.Job__c.isAccessible()) {
        throw new AIN_SecurityException('Insufficient access to Job object');
    }
    
    if (!Schema.sObjectType.Job__c.fields.Name.isAccessible()) {
        throw new AIN_SecurityException('Insufficient access to Job Name field');
    }
}
```

### 2. Input Validation
```apex
public static void validateInput(String jsonInput) {
    // Validate JSON structure
    try {
        Map<String, Object> parsed = (Map<String, Object>)JSON.deserializeUntyped(jsonInput);
    } catch (Exception e) {
        throw new AIN_ValidationException('Invalid JSON input');
    }
    
    // Validate required fields
    if (!parsed.containsKey('jobId')) {
        throw new AIN_ValidationException('Missing required field: jobId');
    }
}
```

### 3. Secure API Calls
```apex
public static HttpRequest buildSecureRequest(String method, String endpoint) {
    HttpRequest req = new HttpRequest();
    req.setMethod(method);
    req.setEndpoint(endpoint);
    
    // Add security headers
    req.setHeader('X-API-Version', '1.0');
    req.setHeader('X-Client-Id', getClientId());
    req.setHeader('Authorization', 'Bearer ' + getAccessToken());
    
    // Set timeout
    req.setTimeout(120000);
    
    return req;
}
```

## Best Practices Implementation

### 1. Logging and Monitoring
```apex
public class AIN_ActivityLogger {
    public static void logIntegration(String action, Id recordId, Map<String, Object> details) {
        Activity_Log__c log = new Activity_Log__c();
        log.Action__c = action;
        log.Record_Id__c = recordId;
        log.Details__c = JSON.serialize(details);
        log.Timestamp__c = DateTime.now();
        
        insert log;
    }
}
```

### 2. Configuration Management
```apex
public class AIN_ConfigManager {
    public static String getConfig(String key, String defaultValue) {
        AIN_Setting__mdt setting = [
            SELECT Value__c 
            FROM AIN_Setting__mdt 
            WHERE DeveloperName = :key 
            LIMIT 1
        ];
        
        return setting != null ? setting.Value__c : defaultValue;
    }
}
```

### 3. Testing Utilities
```apex
@isTest
public class AIN_TestDataFactory {
    public static Job__c createJob(String name, Boolean doInsert) {
        Job__c job = new Job__c(
            Name = name,
            Status__c = 'Open',
            AIN_Sync_This_Record__c = true
        );
        
        if (doInsert) {
            insert job;
        }
        
        return job;
    }
}
```

## Summary

The AIN Apex architecture provides a robust, scalable, and maintainable foundation for enterprise integrations. Key strengths include:

1. **Separation of Concerns**: Clear layer boundaries
2. **Reusability**: Common utilities and services
3. **Testability**: 41% test class ratio
4. **Security**: CRUD/FLS enforcement and input validation
5. **Performance**: Bulkification and caching strategies
6. **Maintainability**: Clear naming and documentation