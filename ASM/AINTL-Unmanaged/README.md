# AINTL-Unmanaged — Documentation

> **Repo**: `ASM/AINTL-Unmanaged` on disk · **Branch read**: `master` (HEAD `fc09cb4` @ 2026-11-04) ·
> **Package type**: **Unmanaged** (not a 2GP managed package like its two siblings) ·
> **Declared dependencies**: none (confirmed — `sfdx-project.json` has no `dependencies` array).

## Update (2026-11-04): `master` is now up to date

Previously (as of this doc set's 2026-10-30 refresh), `master` contained only a one-line `README.md`
while all real development sat on `packaging`. On 2026-11-04, `packaging` was merged into `master`
via PR #3 (`95b51da..fc09cb4`), so `master` now carries the repo's full real content. This document
has been rewritten accordingly — everything below describes `master` directly; there is no longer a
branch discrepancy to track for this repo.

## Package identity

`sfdx-project.json` on `master`:
- `"package": "AINTL Un-managed"`, v0.2.0, `versionDescription`: *"AINTL Unmanaged package for
  promts and agents"* [sic — typo in source]
- **No `dependencies` array** — confirms this package declares no dependency on ASYMBL-ATS, `asm`,
  or `asymbl-intelligence`, even though it is conceptually built to be consumed alongside AINTL.
- Scratch-org `features` include `Einstein1AIPlatform`, `EINSTEINGPT`, `EINSTEINGPTPLATFORM`,
  `EINSTEINGPTPROMPTBUILDER`, `EINSTEINGENERATIVESERVICES`, `EINSTEINLLMREQUESTSHOURLY` — i.e. this
  package exists specifically to develop/ship Einstein GPT Prompt Builder content, not general
  Apex/LWC metadata.
- `packageMetadataAccess.permissionSets`: `EinsteinGPTPromptTemplateManager` — same permission-set
  grant declared by `asymbl-intelligence`, reinforcing that both packages are built around the same
  native Salesforce Prompt Builder feature.

## Content: 2 GenAI Prompt Templates (prompts/agents only — no Apex, no LWC)

| Template (`developerName`) | Purpose |
|---|---|
| **`NLP_to_Daxtra_Request`** | Converts natural-language recruiter search text into a Daxtra-compatible advanced boolean search query (`Query.value`), with explicit boolean-operator guardrails (AND/OR, quoted phrases, proximity operators) and a conditional `Candidate`/`Query` JSON output shape depending on whether experience/radius constraints are present. Takes two inputs: `searchText` and `additionalText`. Uses model `sfdc_ai__DefaultGPT41`. |
| **`Search_Query_Analyzer`** | Parses a natural-language recruiter query into 6 structured categories — `job_title`, `skills`, `location`, `experience`, `education`, `availability` — each constrained to 1–4 words, returned as JSON. Takes one input: `Search_Query`. Has 2 template versions, both using model `sfdc_ai__DefaultOpenAIGPT4OmniMini`. This is almost certainly the real backing implementation for the mock response used in `asymbl-intelligence`'s `AINTL_AdvancedSearchController.getPromptTestResponse()` test stub (same six-category JSON shape). |

Both templates live under a doubled/malformed directory path in the actual repo tree:
`force-app/main/default/genAiPromptTemplates/force-app/main/default/genAiPromptTemplates/*.genAiPromptTemplate-meta.xml`
— i.e. the `force-app/main/default/genAiPromptTemplates/` path is duplicated inside itself. This
looks like an accidental nested-copy mistake during a commit (likely `8f9f7e5`, "Added Prompt
Template to analyse the search query") rather than an intentional structure, and would need
correcting before this could cleanly deploy via standard SFDX source-format conventions. This
doubled path carried forward through the `packaging` → `master` merge and is still present.

### ⚠ manifest/package.xml vs. force-app drift (same pattern as the other two ASM-group repos)

`master`'s `manifest/package.xml` declares only:
```xml
<members>NLP_to_Daxtra_Request</members>
<name>GenAiPromptTemplate</name>
```
**`Search_Query_Analyzer` is missing from the manifest** even though the template file exists on
disk (added in a later commit, `bede947`, without updating the manifest). Consistent with the
manifest-drift pattern independently observed in both `asm` and `asymbl-intelligence`.

## CI/CD: unmanaged-package workflow (unique to this repo in the ASM group)

`.github/workflows/AINTL-SF-Unmanaged-Package-Creation.yml` is a `workflow_dispatch`-only job that
builds this repo as an **unmanaged** package version — distinct from the standard managed-2GP-package
pattern used by `asm` and `asymbl-intelligence`. See `/Repos/CI-OVERVIEW.md` for the full cross-repo
CI/CD job map; this is the one workflow in the ASM group that doesn't follow the managed-package
convention.

## Dependency chain position

```
ASYMBL-ATS (bpats core)
  └─ Asymbl Search & Match (ASM/asm)
       └─ Asymbl Intelligence (ASM/asymbl-intelligence)
            └─ AINTL-Unmanaged   ← no declared package dependency, but conceptually downstream:
                                    its prompt templates are the GenAI content that
                                    AINTL_AdvancedSearchController invokes by name at runtime
                                    (see ../asymbl-intelligence/AINTL_Package_Documentation.md)
```

## Confidence and remaining follow-ups

This README is a faithful, ULTRATHINK-checked account of `master` as it exists today
(2026-11-04), now that it has been reconciled with `packaging`. Two real findings remain open and
are carried forward from the pre-merge documentation rather than silently dropped:

1. The doubled `genAiPromptTemplates/force-app/main/default/genAiPromptTemplates/` directory path.
2. `Search_Query_Analyzer` missing from `manifest/package.xml` despite existing on disk.
