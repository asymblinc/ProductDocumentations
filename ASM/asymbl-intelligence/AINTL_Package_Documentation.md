# AINTL (Asymbl Intelligence) Package Documentation

> **Repo**: `ASM/asymbl-intelligence` on disk · **Branch read**: `master` (HEAD `bcef0b5` @ 2026-07-29) ·
> **Package name**: `Asymbl Intelligence`, v0.26.0 (`sfdx-project.json`) · **Namespace**: `bpats`
> (shared across the whole ASM/ATS product family) ·
> **Depends on**: ASYMBL-ATS (`1.134.5.LATEST`) **and** Asymbl Search & Match
> (`0.115.0.LATEST`, i.e. the `ASM/asm` repo) — both declared in `sfdx-project.json`'s
> `dependencies` block. This is the **first time** this repo has had dedicated documentation in this
> repository; there was no prior AINTL-specific content here (the previous flat `ASM/` folder was
> entirely about `ASM/asm`).

## Overview

AINTL ("Asymbl Intelligence") is the AI/GenAI layer of the ASM product group. It sits **on top of**
`ASM/asm`'s search infrastructure and ASYMBL-ATS's core objects, adding:

- **Natural-language candidate search** via Einstein GPT / Agentforce prompt templates (Einstein
  `ConnectApi.EinsteinLLM` calls, not a custom LLM integration)
- **AI-assisted Advanced Search** — parses free-text recruiter queries ("Software Engineers with
  5+ years of experience in California") into structured search parameters (job title, skills,
  location, experience, education, availability)
- **Candidate Detail card views** — Lightning components for presenting enriched candidate summaries
- A thin **Beta Intelligence** admin tab surfacing the above via `aintlCustomSearchContainer`

AINTL is intentionally thin relative to `asm`: it does **not** reimplement search/match or resume
parsing — it delegates directly into `asm`'s Apex via explicit cross-package calls (see
"Dependency on `asm`" below). This is consistent with it being a comparatively young, fast-moving
add-on (v0.26.0 vs. `asm`'s v0.167.0).

## Package Statistics (ULTRATHINK-verified against `force-app` on `master`; cross-checked against `manifest/package.xml`)

| Component Type | On disk (`force-app`) | Declared in `manifest/package.xml` | Note |
|---|---|---|---|
| **ApexClass** | 14 (7 prod + 7 test classes, 28 files incl. `-meta.xml`) | 14 members, but **malformed XML** — see critical finding below | |
| **CustomObject** | 1 (`AINTL_Generic_Setting__mdt`) | 1 | Matches |
| **CustomField** | 3 (all on `AINTL_Generic_Setting__mdt`) | 3 | Matches |
| **CustomMetadata** (records) | 1 (`AINTL_Generic_Setting.Generic_Setting`) | 1 | Matches |
| **Layout** | 1 | 1 | Matches |
| **CustomTab** | 1 (`Beta_Intelligence`, correctly spelled on disk) | 1, but **misspelled `Beta_Inteligence`** in package.xml | Real drift — see below |
| **LightningComponentBundle** | 3 (`aintlCandidateDetailCardView`, `aintlCustomSearchContainer`, `aintlListTooltip`) | 2 declared (`aintlCandidateDetailCardView` missing) | Real gap |
| **CustomLabel** | 22 (counted directly in `CustomLabels.labels-meta.xml`) | 16 declared | Real gap (6 undeclared labels, incl. `AINTL_Candidate_Name_Label_New`, `AINTL_Candidates_Label_New`, `AINTL_Showing_Contacts_Count_New`, `AINTL_Showing_Contact_Count_New`, `AINTL_Candidate_Information_Label`, `AINTL_Applicant_No_Attachment`) |
| **FieldSet** | 1 (`Contact.Candidate_Card_Details_Field_Set`) | 1 | Matches |
| **PermissionSet** | 0 static metadata files | 0 | **Not a gap** — see "Dynamic permission sets" below |

### ⚠ Critical finding: malformed `manifest/package.xml` on `master`

`manifest/package.xml`'s `ApexClass` `<types>` block has a genuine XML syntax error at its final
member:

```xml
<members>AINTL_AdvancedSearchControllerTest<members>
<name>ApexClass</name>
```

The closing `</members>` tag is missing its `/`, so it reads `<members>` again instead of
`</members>`. A strict XML parser (e.g. Salesforce CLI's manifest validation, or any tool using a
real XML parser rather than regex) would likely fail to parse this file or silently mis-scope
everything after this point. This is a genuine defect in the repo's `master` branch, not a
documentation artifact — flagging for the AINTL team to fix before this manifest is used for any
retrieve/deploy operation.

### Apex Classes (14 total: 7 production + 7 test)

- **AINTL_AdvancedSearchController** / `...Test` — Einstein GPT-powered natural-language query
  parser. `analyzeSearchQuery(promptTemplateName, query)` calls
  `ConnectApi.EinsteinLLM.generateMessagesForPromptTemplate(...)` against an Einstein Prompt
  Template (the kind of template that, for the full ASM group, physically lives in the
  `AINTL-Unmanaged` repo — see cross-repo note below) and deserializes the JSON response into a
  `SearchWrapper` (job title / skills / location / experience / education / availability).
- **AINTL_CustomSearchController** / `...Test` — search UI controller.
- **AINTL_CustomSearchService** / `...Test` — **delegates directly into `asm`'s packaged Apex**:
  `fetchContactDetails`, `fetchMoreContacts`, `fetchFiles`, `updateCandidateMetadata`, and
  `fetchCandidateFieldsDescribe` all call `bpats.ASM_CustomSearchService.aintlFetch...` /
  `aintlUpdate...` methods — i.e. `ASM_CustomSearchService` in the `asm` repo exposes an `aintl*`-
  prefixed public API surface specifically for AINTL to consume. This is the concrete, in-code proof
  of the `asymbl-intelligence → asm` dependency (beyond just the declared package dependency).
- **AINTL_CandidateDetailController** / `...Test` — backs the candidate detail card view LWC.
- **AINTL_Constants** — shared constants.
- **AINTL_SelectOptionWrapper** — generic `{label, value}` wrapper used across search UI.
- **AINTL_Utility** / `...Test` — shared utility methods (e.g. `getOrgNamespace()`, used by the
  post-installer to detect the subscriber org's namespace at install time).
- **AINTL_PostInstaller** / `...Test` — `InstallHandler` implementation; see "Dynamic permission
  sets" below.

### Custom Objects / Metadata (1 custom metadata type)

- **AINTL_Generic_Setting__mdt** — fields: `Enable_Logging__c` (Checkbox, default `false`),
  `Prompt_Name__c` (Text 255), `Search_Query_Prompt_Name__c` (Text 255). A single record,
  `Generic_Setting`, ships with the package (both prompt-name fields currently null/blank in the
  shipped default record — they are populated per-org at configuration time with the actual
  Einstein Prompt Template API names to invoke).
- **Standard object extension**: `Contact.Candidate_Card_Details_Field_Set` — a FieldSet (not a
  custom field) exposing `MailingState`, `Email`, `MobilePhone`, `Title` for the candidate card UI.

### Lightning Web Components (3)

| Component | Purpose |
|---|---|
| `aintlCustomSearchContainer` | Main search container; backs the `Beta_Intelligence` tab |
| `aintlCandidateDetailCardView` | Candidate detail card UI (not declared in package.xml — gap noted above) |
| `aintlListTooltip` | Shared tooltip utility component |

### Custom Tab

- **Beta_Intelligence** (label "Beta Intelligence") — `lwcComponent: aintlCustomSearchContainer`,
  motif `Custom24: Building`. Correctly named on disk; package.xml has a typo (`Beta_Inteligence`).

### Dynamic (install-time) permission sets — not static metadata

`AINTL_PostInstaller.onInstall()` programmatically creates **two permission sets at package
install time** rather than shipping static `.permissionset-meta.xml` files:

- **ASYMBL_AINTL_User** ("Asymbl AINTL User")
- **ASYMBL_AINTL_Admin** ("Asymbl AINTL Admin")

It grants object/field permissions (via SOQL against `Schema.getGlobalDescribe()`, filtered to
custom fields in the subscriber's namespace), Apex class access to every class matching
`Name LIKE 'AINTL%'`, custom-metadata/custom-setting access, and tab visibility (including the
`Beta_Intelligence` tab) — all driven by Apex rather than declarative metadata. This explains why
`force-app` and `manifest/package.xml` both correctly show **zero** `PermissionSet` components even
though end users clearly need permission sets to use the package.

### sfdx-project.json detail

```json
"dependencies": [
  { "package": "ASYMBL-ATS", "versionNumber": "1.134.5.LATEST" },
  { "package": "Asymbl Search & Match", "versionNumber": "0.115.0.LATEST" }
],
"packageMetadataAccess": {
  "permissionSets": ["EinsteinGPTPromptTemplateManager"]
}
```

The `packageMetadataAccess.permissionSets` entry grants AINTL's package build access to the
**`EinsteinGPTPromptTemplateManager`** permission set at packaging time — a Salesforce-platform
permission set (not shipped by any of the three ASM-group repos) required to manage/use Einstein
Prompt Templates. This is the metadata-level tie confirming AINTL is built around Salesforce's
native Prompt Builder / Einstein GPT feature set, matching the "AI layer (Einstein GPT,
Agentforce)" framing used for the ASM group as a whole.

## Cross-repo note: where are the actual prompt templates?

`AINTL_AdvancedSearchController.analyzeSearchQuery()` takes a `promptTemplateName` parameter and
invokes it via `ConnectApi.EinsteinLLM.generateMessagesForPromptTemplate(promptTemplateName, ...)` —
but **no `genAiPromptTemplate` metadata exists anywhere in this repo** (`asymbl-intelligence`) on any
branch checked. The actual prompt template definitions that plausibly populate
`AINTL_Generic_Setting__mdt.Search_Query_Prompt_Name__c` (e.g. a template that performs the same
job-title/skills/location/experience/education/availability extraction described in
`AINTL_AdvancedSearchController`'s test mock) live in the sibling **`AINTL-Unmanaged`** repo — see
`../AINTL-Unmanaged/README.md` for the important caveat that those templates currently exist only on
`AINTL-Unmanaged`'s `packaging` branch, not its `master`. This is the concrete, code-level link that
justifies `AINTL-Unmanaged` being "conceptually downstream of AINTL" despite having no declared
package dependency on it.

## Component count caveat

Given the manifest/force-app drift and the XML malformation documented above, do not treat
`manifest/package.xml` on this branch as authoritative for a deploy/retrieve manifest without first
fixing the malformed tag and reconciling the gaps (`aintlCandidateDetailCardView`,
`Beta_Inteligence` → `Beta_Intelligence`, and the 6 undeclared `CustomLabel` entries).
