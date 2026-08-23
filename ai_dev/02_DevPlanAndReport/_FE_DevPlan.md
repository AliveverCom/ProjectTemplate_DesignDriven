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
    v0.3_DevPlan.md        -- the version's overall dev plan
    v0.3_BE_DevPlan.md     -- backend-specific dev plan
    v0.3_FE_DevPlan.md     -- frontend-specific dev plan (this template)
    v0.3_DevReport.md      -- the version's dev report / progress review
    v0.3_Snapshot.md       -- saved context
    v0.3_Handover.md       -- AI-to-AI handover
    v0.3_ImprovePlan.md    -- improvement plan
  When multiple documents of the same type exist within one version, append a date suffix: v0.3_FE_DevPlan_20260326.md
  When backfilling across versions, list version numbers side by side: v0.1_v0.2_FE_DevPlan.md
  The version number is taken from the component's {component}_version_plan.md;
  cross-version organizational plans (RenamePlan / ReorganizePlan) use **the current version number at the time they are initiated**.

[Division of Labor Among the Three Dev Plan Templates]
  _DevPlan.md = **version master plan**. Aimed at a single complete AI coding session,
      it is the battle briefing handed to the executor, leaning toward "what you must
      know before starting work": what to read (with 🔴 Must Read / 🟠 Important /
      🟡 Reference priority), what the hard constraints are, where the current code
      stands, the Phase breakdown, the deliverable file list, a route quick-reference,
      locked tech versions, and notes for the executor. Used when frontend and backend
      are completed together within the same version.
  _BE_DevPlan.md = **backend-specific plan**. Referenced design document index (this
      component / platform conventions / shared library / previous-version code --
      four categories), tech stack and architecture constraints, target directory
      structure, database script plan, development tasks by Phase, API endpoint
      routing table, development notes, completion status review.
  _FE_DevPlan.md (this file) = **frontend-specific plan**. Background and motivation,
      referenced document index, technical approach (architecture changes / new
      directories / API integration endpoint list), Phase tasks, final routing
      registration state, file change summary, verification method.

[Structural Differences From _BE_DevPlan.md]
  - The frontend plan is usually driven by a **Dev Report** (a gap is found -> it gets
    filled in), so section 0 is "Background and Motivation" rather than "Document
    Index"; the document index is pushed down to section 1.
  - Two extra sections are added, "Final Routing Registration State" and "File Change
    Summary," because frontend deliverables are accepted per file and per route.

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

# {ComponentDisplayName} Frontend Dev Plan -- v{Version} {Version Codename}

**Document**: v{TargetVersion}_FE_DevPlan.md
**Date**: {YYYY-MM-DD}
**Component**: {component}
**Target**: {This round's goal, e.g.: wrap up v0.1 (P04 collection task detail page) + fully implement the v0.2 frontend API integration layer}
**Developer**: AI ({model name})
**Prerequisite**: {Prerequisite conditions, e.g.: v0.1 frontend 6/7 pages completed (v0.1_FE_DevPlan.md); v0.3 backend completed (v0.3_BE_DevPlan.md)}
**Status**: {🟡 In Progress / 🟢 Fully Completed (backfilled plan, records the actual development done in this session) / ⚪ Not Started}

---

## 0 Background and Motivation

{Explain the source that drove this plan. If driven by a dev report, reference it and summarize its conclusions.}

Based on the review conclusions of `{driving document, e.g. v{version}_DevReport.md}`, the current module has the following gaps:

| # | Problem | Severity | Notes |
|---|------|--------|------|
| 1 | {P{nn} page missing} | **P1 Blocking** | {v0.1 plan called for {n} routes, only {n} actually exist; {the entry button for the related page is disabled}} |
| 2 | {Frontend not integrated with backend API} | **P1 Gap** | {Backend fully implemented through v{n} (real database), frontend still stuck on v{n} mock data}} |
| 3 | {`services/` API client directory missing} | **P2** | {Required by the v{n} milestone; frontend has no HTTP request wrapper at all} |
| 4 | {P{nn} API debug page missing} | **P2** | {Required by the v{n} milestone, for convenient backend debugging} |

Goal of this plan: {complete the outstanding v{n} items and all v{n} frontend-layer work in one pass, so that frontend and backend are fully connected.}

---

## 1 Referenced Document Index

> **⚠️ Must Read Before Development**: the following files contain all the specifications and reference implementations needed for this round of frontend development.

| # | File Path | Notes |
|---|---------|------|
| 1 | `docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{version}_DevReport.md` | The document driving this round of development -- {dev report / progress review conclusions} |
| 2 | `docs/components/{component}/{component}_uiux.md` | **Entire document** -- page list (§2.1), navigation menu (§2.2), Page-Object-API mapping (§2.3), per-page design (§3), route definitions (§4) |
| 3 | `docs/components/{component}/{component}_tech_design.md` §5 | API endpoint definitions ({n} endpoints total, {n} to be integrated in this round) |
| 4 | `docs/uiux_design_specification.md` | Platform-level UI/UX conventions: page design patterns, control styles, color scheme, state management, data formatting |
| 5 | `docs/naming_convention.md` | Type naming (C/E/I prefixes), file naming (kebab-case) |
| 6 | `src/backend/{component}/internal/handler/router.go` | Backend route registration (confirm endpoint paths and parameters) |
| 7 | `src/backend/{component}/pkg/response.go` | Unified response structure `{ApiResponse<T>}` + `{PaginatedData<T>}` |
| 8 | `src/frontend/packages/{reference component}/src/services/api.ts` | Reference pattern -- API client wrapper of an existing component |
| 9 | `src/frontend/packages/{reference component}/src/pages/ApiDebug/index.tsx` | Reference pattern -- API debug page of an existing component |
| 10 | `docs/components/{component}/ui_page_design/P{nn}  {page name}/code.html` | Page HTML mockup (source of layout and mock data) |

---

## 2 Technical Approach

### 2.1 Architecture Change Summary

| Change | v{CurrentVersion} State | v{TargetVersion} Target |
|------|-----------------|-----------------|
| Data source | {hardcoded in `mock/` directory} | {`services/` calling backend REST API} |
| Request wrapper | {none} | {unified `apiClient` + interceptors + error handling} |
| Data fetching | {component references mock constants directly} | {`hooks/use{Resource}.ts` data hooks} |
| State management | {component-local state} | {{state library} store, one-store-per-entity} |
| Type definitions | {partially missing} | {`models/` covers all API response types} |
| Debugging capability | {none} | {P{nn} API debug page covers all this module's endpoints} |

### 2.2 New Directory Structure

```
src/frontend/packages/{component-kebab-case}/src/
├── services/                       # [New] API client layer
│   ├── apiClient.ts                # axios/fetch instance + interceptors + unified error handling
│   ├── {resource}Api.ts            # Per-resource endpoint wrapper
│   └── index.ts
├── hooks/                          # [New] Data hook layer
│   ├── use{Resource}List.ts
│   ├── use{Resource}Detail.ts
│   └── index.ts
├── models/                         # [Extended] TypeScript type definitions
│   ├── {resource}.ts               # C{Resource} interface
│   ├── enums.ts                    # E{Enum} enum
│   └── api.ts                      # ApiResponse<T> / PaginatedData<T>
├── pages/
│   ├── {page-kebab-case}/          # [New] Missing page
│   │   └── index.tsx
│   └── api-debug/                  # [New] API debug page
│       └── index.tsx
├── stores/                         # [Reworked] Connected to real data
└── routes.tsx                      # [Reworked] New routes registered
```

> Directory names use **kebab-case** (frontend convention), unlike the backend's snake_case.

### 2.3 API Integration Endpoint List

> This table is the acceptance checklist for frontend-backend integration testing. Endpoints must exactly match `{component}_tech_design.md` §5 and the backend `router.go`.

| # | Method | Endpoint | Frontend Wrapper Function | Consuming Page | tech_design | Status |
|---|--------|----------|-------------|---------|-------------|------|
| 1 | GET | `/api/v1/{resource}` | `{resource}Api.list()` | {P01, P06} | §5.1.1 | |
| 2 | GET | `/api/v1/{resource}/:{id}` | `{resource}Api.get()` | {P02, P07} | §5.1.2 | |
| 3 | POST | `/api/v1/{resource}` | `{resource}Api.create()` | {P07} | §5.1.3 | |
| 4 | PUT | `/api/v1/{resource}/:{id}` | `{resource}Api.update()` | {P07} | §5.1.4 | |
| 5 | DELETE | `/api/v1/{resource}/:{id}` | `{resource}Api.remove()` | {P06, P07} | §5.1.5 | |
| 6 | POST | `/api/v1/{resource}/batch-delete` | `{resource}Api.batchDelete()` | {P01} | §5.2.4 | |

**Endpoints integrated this round**: {n} / total backend endpoints {n}. {Endpoints not yet integrated and why: {explanation}.}

---

## 3 Development Task List

> Phases run in series; tasks within a Phase can run in parallel. The "Status" column is filled in during development.

### Phase 1: {Phase name, e.g. v0.1 Wrap-Up -- P04 Page Completion}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 1.1 | {Create P{nn} page component} | `pages/{page-kebab}/index.tsx` | {Implement layout and elements per uiux §3.x; use mock data initially} | |
| 1.2 | {Register route} | `routes.tsx` | {Path `{/path/:id}`, consistent with uiux §4} | |
| 1.3 | {Wire up entry point} | `pages/{list page}/index.tsx` | {Enable the previously-disabled "view" button and double-click behavior} | |

### Phase 2: {Phase name, e.g. API Client Layer}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 2.1 | {Create apiClient} | `services/apiClient.ts` | {baseURL from environment variable; response interceptor unwraps `data`; errors converted uniformly} | |
| 2.2 | {Wrap {resource} endpoints} | `services/{resource}Api.ts` | {Implement each item in the §2.3 list one by one, parameters and types strictly aligned with tech_design} | |
| 2.3 | {Define response types} | `models/api.ts` | {`ApiResponse<T>`, `PaginatedData<T>`} | |

### Phase 3: {Phase name, e.g. Data Hook Layer}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 3.1 | {List hook} | `hooks/use{Resource}List.ts` | {Wrap pagination, sorting, filter params, and loading/error state} | |
| 3.2 | {Detail hook} | `hooks/use{Resource}Detail.ts` | {Fetch by id, including 404 handling} | |

### Phase 4: {Phase name, e.g. Page Migration (Mock -> API)}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 4.1 | {P01 integrate API} | `pages/{page}/index.tsx` | {Remove mock references, switch to hooks; filtering/sorting become backend params} | |
| 4.2 | {P02 integrate API} | `pages/{page}/index.tsx` | {Same as above} | |

### Phase 5: {Phase name, e.g. API Debug Page}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 5.1 | {Create API debug page} | `pages/api-debug/index.tsx` | {Two-column layout per uiux §3.x; left side endpoint group tree, right side parameter form + response display} | |
| 5.2 | {Endpoint list config} | `pages/api-debug/endpoints.ts` | {Cover all {n} endpoints of this module, including cross-module call groups} | |

### Phase 6: {Phase name, e.g. Model Extension}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 6.1 | {Complete type definitions} | `models/{resource}.ts` | {Fields aligned one-to-one with the tech_design §2 class definitions, including nullability} | |
| 6.2 | {Complete enums} | `models/enums.ts` | {E prefix, values consistent with backend} | |

---

## 4 Final Routing Registration State

> This table is the acceptance checklist for `routes.tsx`, and must exactly match the route definitions in `{component}_uiux.md` §4.

| # | Path | Page Component | Page ID | Params | Menu Entry | Status |
|---|------|---------|---------|------|---------|------|
| 1 | `{/component/resource}` | `{ResourceListPage}` | {P01} | -- | {Menu: xxx} | |
| 2 | `{/component/resource/:id}` | `{ResourceDetailPage}` | {P02} | `{id}` | {None (entered from list)} | |
| 3 | `{/component/api-debug}` | `{ApiDebugPage}` | {P08} | -- | {Menu: API Debug} | |

**Total routes**: {n}.

---

## 5 File Change Summary

### 5.1 New Files ({n} total)

| # | File Path | Type | Notes |
|---|---------|------|------|
| 1 | `src/frontend/packages/{component}/src/services/apiClient.ts` | {Service} | {Notes} |
| 2 | `src/frontend/packages/{component}/src/services/{resource}Api.ts` | {Service} | {Notes} |
| 3 | `src/frontend/packages/{component}/src/hooks/use{Resource}List.ts` | {Hook} | {Notes} |
| 4 | `src/frontend/packages/{component}/src/pages/{page}/index.tsx` | {Page} | {Notes} |

### 5.2 Modified Files ({n} total)

| # | File Path | Change |
|---|---------|---------|
| 1 | `src/frontend/packages/{component}/src/routes.tsx` | {Add {n} routes} |
| 2 | `src/frontend/packages/{component}/src/pages/{page}/index.tsx` | {Remove mock references, switch to hooks} |
| 3 | `src/frontend/packages/{component}/src/models/{resource}.ts` | {Add {field} field} |
| 4 | `src/frontend/packages/{component}/package.json` | {Add dependency {dep}} |

---

## 6 Verification Method

| # | Verification Item | Method | Expected Result | Status |
|---|--------|------|---------|------|
| 1 | {Build passes} | `{npm run build}` | {No TypeScript errors} | |
| 2 | {Routes reachable} | {Visit each path in §4 one by one} | {Page renders normally, no blank screen} | |
| 3 | {API connectivity} | {Start backend service, open P{nn} API debug page and click each endpoint} | {All return `code: 0`, data structure consistent with tech_design} | |
| 4 | {List pagination} | {Page through, change sort, change filter} | {Request params correct, list refreshes} | |
| 5 | {Detail page round trip} | {List -> detail -> back} | {List state preserved after returning} | |
| 6 | {Error handling} | {Stop backend service and operate the page} | {Shows unified error message, no blank screen} | |
| 7 | {Consistent with mockup} | {Compare against `ui_page_design/P{nn}/screen.png`} | {Layout, font size, color scheme consistent} | |
| 8 | {Consistent with uiux document} | {Compare against `{component}_uiux.md` §3.x} | {Elements, interactions, behaviors fully covered} | |

---

## 7 Open Items and Follow-Up

| # | Open Item | Reason | Planned Version |
|---|--------|------|---------|
| 1 | {Item not implemented} | {Reason} | {v{n}} |
