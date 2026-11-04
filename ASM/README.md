# ASM Group Documentation

Welcome to the comprehensive documentation for the **ASM product group** - three separate Salesforce repositories that together form a layered search, AI-search, and prompt/agent stack on top of ASYMBL-ATS.

> **Source of truth**: This documentation reflects all three repos' `master` branches as of 2026-10-30. It supersedes a single flat `ASM/` folder that presented all three repos as one undifferentiated product — investigation confirmed the prior content was entirely and exclusively about `ASM/asm`, with `asymbl-intelligence` and `AINTL-Unmanaged` having zero dedicated documentation anywhere in this repository. See "Why this was restructured" below.

## 📋 Table of Contents

- [Overview](#overview)
- [Package Statistics](#package-statistics)
- [Documentation by Repo](#documentation-by-repo)
- [Core Documentation](#core-documentation)
- [Object Categories](#object-categories)
- [Dependency Chain & Critical Caveat](#dependency-chain--critical-caveat)
- [Getting Started](#getting-started)
- [Quick Navigation](#quick-navigation)
- [Why This Was Restructured](#why-this-was-restructured)

## 🎯 Overview

The "ASM" product group spans **three separate Salesforce repositories**, all shipping under namespace `bpats`, forming a strictly layered dependency chain: a resume-parsing/search foundation (`asm`), an AI/natural-language search layer built on top of it (`asymbl-intelligence`), and a standalone prompts/agents package (`AINTL-Unmanaged`) that is conceptually downstream of both but declares no formal dependency.

### Key Features

- **Resume Parsing & Candidate Search** (`asm`): Daxtra/RChilli integration, advanced search, candidate-history tracking on top of the ATS data model
- **AI-Powered Natural-Language Search** (`asymbl-intelligence`): Einstein GPT / Prompt Builder-driven candidate search that delegates its core logic into `asm`'s packaged Apex
- **Prompt/Agent Content** (`AINTL-Unmanaged`): GenAI Prompt Templates for natural-language-to-boolean-search conversion and query analysis, consumed at runtime by `asymbl-intelligence`
- **Layered, Verified Dependency Chain**: Each repo's dependency on the ones below it is confirmed both declaratively (`sfdx-project.json`) and in code (live cross-package Apex calls)

## 📊 Package Statistics

| Metric | `asm` | `asymbl-intelligence` | `AINTL-Unmanaged` |
|--------|-------|------------------------|---------------------|
| **Package type** | Managed 2GP | Managed 2GP | Unmanaged |
| **Branch read** | `master` (HEAD `31f9b46` @ 2026-08-09) | `master` (HEAD `bcef0b5` @ 2026-07-29) | `master` (HEAD `373f805` @ 2025-07-02) — **effectively empty, see caveat below** |
| **Total package.xml components** | 864 | — (small, untracked at this granularity) | 2 (on `packaging`, not `master`) |
| **Apex Classes** | 103 declared (~53 unique) | 14 | 0 |
| **Lightning Web Components** | 51 declared / 53 on disk | 3 | 0 |
| **Custom Objects** | 9 (4 business + 5 `__mdt`) | 1 `__mdt` type | 0 |
| **Custom Labels** | 337 declared / 372 on disk | — | 0 |
| **Custom Metadata Records** | 242 | 1 type, 1 record | 0 |
| **GenAI Prompt Templates** | 0 | 0 (consumes AINTL-Unmanaged's by name) | 2 (on `packaging`) |
| **Declared dependency** | ASYMBL-ATS only | ASYMBL-ATS + Asymbl Search & Match | None declared |

See each repo's own README (linked below) for the full, caveated breakdown including manifest/force-app drift findings.

## 🗺️ Documentation by Repo

### [`asm/`](asm/) — Asymbl Search & Match

| Document | Description |
|-------------|-------------|
| [`README.md`](asm/README.md) | Entry point — dependency chain, package statistics |
| [`ASM_Package_Documentation.md`](asm/ASM_Package_Documentation.md) | Full component inventory, ULTRATHINK-verified, including manifest/force-app drift notes |
| [`ASM_Integration_Architecture.md`](asm/ASM_Integration_Architecture.md) | Daxtra/RChilli resume-parsing integration architecture |
| [`ASM_UI_Components_Architecture.md`](asm/ASM_UI_Components_Architecture.md) | Lightning Web Components, Quick Actions, Tabs, Layouts |
| [`ERD_Index.md`](asm/ERD_Index.md) | Entity-relationship diagrams for the custom object model |

### [`asymbl-intelligence/`](asymbl-intelligence/) — Asymbl Intelligence (AINTL)

| Document | Description |
|-------------|-------------|
| [`README.md`](asymbl-intelligence/README.md) | Entry point — dependency chain, what's notably different from `asm` |
| [`AINTL_Package_Documentation.md`](asymbl-intelligence/AINTL_Package_Documentation.md) | Full component inventory (Apex, LWC, custom metadata, dynamic permission sets), including a documented XML malformation in `manifest/package.xml` |

### [`AINTL-Unmanaged/`](AINTL-Unmanaged/) — prompts/agents (unmanaged package)

| Document | Description |
|-------------|-------------|
| [`README.md`](AINTL-Unmanaged/README.md) | Documents both the empty `master` branch and the real `packaging`-branch content, clearly labeled |

## 📖 Core Documentation

**[`asm/ASM_Package_Documentation.md`](asm/ASM_Package_Documentation.md)** - The most comprehensive single document in this group, containing:

- Complete object inventory and relationships for the foundation repo
- Package.xml vs. force-app drift findings (apparent renames not yet reflected in the manifest)
- `Search_History__c`, a business object missing from the original July 2025 inventory
- Daxtra/RChilli resume-parsing integration details

*Start here for the deepest technical detail; use the per-repo READMEs above for navigation into AINTL and AINTL-Unmanaged.*

## 🏗️ Object Categories

### 1. Search & Matching Objects (`asm`)
- **ASM_Education_History__c / Employment_History__c / ASM_Skill__c**: Candidate background tracking
- **Search_History__c**: Recent/saved search tracking (new since original inventory)
- **ASM_Candidate_Mapping__mdt / ASM_Job_Mapping__mdt / ASM_Daxtra_Credentials__mdt / ASM_Resume_Mapping__mdt**: Resume-parsing configuration

### 2. AI Search Layer (`asymbl-intelligence`)
- **AINTL_Generic_Setting__mdt**: AINTL's sole configuration object (3 fields, one record)
- 14 Apex classes and 3 LWCs implementing natural-language search on top of `asm`'s search service
- Dynamic permission sets (`ASYMBL_AINTL_User`, `ASYMBL_AINTL_Admin`) created programmatically, not shipped as static metadata

### 3. Prompt/Agent Content (`AINTL-Unmanaged`, on `packaging`)
- **NLP_to_Daxtra_Request**: Converts natural-language search text into a Daxtra-compatible boolean query
- **Search_Query_Analyzer**: Parses a natural-language query into 6 structured categories (job title, skills, location, experience, education, availability)

### 4. Standard-Object Extensions (confirming the ATS dependency)
- `Contact`, `bpats__Job__c`, `bpats__Job_Board_Application__c` — `Is_Synced__c`/`Sync_This_Record__c` fields added by `asm`

## 🔗 Dependency Chain & Critical Caveat

```
ASYMBL-ATS (bpats core)
  └─ Asymbl Search & Match (ASM/asm)                depends on: ASYMBL-ATS
       └─ Asymbl Intelligence (ASM/asymbl-intelligence)   depends on: ASYMBL-ATS + Asymbl Search & Match
            └─ AINTL-Unmanaged (ASM/AINTL-Unmanaged)      depends on: nothing declared — standalone,
                                                            conceptually downstream of AINTL only
```

Verified directly from each repo's `sfdx-project.json` on `master`: `asm` depends only on `ASYMBL-ATS`; `asymbl-intelligence` depends on both `ASYMBL-ATS` and `Asymbl Search & Match`, and additionally grants access to the `EinsteinGPTPromptTemplateManager` permission set; `AINTL-Unmanaged` declares no dependency block at all, and ships as an **unmanaged** package via its own GitHub Action, unlike its two managed-2GP siblings. A change low in this chain (ATS core) can ripple upward through all three ASM repos; the reverse is not true.

### ⚠ `AINTL-Unmanaged`'s `master` branch is effectively empty

As of 2026-10-30, `ASM/AINTL-Unmanaged`'s `master` branch contains **only a one-line `README.md`** — no `force-app`, no `manifest/package.xml`, no prompt templates, no CI workflows. All real content (the two GenAI prompt templates, `sfdx-project.json`, the unmanaged-package GitHub Action) exists only on the `packaging` branch and was **never merged into `master`**. This documentation set was built to the letter of the instruction to read `master` everywhere; the [`AINTL-Unmanaged/README.md`](AINTL-Unmanaged/README.md) documents both branches explicitly and clearly labeled. If you need the real AINTL-Unmanaged documentation, treat the `packaging`-branch content there as the operative reference, and flag to the repo owner that `master` is stale relative to `packaging`.

## 🚀 Getting Started

### For Developers
1. If extending candidate search/matching or resume parsing, start in [`asm/ASM_Package_Documentation.md`](asm/ASM_Package_Documentation.md)
2. If extending AI/natural-language search, start in [`asymbl-intelligence/AINTL_Package_Documentation.md`](asymbl-intelligence/AINTL_Package_Documentation.md) — it delegates core search logic into `asm`, so read that repo's docs first
3. If authoring or editing prompt templates, read [`AINTL-Unmanaged/README.md`](AINTL-Unmanaged/README.md) in full — note the `master`/`packaging` caveat before making any branch assumptions

### For Business Users
1. Begin with [`asm/ASM_Integration_Architecture.md`](asm/ASM_Integration_Architecture.md) for the resume-parsing workflow
2. Review [`asymbl-intelligence/README.md`](asymbl-intelligence/README.md) for what natural-language search adds on top

### For System Administrators
1. Study [`asm/ASM_UI_Components_Architecture.md`](asm/ASM_UI_Components_Architecture.md) for UI/tab/layout configuration
2. Review the dynamic permission-set provisioning notes in [`asymbl-intelligence/AINTL_Package_Documentation.md`](asymbl-intelligence/AINTL_Package_Documentation.md)
3. Check this file's [Dependency Chain & Critical Caveat](#dependency-chain--critical-caveat) section before relying on `AINTL-Unmanaged`'s `master` branch for anything

## 🧭 Quick Navigation

### By Functional Area
- **Resume Parsing & Search**: [`asm/ASM_Integration_Architecture.md`](asm/ASM_Integration_Architecture.md)
- **AI/Natural-Language Search**: [`asymbl-intelligence/AINTL_Package_Documentation.md`](asymbl-intelligence/AINTL_Package_Documentation.md)
- **Prompt Templates**: [`AINTL-Unmanaged/README.md`](AINTL-Unmanaged/README.md)

### By Repo
- **`asm`**: [README](asm/README.md) | [Package Docs](asm/ASM_Package_Documentation.md) | [ERD Index](asm/ERD_Index.md)
- **`asymbl-intelligence`**: [README](asymbl-intelligence/README.md) | [Package Docs](asymbl-intelligence/AINTL_Package_Documentation.md)
- **`AINTL-Unmanaged`**: [README](AINTL-Unmanaged/README.md)

### Implementation Resources
- **Complete Object List (asm)**: [ASM Package Documentation](asm/ASM_Package_Documentation.md)
- **Cross-Repo CI/CD Job Map**: `/Repos/CI-OVERVIEW.md`
- **Dependency Verification**: This file's [Dependency Chain & Critical Caveat](#dependency-chain--critical-caveat) section

## Why This Was Restructured

The previous flat `ASM/` folder (four files: `README.md`, `ASM_Package_Documentation.md`, `ASM_Integration_Architecture.md`, `ASM_UI_Components_Architecture.md`, `ERD_Index.md`, plus a stray `ASM/AIN/` subfolder) presented all content as if "ASM" were one undifferentiated product.

1. **All four flat `.md` files were entirely and exclusively about the `ASM/asm` repo** — none mentioned `asymbl-intelligence` or `AINTL-Unmanaged` at all. They have been moved, not duplicated, into `asm/` and corrected in place.
2. **`ASM/AIN/README.md` was misplaced AIN (asymbl-integration) content**, unrelated to any of the three ASM-group repos. Its content was fully and more thoroughly duplicated already in the top-level `/Repos/ProductDocumentations/AIN/` folder (12 files vs. this stub's 1), so the stray `ASM/AIN/` nesting was removed without any content loss.

---

## 📝 Documentation Notes

- All ERD diagrams use Mermaid syntax for consistent visualization, following the color-coding scheme in `ProductDocumentations/Claude.md`
- Component counts and relationships are verified directly against each repo's `manifest/package.xml` and `force-app/main/default` on `master`
- Cross-references between documents provide complete navigation paths across all three repos
- This revision (2026-10-30) restructured one flat folder into three repo-scoped subfolders and wrote first-ever dedicated documentation for `asymbl-intelligence` and `AINTL-Unmanaged` — see each subfolder's own documentation notes for repo-specific verification detail

For questions or contributions to this documentation, please refer to the main repository structure and contribution guidelines.
