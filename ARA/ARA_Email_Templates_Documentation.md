# ARA (Asymbl Copilot) Email Templates Documentation

*Regenerated 2026-10-30 against `ara` repo, branch `master`, commit `ba1083bd605581d3da5ca881a9e10db9e86fb878`.*

## Correction Notice

The prior version documented 4 email templates in a single `ARA` folder. The actual count is **19**, across **2** email folders: `ARA` (15 templates) and `ARA_BaseEmailTemplates` (4 templates). All are of `type=custom` (classic merge-field HTML templates, not Lightning email templates).

## Folder: ARA (15 templates)

| Template (DeveloperName) | Subject | Purpose |
|---|---|---|
| `ARA_Candidate_Submission_Email_Template` | Candidate Submission for [Job_Name] | Candidate submission to hiring manager (legacy/original) |
| `Submit_Candidate_Email_Template` | Candidate Submissions — [Job_Name] Role | Candidate submission to hiring manager (current) |
| `Submit_Candidate_To_Hiring_Manager` | Candidate Submissions — [Job_Name] Role | Candidate submission to hiring manager (current, duplicate/variant of above) |
| `ARA_Offer_Draft_Template` | Offer Letter for [Job_Title] | AI-drafted offer letter body |
| `ARA_Reminder_Email_Template` | Reminder: Interview for [Job_Name] | Interview reminder (legacy) |
| `ARA_Send_Reminder_Email_Template` | Reminder: Interview for [Job_Name] | Interview reminder (current) |
| `ARA_Interview_Booked_Notify` | Interview Booked: [Candidate_Name] → [Interview_Date] | Confirms a successfully booked interview |
| `ARA_Unsuccessful_Booking_Notification` | Follow-up required - [Candidate_Name] interview not booked | Alerts recruiter when automated booking fails |
| `ARA_Screen_Call_Pre_Notification` | Screening initiated for [Job_Title] — [Failed_Count] candidates could not be started | Sent when a screening call batch begins, surfaces candidates that couldn't be started |
| `ARA_Screen_Call_Batch_Completed` | Screening initiated for [Job_Title] — [Failed_Count] candidates could not be started | Sent when `ARA_ScreeningCallBatch` finishes; summary + attached report of failed calls |
| `ARA_Screening_Summary_Result` | [Total_Count] candidates screened for [Job_Title] | Sent when `ARA_ScreeningCallSummaryBatch` completes; total/success/failure counts with CSV report attached |
| `ARA_Voice_Interview_Confirmation_Call_Disconnected_To_Candidate` | Interview Confirmation – Call Disconnected | Notifies candidate their confirmation call disconnected |
| `ARA_Voice_Interview_Confirmation_Call_Disconnected_To_Recruiter` | Interview booked (verify receipt) - [Candidate_Name] / [Job_Title] | Notifies recruiter the call disconnected but interview was still booked; asks them to verify |
| `ARA_Voice_Interview_Failure_Notification` | Follow-up required - [Candidate_Name] interview not booked | Notifies recruiter to follow up manually after voice-agent scheduling failure |
| `ARA_Screening_Email_Template` / `ARA_Screening_SMS_Template` | (present on disk, not yet in `master`'s package.xml — see Note below) | Screening-related email/SMS templates currently mid-development |

**Note on working-tree vs. `master`**: `ARA_Screening_Email_Template.email-meta.xml` and `ARA_Screening_SMS_Template.email-meta.xml` exist in the local `ara` working tree but are **not present in the `master` commit** (`git show master:<path>` fails for both). They should be treated as in-flight/unreleased work on a feature branch, not yet part of `master`'s shipped inventory, and are excluded from the 19-template total above and from `manifest/package.xml`.

## Folder: ARA_BaseEmailTemplates (4 templates)

These are general-purpose recruiting-marketing templates, distinct from the transactional/automation templates in the `ARA` folder — likely intended as starting points for recruiters drafting outbound candidate communications via the Email_Management or Recruitment_Marketing plugins rather than being sent by Apex automation directly.

| Template (DeveloperName) | Subject |
|---|---|
| `Holiday_Seasonal_Greetings` | Season's Greetings and 2025 Outlook |
| `Industry_Event_Networking_Invitation` | Invitation - {EventName} networking event |
| `Initial_Application_Acknowledgment` | Thank you for your application - {CompanyName} {RoleName} |
| `Welcome_Onboard_Email_Template` | Welcome aboard for {Role_Name} |

## Voice Interview Subsystem Coverage (net-new)

6 of the 19 templates (`ARA_Interview_Booked_Notify`, `ARA_Unsuccessful_Booking_Notification`, `ARA_Screen_Call_Pre_Notification`, `ARA_Screen_Call_Batch_Completed`, `ARA_Screening_Summary_Result`, both `ARA_Voice_Interview_Confirmation_Call_Disconnected_*` templates, and `ARA_Voice_Interview_Failure_Notification`) exist solely to support the Voice Interview / Voice Agent subsystem documented in `ARA_Package_Overview.md` and `ERD_ARA_Recruiting_Objects.md`. None of these appeared in the prior (4-template) documentation.

## Integration Points

- `Submit_Candidate_To_Hiring_Manager` is referenced by the `ARA_CandidateSubmissionToHM` / `ARA_CandidateSubmission_To_HMAction` GenAiFunction flow (Interview_Management plugin).
- `ARA_Offer_Draft_Template` is referenced by `ARA_DraftOfferEmail` / `ARA_GenerateOfferEmail` (Recruiting plugin).
- `ARA_Send_Reminder_Email_Template` is referenced by `ARA_ScheduleReminderEmail` / `ARA_GetInterviewsForReminder` (Interview_Reminder plugin).
- Voice-interview templates are dispatched by `ARA_VoiceInterviewHandler`, `ARA_VoiceInterviewWebhookHandler`, and the screening-call batches (`ARA_ScreeningCallBatch`, `ARA_ScreeningCallSummaryBatch`).
