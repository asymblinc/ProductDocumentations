# AIN Permission Sets Documentation

## Overview — Material Correction

**No static `PermissionSet` metadata ships in the AIN package.** A repo-wide search of `force-app/main/default/` on `master` confirms there is no `permissionsets/` directory at all. The three permission sets previously documented (`AIN_Admin`, `AIN_Beeline_Admin`, `AIN_IN_Integration_Guest_User`) as deployed `.permissionset-meta.xml` files **do not exist** — that entire prior documentation (object/field permission tables, tab visibility, sharing rules, permission set groups) was fabricated and has been fully replaced below with the verified, actual model.

## The Real Model: Runtime-Provisioned Permission Sets

AIN provisions access control **programmatically at install/runtime** via `AIN_PostInstallScript.onInstall()` (which calls its own `createPermissionSet()`) and the companion `AIN_PermissionSetController` (callable later, e.g. when new objects/fields are added, to re-provision/update permissions without a new package version). Both classes contain near-identical logic.

### The Four Permission Sets Created

| API Name | Label (from Custom Label) | `PERMISSION_SET_MAP` constant | Intended audience |
|---|---|---|---|
| `AIN_Admin_Clone` | `System.Label.AIN_Admin_User_Permission_Set_Label` | `AIN_Constants.AIN_ADMIN_PERMISSIONSET` | Full admin — edit + delete + view/modify all on AIN-managed objects |
| `AIN_User_Clone` | `System.Label.AIN_User_Permission_Set_Label` | `AIN_Constants.AIN_USER_PERMISSIONSET` | Standard internal users — read/create/edit (no delete, no view-all) |
| `AIN_Guest_Clone` | `System.Label.AIN_Guest_User_Permission_Set_Label` | `AIN_Constants.AIN_GUEST_PERMISSIONSET` | Guest/site users hitting public-facing endpoints (e.g. the screening question URL page); `PermissionsSendCustomNotifications`/`PermissionsRunFlow` explicitly set `false` |
| `AIN_Api_User_Clone` | `System.Label.AIN_Api_User_Permission_Set_Label` | `AIN_Constants.AIN_API_USER_PERMISSIONSET` | Integration/API users — edit + view-all but **not** delete, and excluded from the Admin tab |

### What Gets Provisioned, Per Permission Set

`assignPermissionsToPermissionSet()` builds, for each of the 4 permission sets:

1. **Object Permissions** (`assignObjectToPermissionSet`) — Read+Create always; Edit for `AIN_User_Clone`/`AIN_Admin_Clone`/`AIN_Api_User_Clone`; Delete + ViewAllRecords only for `AIN_Admin_Clone` (and ViewAllRecords for `AIN_Api_User_Clone`), on this object set:
   - `Job__c`, `ATS_Applicant__c`, `Job_Board_Application__c`, `ATS_Log__c` (excluded from object permission grants via `OBJECT_PERMISSION_TO_EXCULDE`, i.e. logging object is never object-permissioned here), `AIN_External_Custom_Fields__c`, `AIN_Webhook_Event__e` (platform event — read/create only, no edit, per `eventList` exclusion), `AIN_Webhook_Request__c`, `AIN_Candidate_Question__c`, `AIN_Question_Assignment__c`, `AIN_Candidate_Response__c`.
2. **Field Permissions** (`assignFieldToPermissionSet`) — dynamically discovered via `Schema.describe()` per object (excludes augmented/person-account/address compound-field suffixes `__s`/`__pc`/`__ps`), not a hand-maintained list. Read always; Edit unless the field is non-updatable (formula/autonumber) or the permission set is `AIN_User_Clone` against a configured read-only object (`AIN_USER_READ_ONLY_OBJECT_LIST`, currently empty).
3. **Custom Metadata / Custom Setting Access** (`assignMetaDataAndCustomSettingToPermissionSet`) — grants `CustomEntityDefinition` setup-entity access to all 10 CMDT types (`customMetaDataList`: Integration Credentials, Indeed Apply Mapping, Job Mapping, Job Board Mapping, VMS Job Mapping, VMS Job Board Mapping, Webhook Setup, Default Question Template, Disposition Sync Config, Disposition Value Map).
4. **Apex Class Access** (`assignApexClassToPermissionSet`) — every class matching `Name LIKE 'AIN%'`, plus 4 base-package utility classes (`ATS_DataUtility`, `ATS_LogHandler`, `ATS_SecureDml`, `ATS_Utility`).
5. **Apex Page Access** (`assignApexPageToPermissionSet`) — `AIN_ScreeningQuestionURLPage`, granted to all permission sets **except** `AIN_Api_User_Clone`.
6. **Named Credential Principal Access** (`assignNamedCredentialPrincipalToPermissionSet`) — grants `ExternalCredentialParameter` setup-entity access for the `AIN_AWS_Indeed_Integration` external credential's principals, via `ConnectApi.NamedCredentials.getExternalCredential()` (in `AIN_PostInstallScript`) and a Tooling API query fallback (`queryExternalCredentialParameters`, used by `AIN_PermissionSetController`).
7. **Tab Settings** (`assignTabSetToPermissionSet`, `@future`) — grants `DefaultOn` visibility to tabs for the managed object set, plus the `Asymbl_Integration_Admin` custom tab for `AIN_Admin_Clone` only (`AIN_User_Clone` explicitly excludes it via `OBJECT_TABS_TO_EXCLUDE_FOR_USER`).

### Error Handling

Both classes enqueue a nested `Queueable` (`ErrorLogQueue`) on any exception, logging via `bpats.ATS_LogHandler.createErrorLog` (controller) or a direct `ATS_Log__c` insert (`AIN_PostInstallScript`, since the install context may not have access to the base package's logger in all org states).

### `updateObjectPermission(objectName)`

`AIN_PermissionSetController` exposes a re-provisioning entry point: callers (e.g. when a new custom object needs to be covered) can call `updateObjectPermission('Some_Object__c')`, which adds the object to `sObjectList` and re-runs `createPermissionSet()`. Success is determined by checking that all 4 permission sets were returned (`permissionSetList.size() == 4`).

## Why This Matters for Documentation/Support

- There is **no Setup UI permission set record to show an admin** labeled "Asymbl Integration Admin" or similar — admins will see `AIN_Admin_Clone`, `AIN_User_Clone`, `AIN_Guest_Clone`, `AIN_Api_User_Clone` in Setup, with labels pulled from custom labels at creation time.
- Because permissions are recomputed from live schema describes (not a static XML diff), **newly added AIN fields/objects are automatically covered** the next time `createPermissionSet()`/`updateObjectPermission()` runs — there is no risk of a field being "missing from the permission set" the way there would be with hand-maintained static metadata, provided the post-install/update path is invoked.
- Assigning AIN access to a user means assigning one of these four permission sets via standard Permission Set Assignment — there are no permission set *groups*, sharing rules, or "Permission_Usage__c" audit object shipped with this package (all fabricated in prior documentation).

## Corrections vs. prior documentation

- `AIN_Admin`, `AIN_Beeline_Admin`, `AIN_IN_Integration_Guest_User` as static deployed permission sets **do not exist**. The real names are `AIN_Admin_Clone`, `AIN_User_Clone`, `AIN_Guest_Clone`, `AIN_Api_User_Clone`, and they are **created by Apex at install/runtime**, not retrieved as `.permissionset-meta.xml` source.
- There is no `Activity_Log__c` object (prior docs referenced permissions on it) — the actual logging object from the base package is `ATS_Log__c`, and it is explicitly *excluded* from object-permission assignment in both provisioning classes.
- There is no `Permission_Usage__c` tracking object, no sharing-rule configuration, and no permission-set-group recommendation shipped or implied by this package's metadata — all of that content in the prior documentation was invented.
- The `FeatureManagement.checkPermission('AIN_Admin')` code sample in prior documentation does not correspond to any real custom permission in this package and has been removed.
