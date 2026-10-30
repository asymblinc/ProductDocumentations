# AIN Lightning Web Components Documentation

## Overview

The AIN package ships **31 Lightning Web Components** (verified `LightningComponentBundle` member count in `manifest/package.xml` on `master`), up from 16 previously documented — nearly double, driven by RedDot, the disposition mapping UI, the screening/demographic question builder, and the webhook configuration/retry UI, none of which were covered before.

## Component Inventory (all 31, verified against package.xml)

| Component | Functional Area | Purpose |
|---|---|---|
| `ainActivityTimeline` | Shared | Integration activity history display on record pages |
| `ainAdminSetup` | Admin shell | Top-level admin setup container for the `Asymbl_Integration_Admin` tab |
| `ainAuthenticationContainer` | Admin shell | Wraps per-system authentication setting components |
| `ainAuthenticationSetting` | Shared | Generic credential input/test-connection component |
| `ainBeelineAuthenticationContainer` | Beeline | Container for Beeline-specific auth configuration |
| `ainBeelineAuthenticationSetting` | Beeline | Beeline OAuth credential fields (client ID/secret/base URL), connection test |
| `ainCandidateMapping` | Beeline | Candidate field mapping configuration UI |
| `ainCandidateSubmission` | Beeline | Candidate submission-to-VMS interface |
| `ainDispositionFieldEntry` | Disposition Sync | Single field-row entry in the disposition sync configuration grid |
| `ainErrorDetailModal` | Shared | Modal for displaying detailed error information |
| `ainFieldMappingEntry` | Field Mapping | Single row in a generic field mapping configuration grid |
| `ainIndeedApplySettings` | Indeed | Indeed Apply configuration (resume/cover letter/phone requirements) |
| `ainIndeedDispositionMapping` | Disposition Sync | Indeed disposition-status mapping configuration UI |
| `ainIndeedFieldEntry` | Indeed | Indeed-specific field configuration row |
| `ainJobPortalCard` | Job Publishing | Publish/unpublish status card per external system (used by the `Publish_Job` quick action) |
| `ainJobSourceAndDistributionContainer` | Job Publishing | Container showing job source/distribution across channels; uses `lightning/refresh` for record-page refresh registration |
| `ainMultiSelectComboboxItem` | Shared | Individual selectable item within `ainMultiselectCombobox` |
| `ainMultiselectCombobox` | Shared | Multi-select dropdown control |
| `ainObjectMappingSetting` | Field Mapping | Object/field mapping configuration screen |
| `ainPostingDetails` | Job Publishing | Displays posting detail/status information for a job |
| `ainPublishJobQuickAction` | Job Publishing | Quick action UI for publishing a job to one or more external systems |
| `ainQuestionForm` | Question Builder | Form for creating/editing a single screening or demographic question (supports hierarchical/conditional logic) |
| `ainQuestionTemplateManager` | Question Builder | Manages the question template library and per-job question assignment |
| `ainRedDotAuthenticationContainer` | RedDot | Container for RedDot authentication configuration |
| `ainRedDotAuthenticationSettings` | RedDot | RedDot credential fields and connection settings |
| `ainReddotIframe` | RedDot | Embeds the RedDot job-posting iframe experience on the record page (uses `lightning/uiRecordApi`) |
| `ainRetryWebhook` | Webhook | Manual webhook retry UI (backs the `Retry_Webhooks` quick action; uses `getRecordNotifyChange`) |
| `ainTemplateFormModal` | Question Builder | Modal wrapper for creating/editing question templates |
| `ainUtility` | Shared | Shared utility functions, including `reduceErrors` (imported by nearly every other component's error handling) |
| `ainWebhookConfiguration` | Webhook | Webhook endpoint/URL configuration screen (site URL generation, retry scheduling) |
| `ainWebhookContainer` | Webhook | Top-level container wrapping webhook configuration and retry UI |

**31 components confirmed; no components named `ainMultiselectCombobox`'s predecessor or similar are missing — this is the complete, exhaustive list from package.xml.**

## Corrections vs. prior documentation

- **Count is 31, not 16.** Fifteen components are net-new: `ainCandidateMapping`, `ainCandidateSubmission`, `ainDispositionFieldEntry`, `ainIndeedDispositionMapping`, `ainJobSourceAndDistributionContainer`, `ainPostingDetails`, `ainQuestionForm`, `ainQuestionTemplateManager`, `ainRedDotAuthenticationContainer`, `ainRedDotAuthenticationSettings`, `ainReddotIframe`, `ainRetryWebhook`, `ainTemplateFormModal`, `ainWebhookConfiguration`, `ainWebhookContainer`.
- All AIN LWCs use `apiVersion 66.0` (prior documentation's metadata sample showed `59.0`, which is stale).
- Prior documentation's code samples (e.g. `AIN_AuthenticationController` Apex backing class, drag-and-drop field mapping, `LightningConfirm.open()` deletion confirmation) are largely illustrative/generic and were not verified against real component source; several were materially inaccurate (`ainAuthenticationContainer` has no `AIN_AuthenticationController` Apex class — there is no such class in the package; auth components call system-specific controllers like `AIN_BeelineAuthSettingController`, `AIN_IndeedAuthSettingController`, `AIN_RedDotAuthSettingController` directly).
- The actual, verified coding conventions for these components (import patterns, event dispatch patterns, labels/constants file structure, Jest testing requirements) are documented separately and authoritatively in `asymbl-integration/.claude/rules/lwc.md` — refer to that file rather than this document for implementation-level patterns, since it is derived directly from reading live component source (`ainJobPortalCard`, `ainBeelineAuthenticationSetting`, `ainActivityTimeline`, `ainWebhookConfiguration`, `ainPublishJobQuickAction`).
- **No `__tests__/` Jest directories exist for any of the 31 components** as of this review (consistent with the repo's own `.claude/rules/lwc.md`, which flags this as a known gap and mandates tests for new components going forward).

## Shared Conventions (verified)

- Naming: `ain<FeatureName>` folder, `Ain<FeatureName>` JS class, kebab-case tag (`c-ain-job-portal-card`).
- Error handling: `reduceErrors` from `c/ainUtility`, surfaced via `ShowToastEvent`.
- Spinner coordination: custom `spinner` event bubbled to parent containers.
- Conditional rendering: `lwc:if`/`lwc:elseif`/`lwc:else` (not legacy `if:true`/`if:false`) in current components.
