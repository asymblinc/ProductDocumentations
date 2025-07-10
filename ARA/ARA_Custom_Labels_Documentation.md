# ARA Custom Labels Documentation

## Executive Summary

The ARA (Asymbl Recruiting Agent) package utilizes 24 custom labels to provide consistent, localized, and user-friendly messaging throughout the AI-powered recruiting system. These labels support both the Candidate Portal (CP) module and the core ARA recruiting automation module, ensuring proper error handling, success notifications, and system status communication.

## Table of Contents

1. [Overview](#overview)
2. [Label Categories](#label-categories)
3. [CP Module Labels (6 Labels)](#cp-module-labels-6-labels)
4. [ARA Module Labels (18 Labels)](#ara-module-labels-18-labels)
5. [Error Handling Patterns](#error-handling-patterns)
6. [Implementation Examples](#implementation-examples)
7. [Localization Considerations](#localization-considerations)
8. [Best Practices](#best-practices)

---

## Overview

### Purpose
Custom labels in the ARA package serve multiple critical functions:
- **User Communication**: Provide clear, consistent messages to end users
- **Error Handling**: Deliver meaningful error messages for troubleshooting
- **Localization**: Enable multi-language support for global deployments
- **Maintainability**: Centralize message management for easy updates
- **Consistency**: Ensure uniform messaging across all components

### Naming Convention
- **CP_** prefix: Candidate Portal module labels
- **ARA_** prefix: Core recruiting agent labels
- **_Label** suffix: Indicates status/action messages
- Descriptive names using underscores for word separation

---

## Label Categories

### 1. Success Messages (3 labels)
Confirm successful operations to users

### 2. Error Messages (15 labels)
Communicate various error conditions

### 3. Validation Messages (4 labels)
Inform about data validation issues

### 4. System Status Messages (2 labels)
Provide system state information

---

## CP Module Labels (6 Labels)

### 1. CP_ATS_Applicant_Created_Successfully_Label
**Purpose**: Confirms successful creation of applicant record in ATS  
**Default Text**: "Applicant has been successfully added to the job."  
**Usage Context**: 
- Class: `CP_AddApplicantToJob`
- Displayed after successful job application submission
**Message Type**: User-facing success message  
**Severity**: Information  
**Localization Notes**: Keep message concise and positive

### 2. CP_All_Contact_Records_Not_Found_Label
**Purpose**: Indicates some contact records could not be found  
**Default Text**: "Some candidate records could not be found. Please verify the candidate information."  
**Usage Context**:
- Class: `CP_AddApplicantToJob`
- Batch processing when partial records are missing
**Message Type**: User-facing warning  
**Severity**: Warning  
**Recovery**: Suggest user to verify candidate data

### 3. CP_Contact_Records_Not_Found_Label
**Purpose**: No contact records found for the operation  
**Default Text**: "No candidate records found. Please ensure candidates exist in the system."  
**Usage Context**:
- Class: `CP_AddApplicantToJob`
- When candidate lookup returns empty
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Direct user to candidate creation process

### 4. CP_Error_Occured_Label
**Purpose**: Generic error message for unexpected failures  
**Default Text**: "An error occurred while processing your request. Please try again or contact support."  
**Usage Context**:
- All CP classes as fallback error handler
- Catch-all for unexpected exceptions
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Suggest retry or support contact

### 5. CP_Job_Doesnt_Has_Stage_Label
**Purpose**: Job requisition missing required stage configuration  
**Default Text**: "The selected job does not have any stages configured. Please contact your administrator."  
**Usage Context**:
- Class: `CP_AddApplicantToJob`
- Validation before adding applicant
**Message Type**: System configuration error  
**Severity**: Error  
**Recovery**: Requires admin intervention

### 6. CP_Job_Records_Not_Found_Label
**Purpose**: Specified job records do not exist  
**Default Text**: "The specified job could not be found. Please verify the job is active."  
**Usage Context**:
- Class: `CP_AddApplicantToJob`
- Job lookup validation
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Verify job ID and status

---

## ARA Module Labels (18 Labels)

### Email-Related Labels

#### 1. ARA_Email_Not_Found
**Purpose**: Email address not found for recipient  
**Default Text**: "Email address not found for the selected recipient."  
**Usage Context**:
- Class: `ARA_EmailUtility`
- Email send validation
**Message Type**: Data validation error  
**Severity**: Error  
**Recovery**: Update contact with email address

#### 2. ARA_Email_Scheduled_Message
**Purpose**: Confirms email scheduling success  
**Default Text**: "Email has been successfully scheduled."  
**Usage Context**:
- Class: `ARA_ScheduledEmailSender`
- After queuing email for delivery
**Message Type**: User-facing success  
**Severity**: Information  

#### 3. ARA_Email_Send
**Purpose**: Confirms immediate email send  
**Default Text**: "Email has been sent successfully."  
**Usage Context**:
- Class: `ARA_EmailUtility`
- After successful email delivery
**Message Type**: User-facing success  
**Severity**: Information  

#### 4. ARA_Email_Template_Not_Found
**Purpose**: Required email template missing  
**Default Text**: "Email template not found. Please contact your administrator."  
**Usage Context**:
- Class: `ARA_EmailUtility`
- Template lookup failure
**Message Type**: System configuration error  
**Severity**: Error  
**Recovery**: Admin needs to verify template deployment

#### 5. ARA_Error_While_Sending_Email
**Purpose**: Email send operation failed  
**Default Text**: "Unable to send email. Please check the recipient's email address and try again."  
**Usage Context**:
- Class: `ARA_EmailUtility`
- Email API failure handling
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Verify email configuration and retry

### Validation Labels

#### 6. ARA_Empty_Applicants
**Purpose**: No applicants provided for operation  
**Default Text**: "No candidates selected. Please select at least one candidate."  
**Usage Context**:
- Class: `ARA_SubmitCandidatesToManager`
- Input validation
**Message Type**: User-facing validation error  
**Severity**: Warning  
**Recovery**: User action required to select candidates

#### 7. ARA_Empty_Job_Name
**Purpose**: Job name is required but missing  
**Default Text**: "Job name is required. Please provide a valid job name."  
**Usage Context**:
- Multiple classes for job validation
**Message Type**: User-facing validation error  
**Severity**: Error  
**Recovery**: Provide required field

#### 8. ARA_Empty_List_of_Interviewers
**Purpose**: No interviewers selected for scheduling  
**Default Text**: "No interviewers selected. Please select at least one interviewer."  
**Usage Context**:
- Class: `ARA_ScheduleInterview`
- Interview scheduling validation
**Message Type**: User-facing validation error  
**Severity**: Error  
**Recovery**: Select interviewers before proceeding

### Interview Scheduling Labels

#### 9. ARA_Error_Interview_Scheduling_Label
**Purpose**: Interview scheduling operation failed  
**Default Text**: "Unable to schedule interview. Please check availability and try again."  
**Usage Context**:
- Class: `ARA_ScheduleInterview`
- Scheduling conflict or error
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Check calendars and retry

#### 10. ARA_Interview_Scheduled_Label
**Purpose**: Confirms successful interview scheduling  
**Default Text**: "Interview has been successfully scheduled."  
**Usage Context**:
- Class: `ARA_ScheduleInterview`
- Success confirmation
**Message Type**: User-facing success  
**Severity**: Information  

#### 11. ARA_Invalid_Date_Time
**Purpose**: Invalid date/time format or value  
**Default Text**: "Invalid date or time provided. Please use a valid future date and time."  
**Usage Context**:
- Class: `ARA_ScheduleInterview`
- Date validation
**Message Type**: User-facing validation error  
**Severity**: Error  
**Recovery**: Correct date/time format

#### 12. ARA_Invalid_Interview
**Purpose**: Interview record is invalid or corrupted  
**Default Text**: "Invalid interview record. Please contact support."  
**Usage Context**:
- Interview-related operations
- Data integrity check failure
**Message Type**: System error  
**Severity**: Error  
**Recovery**: Support intervention required

### Data Validation Labels

#### 13. ARA_Improper_Job_Candidate_Name
**Purpose**: Job or candidate name format invalid  
**Default Text**: "Invalid job or candidate name format. Please check and correct the information."  
**Usage Context**:
- Input validation across multiple classes
**Message Type**: User-facing validation error  
**Severity**: Warning  
**Recovery**: Correct data format

#### 14. ARA_Feedback_Not_Shared
**Purpose**: Interview feedback not yet provided  
**Default Text**: "Interview feedback has not been shared. Please ensure all interviewers submit their feedback."  
**Usage Context**:
- Class: `ARA_CandidatesSummarisedFeedback`
- Feedback collection validation
**Message Type**: User-facing information  
**Severity**: Warning  
**Recovery**: Follow up with interviewers

### Record Not Found Labels

#### 15. ARA_Candidate_Not_Found
**Purpose**: Candidate record lookup failed  
**Default Text**: "Candidate record not found. Please verify the candidate exists in the system."  
**Usage Context**:
- All classes requiring candidate data
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Verify candidate ID

#### 16. ARA_Job_Not_Found
**Purpose**: Job requisition lookup failed  
**Default Text**: "Job requisition not found. Please verify the job is active."  
**Usage Context**:
- All classes requiring job data
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Verify job ID and status

#### 17. ARA_Candidate_Not_On_Job
**Purpose**: Candidate not associated with specified job  
**Default Text**: "Candidate is not associated with this job. Please verify the job application."  
**Usage Context**:
- Validation of candidate-job relationship
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Check application status

### System Error Labels

#### 18. ARA_Error_Occurred
**Purpose**: Generic system error message  
**Default Text**: "An unexpected error occurred. Please try again or contact support if the issue persists."  
**Usage Context**:
- All classes as catch-all error handler
- Used in plugin error handling
**Message Type**: User-facing error  
**Severity**: Error  
**Recovery**: Retry or escalate to support

---

## Error Handling Patterns

### 1. Try-Catch Pattern with Custom Labels

```apex
public class ARA_ExampleClass {
    public static Map<String, Object> processRequest(Map<String, Object> params) {
        Map<String, Object> response = new Map<String, Object>();
        
        try {
            // Validate required parameters
            if (!params.containsKey('candidateId')) {
                response.put('success', false);
                response.put('error', Label.ARA_Candidate_Not_Found);
                return response;
            }
            
            // Business logic
            processCandidate(params);
            
            response.put('success', true);
            response.put('message', Label.ARA_Email_Send);
            
        } catch (EmailException e) {
            response.put('success', false);
            response.put('error', Label.ARA_Error_While_Sending_Email);
            logError('Email Send Failed', e);
            
        } catch (Exception e) {
            response.put('success', false);
            response.put('error', Label.ARA_Error_Occurred);
            logError('Unexpected Error', e);
        }
        
        return response;
    }
}
```

### 2. Validation Pattern with Labels

```apex
public class ValidationHelper {
    public static ValidationResult validateJobApplication(Id candidateId, Id jobId) {
        ValidationResult result = new ValidationResult();
        
        // Check candidate exists
        List<Contact> candidates = [SELECT Id FROM Contact WHERE Id = :candidateId];
        if (candidates.isEmpty()) {
            result.addError(Label.ARA_Candidate_Not_Found);
            return result;
        }
        
        // Check job exists
        List<Job__c> jobs = [SELECT Id, Has_Stages__c FROM Job__c WHERE Id = :jobId];
        if (jobs.isEmpty()) {
            result.addError(Label.ARA_Job_Not_Found);
            return result;
        }
        
        // Check job has stages
        if (!jobs[0].Has_Stages__c) {
            result.addError(Label.CP_Job_Doesnt_Has_Stage_Label);
            return result;
        }
        
        result.setValid(true);
        return result;
    }
}
```

### 3. Plugin Error Handling

```apex
public class PluginErrorHandler {
    public static PluginResponse handleError(Exception e, String context) {
        ErrorType errorType = classifyError(e);
        
        switch on errorType {
            when FUNCTION_NOT_FOUND {
                return new PluginResponse(
                    false, 
                    Label.ARA_Function_Not_Found,
                    'FUNCTION_404'
                );
            }
            when RATE_LIMIT_EXCEEDED {
                return new PluginResponse(
                    false,
                    Label.ARA_Rate_Limit_Exceeded,
                    'RATE_LIMIT_429'
                );
            }
            when INVALID_PARAMETERS {
                return new PluginResponse(
                    false,
                    Label.ARA_Invalid_Parameters,
                    'INVALID_400'
                );
            }
            when else {
                return new PluginResponse(
                    false,
                    Label.ARA_Error_Occurred,
                    'ERROR_500'
                );
            }
        }
    }
}
```

---

## Implementation Examples

### 1. Email Utility Usage

```apex
public class ARA_EmailUtility {
    public static void sendEmail(String templateName, Id targetId, Id whatId) {
        try {
            // Validate email address exists
            Contact recipient = [SELECT Email FROM Contact WHERE Id = :targetId];
            if (String.isBlank(recipient.Email)) {
                throw new EmailException(Label.ARA_Email_Not_Found);
            }
            
            // Get template
            EmailTemplate template = getTemplate(templateName);
            if (template == null) {
                throw new EmailException(Label.ARA_Email_Template_Not_Found);
            }
            
            // Send email
            Messaging.sendEmail(new List<Messaging.SingleEmailMessage>{
                createEmailMessage(template, targetId, whatId)
            });
            
            // Success message
            showToast('Success', Label.ARA_Email_Send);
            
        } catch (Exception e) {
            showToast('Error', Label.ARA_Error_While_Sending_Email);
            logError(e);
        }
    }
}
```

### 2. Interview Scheduling Implementation

```apex
public class ARA_ScheduleInterview {
    @InvocableMethod(label='Schedule Interview' description='AI-powered interview scheduling')
    public static List<ScheduleResult> scheduleInterview(List<ScheduleRequest> requests) {
        List<ScheduleResult> results = new List<ScheduleResult>();
        
        for (ScheduleRequest request : requests) {
            ScheduleResult result = new ScheduleResult();
            
            try {
                // Validate inputs
                if (request.interviewers == null || request.interviewers.isEmpty()) {
                    result.success = false;
                    result.errorMessage = Label.ARA_Empty_List_of_Interviewers;
                    results.add(result);
                    continue;
                }
                
                if (request.scheduledTime < Datetime.now()) {
                    result.success = false;
                    result.errorMessage = Label.ARA_Invalid_Date_Time;
                    results.add(result);
                    continue;
                }
                
                // Schedule interview
                Interview__c interview = createInterview(request);
                insert interview;
                
                result.success = true;
                result.message = Label.ARA_Interview_Scheduled_Label;
                result.interviewId = interview.Id;
                
            } catch (Exception e) {
                result.success = false;
                result.errorMessage = Label.ARA_Error_Interview_Scheduling_Label;
                logError('Interview Scheduling Failed', e);
            }
            
            results.add(result);
        }
        
        return results;
    }
}
```

### 3. Candidate Submission Flow

```apex
public class ARA_SubmitCandidatesToManager {
    public static SubmissionResult submitCandidates(List<Id> candidateIds, Id jobId) {
        SubmissionResult result = new SubmissionResult();
        
        try {
            // Validate candidates
            if (candidateIds == null || candidateIds.isEmpty()) {
                result.addError(Label.ARA_Empty_Applicants);
                return result;
            }
            
            // Validate job
            Job__c job = getJob(jobId);
            if (job == null) {
                result.addError(Label.ARA_Job_Not_Found);
                return result;
            }
            
            if (String.isBlank(job.Name)) {
                result.addError(Label.ARA_Empty_Job_Name);
                return result;
            }
            
            // Check candidate associations
            Map<Id, Job_Application__c> applications = getApplications(candidateIds, jobId);
            for (Id candidateId : candidateIds) {
                if (!applications.containsKey(candidateId)) {
                    result.addWarning(
                        String.format(Label.ARA_Candidate_Not_On_Job, 
                        new List<String>{candidateId})
                    );
                }
            }
            
            // Submit to manager
            submitToManager(applications.values(), job);
            
            result.setSuccess(true);
            result.setMessage(Label.CP_ATS_Applicant_Created_Successfully_Label);
            
        } catch (Exception e) {
            result.addError(Label.ARA_Error_Occurred);
            logError('Candidate Submission Failed', e);
        }
        
        return result;
    }
}
```

---

## Localization Considerations

### 1. Translation Guidelines

- **Maintain Consistency**: Use consistent terminology across all labels
- **Context Awareness**: Ensure translations consider the usage context
- **Length Constraints**: Keep translations concise for UI compatibility
- **Cultural Sensitivity**: Adapt messages for cultural appropriateness

### 2. Multi-Language Support Structure

```xml
<!-- CustomLabels.labels-meta.xml -->
<CustomLabels xmlns="http://soap.sforce.com/2006/04/metadata">
    <labels>
        <fullName>ARA_Email_Send</fullName>
        <language>en_US</language>
        <protected>true</protected>
        <shortDescription>Email sent confirmation</shortDescription>
        <value>Email has been sent successfully.</value>
    </labels>
    <labels>
        <fullName>ARA_Email_Send</fullName>
        <language>es</language>
        <protected>true</protected>
        <shortDescription>Email sent confirmation</shortDescription>
        <value>El correo electrónico se ha enviado correctamente.</value>
    </labels>
    <labels>
        <fullName>ARA_Email_Send</fullName>
        <language>fr</language>
        <protected>true</protected>
        <shortDescription>Email sent confirmation</shortDescription>
        <value>L'e-mail a été envoyé avec succès.</value>
    </labels>
</CustomLabels>
```

### 3. Dynamic Language Selection

```apex
public class LocalizationHelper {
    public static String getLocalizedMessage(String labelName) {
        // Get user's language preference
        String userLanguage = UserInfo.getLanguage();
        
        // Return appropriate label based on language
        return Label.get('', labelName);
    }
    
    public static String formatMessage(String labelName, List<String> params) {
        String message = getLocalizedMessage(labelName);
        
        // Replace placeholders with parameters
        for (Integer i = 0; i < params.size(); i++) {
            message = message.replace('{' + i + '}', params[i]);
        }
        
        return message;
    }
}
```

---

## Best Practices

### 1. Label Usage Guidelines

**DO:**
- Use labels for all user-facing messages
- Provide clear, actionable error messages
- Include recovery suggestions in error labels
- Keep messages concise and professional
- Use consistent tone and terminology

**DON'T:**
- Hard-code messages in Apex classes
- Use technical jargon in user messages
- Create duplicate labels for similar messages
- Include sensitive information in labels
- Use labels for debug or log messages

### 2. Error Message Structure

Follow this template for error messages:
1. **What happened**: Clear statement of the issue
2. **Why it happened**: Brief explanation if helpful
3. **What to do**: Actionable recovery steps

Example:
```
"Unable to schedule interview. The selected time conflicts with another booking. Please choose a different time slot."
```

### 3. Success Message Guidelines

- Be specific about what succeeded
- Avoid redundant information
- Keep positive and professional tone
- Include next steps if applicable

Example:
```
"Interview scheduled successfully. Calendar invitations have been sent to all participants."
```

### 4. Maintenance Best Practices

1. **Regular Review**: Audit labels quarterly for relevance
2. **Consistency Check**: Ensure similar scenarios use consistent messaging
3. **Translation Updates**: Keep all languages synchronized
4. **Usage Tracking**: Monitor which labels are actually used
5. **Deprecation Process**: Mark obsolete labels before removal

### 5. Testing Considerations

```apex
@isTest
private class LabelTestCoverage {
    @isTest
    static void testErrorLabelCoverage() {
        // Test each error scenario
        Test.startTest();
        
        // Test missing candidate
        try {
            ARA_Helper.validateCandidate(null);
        } catch (ValidationException e) {
            System.assertEquals(Label.ARA_Candidate_Not_Found, e.getMessage());
        }
        
        // Test missing email
        try {
            ARA_EmailUtility.validateEmail(null);
        } catch (EmailException e) {
            System.assertEquals(Label.ARA_Email_Not_Found, e.getMessage());
        }
        
        Test.stopTest();
    }
}
```

---

## Error Severity Levels and User Experience

### Severity Classification

1. **Information**: Success confirmations and status updates
   - Display: Green toast/banner
   - Duration: 3 seconds
   - Examples: Email sent, Interview scheduled

2. **Warning**: Non-critical issues that allow continuation
   - Display: Yellow toast/banner
   - Duration: 5 seconds
   - Examples: Some records not found, Feedback not shared

3. **Error**: Critical issues preventing operation
   - Display: Red toast/banner
   - Duration: Until dismissed
   - Examples: Missing required data, System errors

### User Notification Patterns

```apex
public class NotificationHelper {
    public static void showNotification(String label, NotificationType type) {
        switch on type {
            when SUCCESS {
                showToast('Success', label, 'success', 'dismissable');
            }
            when WARNING {
                showToast('Warning', label, 'warning', 'dismissable');
            }
            when ERROR {
                showToast('Error', label, 'error', 'sticky');
            }
            when INFO {
                showToast('Information', label, 'info', 'dismissable');
            }
        }
    }
}
```

---

## Conclusion

The 24 custom labels in the ARA package form a critical component of the user experience, providing clear communication throughout the AI-powered recruiting process. By following the documented patterns and best practices, developers can ensure consistent, localized, and user-friendly messaging that enhances the overall effectiveness of the recruiting automation system.

These labels support both technical requirements (error handling, validation) and business needs (user communication, process confirmation), making them essential for the package's success in production environments.