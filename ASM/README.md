# ASM Group Documentation

> **Restructured 2026-10-30.** This folder was previously a single flat directory describing only
> one of three repos. It is now split into three subfolders — one per actual GitHub repository —
> reflecting the real, strictly layered dependency chain. See "Why this was restructured" below.

## The three repos

The "ASM" product group spans **three separate Salesforce repositories**, all shipping under
namespace `bpats`, all read here on branch **`master`**:

| Folder | Repo (disk path) | Package name | Depends on |
|---|---|---|---|
| [`asm/`](asm/) | `ASM/asm` | **Asymbl Search & Match** | ASYMBL-ATS (`bpats` core) only |
| [`asymbl-intelligence/`](asymbl-intelligence/) | `ASM/asymbl-intelligence` | **Asymbl Intelligence (AINTL)** | ASYMBL-ATS **and** Asymbl Search & Match |
| [`AINTL-Unmanaged/`](AINTL-Unmanaged/) | `ASM/AINTL-Unmanaged` | **AINTL Un-managed** (prompts/agents) | None declared (conceptually downstream of AINTL) |

## Dependency chain

```
ASYMBL-ATS (bpats core)
  └─ Asymbl Search & Match (ASM/asm)                depends on: ASYMBL-ATS
       └─ Asymbl Intelligence (ASM/asymbl-intelligence)   depends on: ASYMBL-ATS + Asymbl Search & Match
            └─ AINTL-Unmanaged (ASM/AINTL-Unmanaged)      depends on: nothing declared — standalone,
                                                            conceptually downstream of AINTL only
```

Verified directly from each repo's `sfdx-project.json` on `master` (2026-10-30):

- `ASM/asm` — package `Asymbl Search & Match`, v0.167.0, one dependency: `ASYMBL-ATS` (1.149.0.LATEST).
- `ASM/asymbl-intelligence` — package `Asymbl Intelligence`, v0.26.0, two dependencies: `ASYMBL-ATS`
  (1.134.5.LATEST) and `Asymbl Search & Match` (0.115.0.LATEST). Also grants access to the
  `EinsteinGPTPromptTemplateManager` permission set via `packageMetadataAccess` (Einstein
  GPT/Agentforce prompt tooling), without shipping that permission set itself.
- `ASM/AINTL-Unmanaged` — no `sfdx-project.json` dependency block at all on `master` (see caveat
  below); not a 2GP managed package — it ships as an **unmanaged** package via its own GitHub Action
  (`AINTL-SF-Unmanaged-Package-Creation.yml`), unlike its two siblings.

A change low in this chain (ATS core) can ripple upward through all three ASM repos; the reverse is
not true. See `/Repos/CI-OVERVIEW.md` for the cross-repo CI/CD job map, including the two
parallel-but-distinct `...-To-AINTL(Daxtra)-Staging-Deploy.yml` jobs (one owned by `bpats`, one
owned by `asm`) that both deploy into the same `aintlstaging.com` org.

## Critical caveat: `AINTL-Unmanaged`'s `master` branch is effectively empty

As of 2026-10-30, `ASM/AINTL-Unmanaged`'s `master` branch contains **only a one-line `README.md`**
(`# AINTL-Unmanaged`) — no `force-app`, no `manifest/package.xml`, no prompt templates, no CI
workflows. `git log master` shows exactly two commits since repo creation (2025-07-02): the initial
commit and a trivial README update.

All real content — the two GenAI prompt templates (`NLP_to_Daxtra_Request`,
`Search_Query_Analyzer`), `sfdx-project.json`, `manifest/package.xml`, and the
`AINTL-SF-Unmanaged-Package-Creation.yml` GitHub Actions workflow — exists only on the `packaging`
branch (and two now-merged-into-packaging feature branches), and was **never merged into `master`**.

This documentation set was built to the letter of the instruction to read `master` everywhere. The
[`AINTL-Unmanaged/README.md`](AINTL-Unmanaged/README.md) in this folder documents both facts
explicitly: what `master` actually contains (next to nothing) and what exists on `packaging` (the
real prompt/agent content), clearly labeled so the two are never confused. If you need the real
AINTL-Unmanaged documentation, treat the `packaging`-branch content in that file as the operative
reference, and flag to the repo owner that `master` is stale/abandoned relative to `packaging`.

## What's in each subfolder

### [`asm/`](asm/) — Asymbl Search & Match
The original, most mature documentation set in this group (first written ~2025-07-10, now refreshed
against `master` as of 2026-08-09). Covers: 53 LWC components, 103 ApexClass package.xml members
(~53 unique classes incl. tests), 9 custom objects (4 business objects + 5 `__mdt` config types),
242 CustomMetadata records, Daxtra/RChilli resume-parsing integration architecture, and UI
components (Quick Actions, Tabs, Layouts). See its own `README.md`... actually this subfolder has no
separate README — start at [`ASM_Package_Documentation.md`](asm/ASM_Package_Documentation.md).

### [`asymbl-intelligence/`](asymbl-intelligence/) — Asymbl Intelligence (AINTL)
New documentation, written from scratch for this restructuring (no prior docs existed for this repo
in this folder). Covers the AI/search layer built on top of `asm`: AINTL Advanced Search, Candidate
Detail views, the `AINTL_Generic_Setting__mdt` configuration object, and its Einstein GPT prompt
template permission-set grant. Read as of `master` @ 2026-07-29.

### [`AINTL-Unmanaged/`](AINTL-Unmanaged/) — prompts/agents (unmanaged package)
New documentation, written from scratch. See the critical caveat above — this subfolder's README
documents the `master`/`packaging` discrepancy directly rather than glossing over it.

## Why this was restructured

The previous flat `ASM/` folder (four files: `README.md`, `ASM_Package_Documentation.md`,
`ASM_Integration_Architecture.md`, `ASM_UI_Components_Architecture.md`, `ERD_Index.md`, plus a
stray `ASM/AIN/` subfolder) presented all content as if "ASM" were one undifferentiated product.
Investigation confirmed:

1. **All four flat `.md` files were entirely and exclusively about the `ASM/asm` repo** — none
   mentioned `asymbl-intelligence` or `AINTL-Unmanaged` at all (e.g. every object, Apex class, and
   LWC name used the `ASM_`/`asm` prefix, never `AINTL_`/`aintl`). They have been moved, not
   duplicated, into `asm/` and corrected in place (see change summary below).
2. **`ASM/AIN/README.md` was misplaced AIN (asymbl-integration) content**, unrelated to any of the
   three ASM-group repos. Its content (component counts, Beeline/Indeed integration architecture,
   links to `AIN_Package_Overview.md` etc.) was fully and more thoroughly duplicated already in the
   top-level `/Repos/ProductDocumentations/AIN/` folder (12 files vs. this stub's 1). Nothing unique
   was found in `ASM/AIN/README.md` that wasn't already covered, in more depth and more recently, in
   the real AIN folder — so the stray `ASM/AIN/` nesting was removed without any content loss.
