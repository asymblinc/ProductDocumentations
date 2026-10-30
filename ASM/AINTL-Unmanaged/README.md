# AINTL-Unmanaged — Documentation

> **Repo**: `ASM/AINTL-Unmanaged` on disk · **Branch instructed**: `master` (HEAD `373f805` @
> 2025-07-02) · **Package type**: **Unmanaged** (not a 2GP managed package like its two siblings) ·
> **Declared dependencies**: none (confirmed — `sfdx-project.json` on the branch with real content
> has no `dependencies` array at all).

## Read this first: `master` is effectively an empty shell

As of 2026-10-30, `ASM/AINTL-Unmanaged`'s **`master` branch contains exactly one file**:

```
README.md   (single line: "# AINTL-Unmanaged")
```

No `force-app`, no `manifest/package.xml`, no `sfdx-project.json`, no `.github/workflows/`, nothing
else. `git log master` shows exactly two commits since the repo's creation on 2025-07-02:

```
373f805  2025-07-02 21:05  Update README.md
7a9cb41  2025-07-02 21:45  Initial commit
```

**All real development on this repo happened on the `packaging` branch** (and two now-merged
feature branches, `feature/github-package-creation-action` and
`fix/slack-webhook-rename-and-order`) and was **never merged back into `master`**. This is the
documentation-methodology equivalent of a repo whose default/mainline branch has gone stale while
active work continued elsewhere — worth flagging to the repo owner rather than silently treating
`packaging`'s content as if it were `master`'s.

The rest of this document describes what actually exists **on `packaging`**, clearly labeled as
such, because that is the only branch with anything to document, and because the parent AINTL
documentation (`../asymbl-intelligence/AINTL_Package_Documentation.md`) references this repo's
prompt templates as the real implementation behind `AINTL_AdvancedSearchController`'s
`promptTemplateName` parameter.

## What exists on `packaging` (NOT `master`)

### Package identity

`sfdx-project.json` on `packaging`:
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

### Content: 2 GenAI Prompt Templates (prompts/agents only — no Apex, no LWC)

| Template (`developerName`) | Purpose |
|---|---|
| **`NLP_to_Daxtra_Request`** | Converts natural-language recruiter search text into a Daxtra-compatible advanced boolean search query (`Query.value`), with explicit boolean-operator guardrails (AND/OR, quoted phrases, proximity operators) and a conditional `Candidate`/`Query` JSON output shape depending on whether experience/radius constraints are present. |
| **`Search_Query_Analyzer`** | Parses a natural-language recruiter query into 6 structured categories — `job_title`, `skills`, `location`, `experience`, `education`, `availability` — each constrained to 1–4 words, returned as JSON. This is almost certainly the real backing implementation for the mock response used in `asymbl-intelligence`'s `AINTL_AdvancedSearchController.getPromptTestResponse()` test stub (same six-category JSON shape). |

Both templates live under a doubled/malformed directory path in the actual repo tree:
`force-app/main/default/genAiPromptTemplates/force-app/main/default/genAiPromptTemplates/*.genAiPromptTemplate-meta.xml`
— i.e. the `force-app/main/default/genAiPromptTemplates/` path is duplicated inside itself. This
looks like an accidental nested-copy mistake during a commit (likely `8f9f7e5`, "Added Prompt
Template to analyse the search query") rather than an intentional structure, and would need
correcting before this could cleanly deploy via standard SFDX source-format conventions.

### ⚠ manifest/package.xml vs. force-app drift (same pattern as the other two ASM-group repos)

`packaging`'s `manifest/package.xml` declares only:
```xml
<members>NLP_to_Daxtra_Request</members>
<name>GenAiPromptTemplate</name>
```
**`Search_Query_Analyzer` is missing from the manifest** even though the template file exists on
disk (added in a later commit, `bede947`, without updating the manifest). Consistent with the
manifest-drift pattern independently observed in both `asm` and `asymbl-intelligence`.

### CI/CD: unmanaged-package workflow (unique to this repo in the ASM group)

`.github/workflows/AINTL-SF-Unmanaged-Package-Creation.yml` (on `packaging`) is a
`workflow_dispatch`-only job that builds this repo as an **unmanaged** package version — distinct
from the standard managed-2GP-package pattern used by `asm` and `asymbl-intelligence`. See
`/Repos/CI-OVERVIEW.md` for the full cross-repo CI/CD job map; this is the one workflow in the ASM
group that doesn't follow the managed-package convention.

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

## Confidence and recommended next step

This README is a faithful, ULTRATHINK-checked account of both branches as they exist today. It is
**not** a substitute for getting `master` and `packaging` reconciled — recommend flagging to the
repo owner that `master` has been stale since 2025-07-02 and either merging `packaging` forward or
re-pointing the default branch, so that future "read `master`" requests for this repo don't
silently return an empty shell.
