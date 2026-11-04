# Asymbl Search & Match (ASM) — Documentation Index

> **Repo**: `ASM/asm` on disk · **Branch read**: `master` (HEAD `31f9b46` @ 2026-08-09) ·
> **Package**: `Asymbl Search & Match`, v0.167.0, namespace `bpats`.

## What this repo is

ASM is the resume-parsing and candidate search/matching add-on to ASYMBL-ATS. It integrates two
external resume-parsing services (Daxtra and RChilli) and provides advanced search, matching, and
candidate-history tracking on top of the ATS data model. It is the foundation repo for the ASM
group — both `asymbl-intelligence` (AINTL) and, indirectly, `AINTL-Unmanaged` build on top of it.

## Dependency chain

```
ASYMBL-ATS (bpats core)
  └─ Asymbl Search & Match (ASM/asm)   ← you are here
       └─ Asymbl Intelligence (ASM/asymbl-intelligence)
            └─ AINTL-Unmanaged (prompts/agents, no declared dependency)
```

Declared in `sfdx-project.json`: depends on `ASYMBL-ATS` (1.149.0.LATEST) only. Confirmed in code:
standard-object extensions on ATS-owned objects (`Contact`, `bpats__Job__c`,
`bpats__Job_Board_Application__c`) and an AINTL-facing API surface (`aintl*`-prefixed methods on
`ASM_CustomSearchService`) that `asymbl-intelligence` calls directly.

## Documents in this folder

- [`ASM_Package_Documentation.md`](ASM_Package_Documentation.md) — full component inventory (Apex,
  LWC, custom objects/metadata, triggers, UI components), ULTRATHINK-verified against `force-app`,
  including documented `package.xml`/`force-app` drift (apparent renames not yet reflected in the
  manifest) and `Search_History__c`, a business object missing from the original July 2025
  inventory.
- [`ASM_Integration_Architecture.md`](ASM_Integration_Architecture.md) — Daxtra/RChilli resume-
  parsing integration architecture.
- [`ASM_UI_Components_Architecture.md`](ASM_UI_Components_Architecture.md) — Lightning Web
  Components, Quick Actions, Tabs, and Layouts.
- [`ERD_Index.md`](ERD_Index.md) — entity-relationship diagrams for the custom object model.

## Package statistics (verified against `master`, 2026-10-30 refresh)

864 total package.xml components · 103 ApexClass members (~53 unique classes) · 51 LWCs declared /
53 on disk · 9 custom objects (4 business objects + 5 `__mdt` config types) · 337 custom labels
declared / 372 on disk · 242 custom metadata records.

See `ASM_Package_Documentation.md` for the full, caveated breakdown.

## What's notably different from `asymbl-intelligence`'s documentation

This is the original, most mature documentation set in the ASM group (first written ~2025-07-10,
refreshed against `master` as of 2026-08-09) — substantially larger in scope than its two younger
sibling repos, reflecting that `asm` is the foundation the other two build on.
