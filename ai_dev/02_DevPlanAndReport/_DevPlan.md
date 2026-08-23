<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] AI Development Process Documents -- 02_DevPlanAndReport

[What This Directory Collects]
  Dev Plans (DevPlan / BE_DevPlan / FE_DevPlan), Dev Reports (DevReport),
  context-saving documents (Snapshot), AI-to-AI handover documents (Handover),
  improvement plans (ImprovePlan), naming-normalization and directory-reorganization
  plans (RenamePlan / ReorganizePlan).

[Target Directory]
  Platform level: docs/ai_dev_history/02_DevPlanAndReport/
  Component level: docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/

[File Naming -- Hard Rule for This Directory]
  All documents must start with **the version number they belong to**: v{version}_{DocType}[_{YYYYMMDD}].md
    v0.3_DevPlan.md        -- the version's overall dev plan (this template)
    v0.3_BE_DevPlan.md     -- backend-specific dev plan
    v0.3_FE_DevPlan.md     -- frontend-specific dev plan
    v0.3_DevReport.md      -- the version's dev report / progress review
    v0.3_Snapshot.md       -- saved context
    v0.3_Handover.md       -- AI-to-AI handover
    v0.3_ImprovePlan.md    -- improvement plan
  When multiple documents of the same type exist within one version, append a date suffix: v0.3_DevPlan_20260320.md
  The version number is taken from the component's {component}_version_plan.md;
  cross-version organizational plans (RenamePlan / ReorganizePlan) use **the current version number at the time they are initiated**.

[Division of Labor Among the Three Dev Plan Templates]
  _DevPlan.md (this file) = **version master plan**. Aimed at a single complete AI coding
      session, it is the battle briefing handed to the executor, leaning toward
      "what you must know before starting work": what to read (with 🔴 Must Read /
      🟠 Important / 🟡 Reference priority), what the hard constraints are,
      where the current code stands, the Phase breakdown, the deliverable file list,
      a route quick-reference, locked tech versions, and notes for the executor.
      Used when frontend and backend are completed together within the same minor version.
  _BE_DevPlan.md = backend-specific plan. Referenced design document index (this
      component / platform conventions / shared library / previous-version code --
      four categories), tech stack and architecture constraints, target directory
      structure, database script plan, development tasks by Phase, API endpoint
      routing table, development notes, completion status review.
  _FE_DevPlan.md = frontend-specific plan. Background and motivation, referenced
      document index, technical approach (architecture changes / new directories /
      API integration endpoint list), Phase tasks, final routing registration state,
      file change summary, verification method.

[Authoritative Scope Definition]
  A dev plan may only implement the version scope already defined in {component}_version_plan.md.
  Anything beyond that scope must first go back and revise version_plan; the plan must not
  expand scope on its own.

[Companion Documents]
  Plan -> Execute -> v{version}_DevReport.md fills in completion status;
  Mid-way session/model change -> v{version}_Handover.md for handover;
  Interruption requiring context to be saved -> v{version}_Snapshot.md.

[Template File Name]
  The leading `_` is only a template marker; rename per the naming rule above when copying.
-->

# {ComponentDisplayName} v{TargetVersion} Dev Plan (Version Master Plan)

**Document**: v{TargetVersion}_DevPlan.md
**Created**: {YYYY-MM-DD}
**Target Version**: v{TargetVersion} ({version codename, e.g. "frontend only, hardcoded data"})
**Executor**: {AI model name}
**Status**: {⚪ Pending / 🟡 In Progress / 🟢 Completed}

---

## 1 Prerequisites

### 1.1 Documents That Must Be Read First (In Order)

| # | Document Path | Reading Focus | Priority |
|---|---------|---------|--------|
| 1 | `docs/components/{component}/{component}_version_plan.md` | **All of §{n} v{TargetVersion}**: feature scope, tech stack, what is not included. This is the authoritative scope definition for v{TargetVersion}. | 🔴 Must Read |
| 2 | `docs/components/{component}/{component}_tech_design.md` | **§2 Class Definitions** ({basis for the frontend TypeScript type definitions; note every member of each class and the §{n} enums}), **§{n} Source Code Directory Layout** ({directory tree}). Note the ⚠️ version markers in each section. | 🔴 Must Read |
| 3 | `docs/components/{component}/{component}_uiux.md` | **§2 Page Overview** ({n} pages overview), **§3 Page Details** ({global style rules + per-page detailed UI design}), **§4 Route Definitions** ({n} routes). Note the version markers throughout. | 🔴 Must Read |
| 4 | `docs/uiux_design_specification.md` | **§{n} Color Scheme**, **§{n} Tech Stack Versions**, **§{n} State Management**, **§{n} Responsive Design**, **§{n} Performance Optimization**, **§{n} Testing Strategy**, **§{n} File Naming**. | 🟠 Important |
| 5 | `docs/naming_convention.md` | {Frontend/backend code naming conventions. Class name prefixes (C/E/I), variable naming rules.} | 🟡 Reference |
| 6 | `docs/technical_overview.md` | {§{n} API design conventions, §{n} frontend development conventions} | 🟡 Reference |
| 7 | `docs/components/{component}/{component}_business_desc.md` | {Business semantics, for understanding field meanings} | 🟡 Reference |

> Priority: 🔴 Must Read (cannot start work without reading) | 🟠 Important (affects implementation approach) | 🟡 Reference (consult when a question arises)

### 1.2 HTML Mockups (Source of Mock Data)

All hardcoded data on every page in v{TargetVersion} comes **directly from** the sample data in the following HTML files. Before development, open and read each one individually, extracting the table data, form values, dropdown options, etc.

| Page | Page Name | HTML Path |
|------|--------|----------|
| {P01} | {Page Name} | `docs/components/{component}/ui_page_design/P01  {Page Name}/code.html` |
| {P02} | {Page Name} | `docs/components/{component}/ui_page_design/P02  {Page Name}/code.html` |
| {P03} | {Page Name} | `docs/components/{component}/ui_page_design/P03  {Page Name}/code.html` |

> `screen.png` in the same directory is a rendered screenshot, used to check layout and color scheme.
> **Data consistency requirement**: when the same business object appears on different pages, its hardcoded data must be consistent (the same ID must correspond to the same record), otherwise page navigation will show mismatched data.

### 1.3 Related Reference Documents

| # | Document Path | Reference Content |
|---|---------|---------|
| 1 | `src/frontend/packages/{completed reference component}/` | {The complete implementation of an already-completed component, as a reference for structure and code style} |
| 2 | `docs/components/{component}/ai_dev_history/01_DocReviewRefine/{most recent review}.md` | {Decisions already confirmed in the design document review conclusions} |
| 3 | `project_directory_plan.md` §{n} | {Directory structure conventions} |
| 4 | `docs/common_lib/common_frontend.md` | {Shared frontend components and utilities} |

### 1.4 Core Constraints (v{TargetVersion} Hard Constraints)

> The following are the hard constraints this version **absolutely must not cross**. Crossing them means rework.

| # | Hard Constraint | Description |
|---|------|------|
| 1 | **{No backend API integration of any kind}** | {All data comes from hardcoded constants in the `mock/` directory; fetch/axios calls are prohibited} |
| 2 | **{No authentication or permissions}** | {Pages are directly reachable, no login check} |
| 3 | **{No responsive layout}** | {Per uiux_design_specification.md §{n}, desktop only} |
| 4 | **{No performance optimization}** | {lazy load / memo / code split are prohibited, keep the code straightforward and readable} |
| 5 | **{No automated tests}** | {Per uiux_design_specification.md §{n}, not required for this version} |
| 6 | **{Do not upgrade dependency versions on your own}** | {Version lock is in §6, consistent with platform conventions} |
| 7 | **{Do not add pages or fields not present in the design documents}** | {Any discovered design gap should be recorded as an open issue, not filled in at your own discretion} |
| 8 | **{File naming must be strict kebab-case}** | {Frontend directory and file naming convention} |

---

## 2 Current Project State

{The state of the code before work begins. Explain what already exists, what is empty, and where this round of work picks up.}

| Path | Current State | Action This Round |
|------|------|---------|
| `src/frontend/packages/{component}/` | {Does not exist / empty shell / already has {n} pages} | {Create / fill in / rework} |
| `src/frontend/packages/{reference component}/` | {v{n} completed} | {Reference only, do not modify} |
| `src/frontend/packages/shared/` | {Already has {component list}} | {Reuse, extend if needed} |
| `src/frontend/{monorepo config}` | {Current state} | {Register new package} |

---

## 3 Development Plan

> Phases run in series. Each Phase should compile independently once complete.

### Phase 1: {Phase name, e.g. Package Initialization}

| # | Task | Output | Notes | Status |
|---|------|------|------|------|
| 1.1 | {Create package directory and config} | `packages/{component}/package.json` etc. | {Dependency versions strictly per §6 lock} | |
| 1.2 | {Register in monorepo} | `{workspace config file}` | {Notes} | |

### Phase 2: {Phase name, e.g. TypeScript Type Definitions}

| # | Task | Output | Notes | Status |
|---|------|------|------|------|
| 2.1 | {Define business object interfaces} | `src/models/{resource}.ts` | {Fields must correspond one-to-one with tech_design §2, including nullability and defaults} | |
| 2.2 | {Define enums} | `src/models/enums.ts` | {E prefix, values consistent with tech_design §{n}} | |

### Phase 3: {Phase name, e.g. Mock Data}

| # | Task | Output | Notes | Status |
|---|------|------|------|------|
| 3.1 | {Extract data from HTML mockups} | `src/mock/{resource}.ts` | {Data source per §1.2; IDs must be consistent across pages} | |

### Phase 4: {Phase name, e.g. Page Component Implementation -- List Page}

| # | Task | Output | Notes | Status |
|---|------|------|------|------|
| 4.1 | {Implement P{nn}} | `src/pages/{page-kebab}/index.tsx` | {Per uiux §3.x: query filter area, action button area, list area, row interactions} | |

### Phase 5: {Phase name, e.g. Page Component Implementation -- Detail Page}

| # | Task | Output | Notes | Status |
|---|------|------|------|------|
| 5.1 | {Implement P{nn}} | `src/pages/{page-kebab}/index.tsx` | {Toggle between create/read-only/edit modes} | |

### Phase 6: {Phase name, e.g. Route Integration}

| # | Task | Output | Notes | Status |
|---|------|------|------|------|
| 6.1 | {Register routes} | `src/routes.tsx` | {Aligned one-to-one with uiux §4, see the §5 quick-reference table} | |
| 6.2 | {Mount into Portal menu} | `{portal menu config}` | {Menu structure per uiux §2.2} | |

### Phase 7: {Phase name, e.g. Integration Verification}

| # | Task | Output | Notes | Status |
|---|------|------|------|------|
| 7.1 | {Build verification} | -- | `{build command}` with no errors | |
| 7.2 | {Visual check page by page} | -- | {Compare against `screen.png` and uiux §3.x} | |
| 7.3 | {Navigation flow verification} | -- | {Walk through each path against the uiux page_flow diagram} | |

---

## 4 File Inventory Summary

### 4.1 New Files

```
src/frontend/packages/{component}/
├── package.json
├── tsconfig.json
├── src/
│   ├── index.ts
│   ├── routes.tsx
│   ├── models/
│   │   ├── {resource}.ts
│   │   └── enums.ts
│   ├── mock/
│   │   └── {resource}.ts
│   ├── stores/
│   │   └── {resource}Store.ts
│   └── pages/
│       ├── {page-1-kebab}/index.tsx
│       └── {page-2-kebab}/index.tsx
```

### 4.2 Modified Existing Files

| # | File Path | Change |
|---|---------|---------|
| 1 | `{monorepo workspace config}` | {Register new package} |
| 2 | `{portal menu/route config}` | {Mount {n} menu items} |
| 3 | `{shared component}` | {Add {component}} |

---

## 5 Route Quick Reference

| # | Path | Page Component | Page ID | Mode | Params |
|---|------|---------|---------|------|------|
| 1 | `{/component/resource}` | `{ResourceListPage}` | {P01} | {List} | -- |
| 2 | `{/component/resource/new}` | `{ResourceDetailPage}` | {P02} | {Create} | -- |
| 3 | `{/component/resource/:id}` | `{ResourceDetailPage}` | {P02} | {Read-only} | `{id}` |
| 4 | `{/component/resource/:id/edit}` | `{ResourceDetailPage}` | {P02} | {Edit} | `{id}` |

**Total routes**: {n}. Must exactly match `{component}_uiux.md` §4.

---

## 6 Locked Tech Versions

> Versions come from `docs/uiux_design_specification.md` §{n} and `docs/technical_overview.md` §5.
> **Do not upgrade on your own** -- if there is a version conflict, raise it as a question rather than changing the version unilaterally.

| Dependency | Locked Version | Purpose |
|------|---------|------|
| {React} | {18.x} | {UI framework} |
| {TypeScript} | {5.x} | {Type system} |
| {Build tool} | {Version} | {Build} |
| {Routing library} | {Version} | {Routing} |
| {State library} | {Version} | {State management} |
| {UI component library} | {Version} | {Base components} |

---

## 7 Notes for the Executor

> This section captures the **pitfalls hit during the previous round of development** and points that are easy to misunderstand. Read through it fully before starting work.

| # | Note |
|---|------|
| 1 | {Sections marked ⚠️ in the design documents are "to be completed" or "not implemented for now" -- when encountered, skip per the marker and do not fill it in yourself} |
| 2 | {List page "selection" has two representations (checkbox column vs. whole-row highlight), which may differ across pages -- implement strictly per the description in uiux §3.x.3} |
| 3 | {Date, number, and empty-value display formats all follow uiux_design_specification.md §{n} uniformly -- do not format them per-page yourself} |
| 4 | {Enum values displayed in the UI differ from the code values -- code uses the E-prefixed enum value, UI display uses a mapping table} |
| 5 | {Soft-deleted records do not disappear from the list, but are shown in a gray style, see uiux_design_specification.md §{n}} |
| 6 | {If you find contradictions or gaps in the design documents, **do not decide on your own** -- record it in §8 for the follow-up review} |
| 7 | {Before submitting, check off each item in §1.4's hard constraints one by one to confirm nothing was crossed} |

---

## 8 Additional Notes

### 8.1 Design Issues Found During Development

> Document gaps, contradictions, or ambiguities found by the executor during development are recorded here, for handling in a follow-up review.

| # | Location | Issue | Temporary Handling | Engineer's Response |
|---|------|------|-------------|-------------|
| 1 | `{document} §{section}` | {Issue description} | {How it was handled this round} | |

> The "Engineer's Response" column is filled in manually by the engineer; leave it blank when AI-generated.

### 8.2 Intentional Deviations From the Design Documents

| # | Design Document Requirement | Actual Implementation | Reason | Needs Doc Update? |
|---|-------------|---------|------|---------------|
| 1 | {Requirement} | {Implementation} | {Reason} | {Yes/No} |

### 8.3 Open Items

| # | Open Item | Reason | Planned Version |
|---|--------|------|---------|
| 1 | {Item} | {Reason} | {v{n}} |
