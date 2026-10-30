# Asymbl Intelligence (AINTL) — Documentation Index

> **Repo**: `ASM/asymbl-intelligence` on disk · **Branch read**: `master` (HEAD `bcef0b5` @
> 2026-07-29) · **Package**: `Asymbl Intelligence` v0.26.0, namespace `bpats`.

## What this repo is

AINTL is the AI/GenAI layer of the ASM product group, built on Salesforce's native Einstein GPT /
Prompt Builder (`ConnectApi.EinsteinLLM`) feature set. It adds natural-language candidate search and
richer candidate-detail UI on top of the search/match foundation that `ASM/asm` provides. It is
deliberately thin — 14 Apex classes, 3 LWCs, 1 custom metadata type — and its core search logic
delegates directly into `asm`'s packaged Apex rather than reimplementing it.

## Dependency chain

```
ASYMBL-ATS (bpats core)
  └─ Asymbl Search & Match (ASM/asm)
       └─ Asymbl Intelligence (ASM/asymbl-intelligence)   ← you are here
            └─ AINTL-Unmanaged (prompts/agents, no declared dependency)
```

Declared in `sfdx-project.json`: depends on `ASYMBL-ATS` (1.134.5.LATEST) **and**
`Asymbl Search & Match` (0.115.0.LATEST). Confirmed in code: `AINTL_CustomSearchService` calls
`bpats.ASM_CustomSearchService.aintlFetchContactDetails(...)` and sibling `aintl*`-prefixed methods
— i.e. the `asm` package explicitly exposes an AINTL-facing API surface.

## Documents in this folder

- [`AINTL_Package_Documentation.md`](AINTL_Package_Documentation.md) — full component inventory
  (Apex, LWC, custom metadata, tabs, dynamic permission sets), ULTRATHINK-verified against
  `force-app`, including a documented XML malformation in `manifest/package.xml` and several
  manifest/force-app drift findings (undeclared `aintlCandidateDetailCardView` LWC, undeclared
  CustomLabels, a `Beta_Inteligence`/`Beta_Intelligence` spelling mismatch between the manifest and
  the actual tab file).

## What's notably different from `asm`'s documentation

- No dedicated ERD file exists for AINTL — its only custom object is a single custom metadata type
  (`AINTL_Generic_Setting__mdt`) with 3 fields and one record; a full ERD felt disproportionate.
  The object model is documented inline in `AINTL_Package_Documentation.md`.
- No permission-set metadata files exist in this repo at all — permission sets
  (`ASYMBL_AINTL_User`, `ASYMBL_AINTL_Admin`) are created **programmatically** by
  `AINTL_PostInstaller` at package-install time. This is called out explicitly so it isn't mistaken
  for a documentation gap.
- This repo's README and overall documentation footprint were thin before this refresh (matching
  the parent task's expectation that AINTL and AINTL-Unmanaged have thinner docs than `asm`) — this
  is the first dedicated, verified documentation set for this repo in `ProductDocumentations`.
