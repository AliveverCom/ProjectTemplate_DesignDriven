<!--
TEMPLATE NOTES (delete this comment block when using)

[DOCUMENT LEVEL] Component-level version plan.

[TARGET LOCATION] docs/components/{component_code_name}/{component_code_name}_version_plan.md
  - Replace the leading `_` with the component code name, e.g. `material_collector_version_plan.md`.

[BOUNDARY AGAINST THE PLATFORM-LEVEL DOCUMENT]
  - The platform-level docs/version_plan.md (generated from templates/platform/_version_plan.md)
    defines the **general version numbering convention**: the five segments of vA.B.C.D.E, the
    milestone ladder (v0.1 Baseline Design Docs -> v1.0 First Official Release), the component
    `VERSION` file strategy and the Git tag naming rules.
  - This component-level document **must not redefine version number semantics**. It only answers
    "what does *this component's* v0.1 / v0.2 / ... deliver, what is included, what is explicitly
    excluded, and which parts of the tech stack are added".
  - This document is the **authoritative scope definition** for the dev plans under
    ai_dev/02_DevPlanAndReport: a dev plan may only implement the scope defined here, and going
    beyond it requires coming back and revising this document first.
  - This document is also the **baseline document** for `v{version}_DevReport.md` under
    ai_dev/02_DevPlanAndReport.

[HOW TO FILL IT IN]
  - The "Out of Scope" section is the most important part of this template. An AI developer is most
    likely to overreach and implement next version's features, so every hard constraint must be
    written down explicitly (no responsive layout, no performance tuning, no database, no automated
    tests, ...).
  - Each version's tech stack lists only what **that version adds**; never repeat what an earlier
    version already introduced.
  - The standard ladder below (v0.1 -> v1.0) is the platform default. Keep the version numbers and
    their semantics; fill in what your component ships in each of them. If a version does not apply
    to your component (for example v0.5 when there are no pipelines or daemons), keep the section
    and write "Not applicable to this component" rather than deleting and renumbering.
-->

# {ComponentName} ({component_code_name}) Version Plan

**Document Version**: {x.y.z}
**Created**: {YYYY-MM-DD}

> **Version number semantics**: the meaning of the `vA.B.C` version numbers used here, the milestone
> definitions, and the `VERSION` file and Git tag rules all follow the platform-level common version
> specification in [docs/version_plan.md](../../version_plan.md)
> (template source: `templates/platform/_version_plan.md`) and
> [project_directory_plan.md §5](../../../project_directory_plan.md#c5).
> This document does not redefine any of them — it defines only the **feature scope** of each
> version of this component.

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Version Plan Overview](#2-version-plan-overview)
- [3 v0.1 — Baseline Design Docs](#3-v01--baseline-design-docs)
  - [3.1 Objective](#31-objective)
  - [3.2 Scope](#32-scope)
  - [3.3 Tech Stack](#33-tech-stack)
  - [3.4 Out of Scope](#34-out-of-scope)
- [4 v0.2 — Frontend Only (Hardcoded Data)](#4-v02--frontend-only-hardcoded-data)
  - [4.1 Objective](#41-objective)
  - [4.2 Added Scope](#42-added-scope)
  - [4.3 Added Tech Stack](#43-added-tech-stack)
  - [4.4 Out of Scope](#44-out-of-scope)
- [5 v0.3.1 — Technical Design Completed](#5-v031--technical-design-completed)
  - [5.1 Objective](#51-objective)
  - [5.2 Added Scope](#52-added-scope)
  - [5.3 Added Tech Stack](#53-added-tech-stack)
  - [5.4 Out of Scope](#54-out-of-scope)
- [6 v0.3.2 — Backend Implementation](#6-v032--backend-implementation)
  - [6.1 Objective](#61-objective)
  - [6.2 Added Scope](#62-added-scope)
  - [6.3 Added Tech Stack](#63-added-tech-stack)
  - [6.4 Out of Scope](#64-out-of-scope)
- [7 v0.4 — Three-Tier Integration](#7-v04--three-tier-integration)
  - [7.1 Objective](#71-objective)
  - [7.2 Added Scope](#72-added-scope)
  - [7.3 Added Tech Stack](#73-added-tech-stack)
  - [7.4 Out of Scope](#74-out-of-scope)
- [8 v0.5 — Pipelines & Daemons](#8-v05--pipelines--daemons)
  - [8.1 Objective](#81-objective)
  - [8.2 Added Scope](#82-added-scope)
  - [8.3 Added Tech Stack](#83-added-tech-stack)
  - [8.4 Out of Scope](#84-out-of-scope)
- [9 v0.6 — Platform Integration](#9-v06--platform-integration)
  - [9.1 Objective](#91-objective)
  - [9.2 Added Scope](#92-added-scope)
  - [9.3 Added Tech Stack](#93-added-tech-stack)
  - [9.4 Out of Scope](#94-out-of-scope)
- [10 v0.7 – v0.8 — Reserve](#10-v07--v08--reserve)
- [11 v1.0 — First Official Release](#11-v10--first-official-release)
- [12 Version Comparison Matrix](#12-version-comparison-matrix)
- [Change Log](#change-log)

---

## 2 Version Plan Overview

| Version | Code Name | Goal in One Line | Status |
|---------|-----------|------------------|--------|
| `v0.1` | Baseline Design Docs | {Design documents and page UI mockups complete} | {🟢/🟡/🔴/⚪} |
| `v0.2` | Frontend Only (Hardcoded Data) | {Every page clickable end to end on hardcoded data} | {🟢/🟡/🔴/⚪} |
| `v0.3.1` | Technical Design Completed | {Backend technical design and frontend API integration design complete} | {🟢/🟡/🔴/⚪} |
| `v0.3.2` | Backend Implementation | {Backend, database and deployment scripts implemented in one pass} | {🟢/🟡/🔴/⚪} |
| `v0.4` | Three-Tier Integration | {Frontend, backend and database wired together and tested} | {🟢/🟡/🔴/⚪} |
| `v0.5` | Pipelines & Daemons | {Data / compute pipelines and daemons implemented} | {🟢/🟡/🔴/⚪} |
| `v0.6` | Platform Integration | {Component integrated end to end with the platform framework} | {🟢/🟡/🔴/⚪} |
| `v0.7` – `v0.8` | Reserve | {Mid-course features, requirement changes, refactoring} | {🟢/🟡/🔴/⚪} |
| `v1.0` | First Official Release | {First official release} | {🟢/🟡/🔴/⚪} |

> **Status legend**: 🟢 Done | 🟡 Partial | 🔴 Not Implemented (but planned to be done by now) | ⚪ Out of Scope / Not Started
>
> The Status column is filled in from the conclusions of `v{version}_DevReport.md` under
> `ai_dev/02_DevPlanAndReport`. Never guess it by hand.

---

## 3 v0.1 — Baseline Design Docs

### 3.1 Objective

{Produce the complete set of baseline design documents for this component, so that the frontend work
in v0.2 can start from a specification rather than from imagination. No source code is written in
this version.}

### 3.2 Scope

| # | Deliverable | Detail |
|---|-------------|--------|
| 1 | Product / business documentation | `{component_code_name}_business_desc.md` complete: component overview, key concepts, business objects and their attributes, business process |
| 2 | Backend technical design — **entity definitions only** | `{component_code_name}_tech_design.md` §2 contains the entity (class) definitions and enum definitions **only**. Nothing else in the backend design is written yet. |
| 3 | Frontend UI/UX document — main content | `{component_code_name}_uiux.md` complete for: page flow diagram, page list, navigation menu, page-object mapping, and the layout / elements / behaviours of every page |
| 4 | Page UI design | Under `ui_page_design/`, **every page has its own `.html` and `.png`** — `P{nn}  {PageName}/code.html` plus `P{nn}  {PageName}/screen.png` |
| 5 | Diagrams | `{component_code_name}_business_process.mmd` + `.png`, `{component_code_name}_class_diagram.mmd` + `.png`, `ui_page_design/{component_code_name}_page_flow.mmd` + `.png` |

### 3.3 Tech Stack

| Item | Value |
|------|-------|
| Documentation format | Markdown, per `templates/` |
| Diagrams | Mermaid (`.mmd`) rendered to `.png`, per `templates/mermaid/mmd_style_guide.md` |
| Page mockups | {HTML mockup tool, e.g. Google Stitch} → `code.html` + `screen.png` |

### 3.4 Out of Scope

> **Hard constraints for v0.1 — none of the following may be produced in this version:**

| # | Explicitly Not Done | Why |
|---|--------------------|-----|
| 1 | Any source code | This is a documentation-only version |
| 2 | Backend class design, database design, API design | Only entity definitions belong in v0.1; the rest lands in v0.3.1 |
| 3 | The design of how each frontend feature calls the backend API | Lands in v0.3.1 |
| 4 | `deployment/` directory and any script | Lands in v0.2 |
| 5 | {Other items this component deliberately defers} | {Reason} |

---

## 4 v0.2 — Frontend Only (Hardcoded Data)

### 4.1 Objective

{Build the complete frontend of this component against hardcoded data, so that every page and every
navigation path defined in the UI/UX document can be clicked through and reviewed before any backend
exists.}

### 4.2 Added Scope

| # | Deliverable | Detail |
|---|-------------|--------|
| 1 | Frontend package | `src/frontend/packages/{component-code-name}/` — all pages listed in `{component_code_name}_uiux.md` §2.1 |
| 2 | Routing | All routes from `{component_code_name}_uiux.md` §4 registered and reachable |
| 3 | Type definitions | `src/models/` — TypeScript types derived from the entity definitions in `{component_code_name}_tech_design.md` §2 |
| 4 | Hardcoded data | `src/mock/` — sample data taken from the `code.html` mockups produced in v0.1 |
| 5 | State management | `src/stores/` — {one store per entity plus a UI store} |
| 6 | `deployment/` directory | Created in this version, with the frontend one-click scripts below |
| 7 | One-click frontend scripts | `build-all-frontend` — build every frontend package<br>`start-frontend` — start the frontend services<br>`kill-frontend` — stop the frontend services |
| 8 | README commands | `README.md` documents the three commands above so a reviewer can run the frontend in one shot |

### 4.3 Added Tech Stack

| Item | Value | Note |
|------|-------|------|
| Frontend framework | {e.g. React 18.x} | Version locked by `technical_overview.md` §5.2 |
| Build tool | {e.g. Vite 5.x} | |
| State management | {e.g. Zustand} | |
| Script shell | {e.g. bash / PowerShell} | For the `deployment/` one-click scripts |

### 4.4 Out of Scope

> **Hard constraints for v0.2 — none of the following may be produced in this version:**

| # | Explicitly Not Done | Why |
|---|--------------------|-----|
| 1 | Any backend code or API call | The frontend reads `src/mock/` only |
| 2 | Database of any kind | Lands in v0.3.2 |
| 3 | Backend one-click scripts, build-all / start-all / kill-all | Land in v0.3.2 |
| 4 | Responsive layout | {Desktop only in this project} |
| 5 | Performance tuning (lazy loading, memoization, code splitting) | Deliberately deferred; do not introduce it on your own |
| 6 | Automated tests | {Deferred to v0.4} |
| 7 | Login / authentication | Handled by the platform portal, integrated in v0.6 |

---

## 5 v0.3.1 — Technical Design Completed

### 5.1 Objective

{Complete every remaining part of the technical design, so that v0.3.2 can implement the whole
backend in a single pass without any open design question.}

### 5.2 Added Scope

| # | Deliverable | Detail |
|---|-------------|--------|
| 1 | Class design | `{component_code_name}_tech_design.md` §2 extended beyond entities: full class definitions, responsibilities, member constraints, cross-module reference classes |
| 2 | Database design | §4 — every table, column, type, constraint and index; dynamic sharded table templates and their creation timing; `{component_code_name}_db_schema.mmd` + `.png` |
| 3 | Remaining backend logic | §3 — programs / services / daemons of this component, their responsibilities, main loops, retry rules and known limitations |
| 4 | Standard CRUD API design | §5 — for every business object: list, get, create, update, delete; request parameters, examples, responses, error codes, writable-field scope |
| 5 | Special query structure design | §5 — non-CRUD endpoints: aggregations, statistics, batch operations, cross-table queries; and the SQL expression mapping for any JSONB sub-field used in sorting or filtering |
| 6 | API diagram | `{component_code_name}_api.mmd` + `.png` |
| 7 | Frontend API integration design | `{component_code_name}_uiux.md` extended: for **every feature on every page**, which endpoint it calls with which parameters, and what it does with the response — the `Page Behaviors` table Action column now carries concrete REST endpoints |

### 5.3 Added Tech Stack

| Item | Value | Note |
|------|-------|------|
| API contract format | {e.g. OpenAPI 3.x} | Contract file location per `project_directory_plan.md` §4.4 |
| Database | {e.g. PostgreSQL 17.x} | Design only in this version; nothing is created yet |

### 5.4 Out of Scope

> **Hard constraints for v0.3.1 — none of the following may be produced in this version:**

| # | Explicitly Not Done | Why |
|---|--------------------|-----|
| 1 | Any backend source code | This is a documentation-only version; implementation is v0.3.2 |
| 2 | Any database object | Scripts are written in v0.3.2 |
| 3 | Changing the frontend to call real APIs | The frontend stays on `src/mock/` until v0.4 |

---

## 6 v0.3.2 — Backend Implementation

### 6.1 Objective

{Implement, in a single pass, everything specified in the backend technical design completed in
v0.3.1 — plus the database initialisation scripts and the full deployment tooling.}

### 6.2 Added Scope

| # | Deliverable | Detail |
|---|-------------|--------|
| 1 | API contract definitions | {e.g. `src/apis/{component_code_name}/openapi.yaml`} — every endpoint from §5 |
| 2 | Backend class implementation | `internal/model/`, `internal/enum/` — the classes and enums from §2 |
| 3 | Backend database access | `internal/repository/` — data access for every table in §4 |
| 4 | Backend service encapsulation | `internal/service/` + `internal/handler/` — the business logic and HTTP layer for every endpoint in §5 |
| 5 | Schema init script | `sql/init_schema.sql` — create database and tables |
| 6 | Data init script | `sql/init_data.sql` — the reference / lookup data the system needs to run |
| 7 | Demo data script | `sql/demo_data.sql` — sample rows for development and review |
| 8 | One-click backend scripts | `build-all-backend`, `start-backend`, `kill-backend` in `deployment/` |
| 9 | One-click global scripts | `build-all`, `start-all`, `kill-all` in `deployment/` — frontend plus backend in one command |
| 10 | README commands **replaced** | `README.md` switches from the frontend-only commands to `build-all` / `start-all` / `kill-all`, so a user can bring the whole system up in one shot |
| 11 | Ubuntu provisioning script | `deployment/` gains a provisioning script for Ubuntu. The database is provided at the **operating-system level** by default; every product module is built as a **Docker image**. |

### 6.3 Added Tech Stack

| Item | Value | Note |
|------|-------|------|
| Backend language | {e.g. Go 1.25.x} | Version locked by `technical_overview.md` §5.1 |
| Web framework | {e.g. Gin 1.x} | |
| ORM | {e.g. GORM 2.x} | |
| Database | {e.g. PostgreSQL 17.x} | Provided at OS level by the Ubuntu provisioning script |
| Migration tool | {e.g. golang-migrate} | |
| Containerisation | {e.g. Docker} | Every product module ships as an image |

### 6.4 Out of Scope

> **Hard constraints for v0.3.2 — none of the following may be produced in this version:**

| # | Explicitly Not Done | Why |
|---|--------------------|-----|
| 1 | Changing the frontend to call the real backend | That is exactly what v0.4 is for |
| 2 | Pipelines / daemons | Land in v0.5 |
| 3 | Integration with the platform framework and portal | Lands in v0.6 |
| 4 | Performance tuning and load testing | {Deferred} |

---

## 7 v0.4 — Three-Tier Integration

### 7.1 Objective

{Wire the frontend, the backend and the database together and test the component end to end. This is
the first fully runnable version of the component.}

### 7.2 Added Scope

| # | Deliverable | Detail |
|---|-------------|--------|
| 1 | Frontend API client | `src/services/api.ts` — every endpoint the pages need, wrapped in one place |
| 2 | Pages switched to real data | Every page reads from the API; `src/mock/` is no longer referenced by production code |
| 3 | API debug page | A page that exercises every endpoint of this component, for development and troubleshooting |
| 4 | Integration testing | Every business function in `{component_code_name}_business_desc.md` §4 verified against the running three-tier stack |
| 5 | Defect fixing | Issues found during integration recorded and fixed per `ai_dev/04_DebugFix` |

### 7.3 Added Tech Stack

| Item | Value | Note |
|------|-------|------|
| HTTP client | {e.g. axios / fetch wrapper} | |
| Test approach | {e.g. manual walkthrough plus service-layer unit tests} | |

### 7.4 Out of Scope

| # | Explicitly Not Done | Why |
|---|--------------------|-----|
| 1 | Pipelines / daemons | Land in v0.5 |
| 2 | Cross-component calls and portal integration | Land in v0.6 |

---

## 8 v0.5 — Pipelines & Daemons

> **Applicability**: keep this section even if the component has no pipeline or daemon — write
> "Not applicable to this component" under Scope rather than deleting and renumbering the sections.

### 8.1 Objective

{Implement the data pipelines, compute pipelines and daemons of this component. The technical design
document comes first, then development and testing.}

### 8.2 Added Scope

| # | Deliverable | Detail |
|---|-------------|--------|
| 1 | Pipeline / daemon technical design | One design document per program under `sub_{program}/`, per `templates/component/_sub_module_design.md` — **written and reviewed before any code** |
| 2 | Implementation | {The daemon / pipeline programs themselves} |
| 3 | Scheduling and triggering | {How each program is started, its interval or trigger strategy, and its failure-retry rules} |
| 4 | Monitoring | {Run-state reporting so the component's monitoring page can display it} |
| 5 | Testing | {Each program tested against real data} |
| 6 | Deployment scripts extended | `deployment/` gains build / start / kill for the daemons, folded into `build-all` / `start-all` / `kill-all` |

### 8.3 Added Tech Stack

| Item | Value | Note |
|------|-------|------|
| {Scheduler / queue} | {version} | {purpose} |

### 8.4 Out of Scope

| # | Explicitly Not Done | Why |
|---|--------------------|-----|
| 1 | Writing code before the design document exists | The design-first rule is not negotiable |
| 2 | Platform-level orchestration | Lands in v0.6 |

---

## 9 v0.6 — Platform Integration

### 9.1 Objective

{Integrate this component, together with its pipelines, into the platform framework end to end.}

### 9.2 Added Scope

| # | Deliverable | Detail |
|---|-------------|--------|
| 1 | Portal integration | All routes of this component mounted into the platform portal; the navigation menu from `{component_code_name}_uiux.md` §2.2 wired up |
| 2 | Authentication and authorization | The component's pages and APIs respect the platform login state |
| 3 | Cross-component calls | Endpoints this component consumes from, or exposes to, other components verified against `technical_overview.md` §4.1 |
| 4 | Pipeline orchestration | {The component's pipelines driven by the platform orchestrator} |
| 5 | End-to-end walkthrough | The platform-level business process in `business_overview.md` §2 walked through with this component in place |

### 9.3 Added Tech Stack

| Item | Value | Note |
|------|-------|------|
| {Auth mechanism} | {version} | {purpose} |
| {Gateway / orchestrator} | {version} | {purpose} |

### 9.4 Out of Scope

| # | Explicitly Not Done | Why |
|---|--------------------|-----|
| 1 | {Items deliberately deferred to v0.7 – v0.8} | {Reason} |

---

## 10 v0.7 – v0.8 — Reserve

These two slots are reserve capacity. They are typically used for:

| # | Typical Use | Note |
|---|-------------|------|
| 1 | Features added mid-course | Features that were not in the original plan but are needed before the official release |
| 2 | Requirement changes | Reworking behaviour that v0.1 – v0.6 already delivered |
| 3 | Code refactoring | Structural cleanups that do not change behaviour |
| 4 | Hardening | {Bug fixing, edge cases, error handling, logging} |

> Whenever a reserve slot is used, add a subsection here recording **what was done, why, and which
> earlier documents were revised as a result**. Anything that changes an earlier version's scope must
> be written back into that version's section, not only recorded here.

| Version | Used For | Date | Documents Revised |
|---------|----------|------|-------------------|
| `v0.7` | {What it was used for, or "Not used"} | {YYYY-MM-DD} | {Documents that had to be updated} |
| `v0.8` | {What it was used for, or "Not used"} | {YYYY-MM-DD} | {Documents that had to be updated} |

---

## 11 v1.0 — First Official Release

### 11.1 Objective

{First official release of the component as part of the platform.}

### 11.2 Release Checklist

| # | Item | Criterion | Status |
|---|------|-----------|--------|
| 1 | Documentation complete | Every design document at its final version, no `{placeholder}` and no "To Be Completed" markers left | {🟢/🟡/🔴} |
| 2 | Feature complete | Every business function in `business_desc.md` §4 implemented and verified | {🟢/🟡/🔴} |
| 3 | No P1 issues | No blocking issue left open in the latest `v{version}_DevReport.md` | {🟢/🟡/🔴} |
| 4 | Deployment reproducible | `build-all` / `start-all` / `kill-all` work on a clean machine using the Ubuntu provisioning script | {🟢/🟡/🔴} |
| 5 | Data initialisation | Schema init, data init and demo data scripts all run successfully on a fresh database | {🟢/🟡/🔴} |
| 6 | `VERSION` files updated | Every `VERSION` file of this component set to the release version | {🟢/🟡/🔴} |

---

## 12 Version Comparison Matrix

| Capability | v0.1 | v0.2 | v0.3.1 | v0.3.2 | v0.4 | v0.5 | v0.6 | v1.0 |
|-----------|------|------|--------|--------|------|------|------|------|
| Business / product documentation | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Backend entity definitions | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Full backend technical design | — | — | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Frontend UI/UX document (main content) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Frontend API integration design | — | — | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Page UI design (`.html` + `.png` per page) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Frontend pages runnable | — | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Frontend data source | — | mock | mock | mock | API | API | API | API |
| Backend service | — | — | — | ✅ | ✅ | ✅ | ✅ | ✅ |
| Database | — | — | — | ✅ | ✅ | ✅ | ✅ | ✅ |
| Frontend one-click scripts | — | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Backend one-click scripts | — | — | — | ✅ | ✅ | ✅ | ✅ | ✅ |
| `build-all` / `start-all` / `kill-all` | — | — | — | ✅ | ✅ | ✅ | ✅ | ✅ |
| Ubuntu provisioning + Docker images | — | — | — | ✅ | ✅ | ✅ | ✅ | ✅ |
| Three-tier integration tested | — | — | — | — | ✅ | ✅ | ✅ | ✅ |
| Pipelines / daemons | — | — | — | — | — | ✅ | ✅ | ✅ |
| Platform framework integration | — | — | — | — | — | — | ✅ | ✅ |
| Login / authentication | — | — | — | — | — | — | ✅ | ✅ |

> **Legend**: ✅ present in this version | — not present in this version | ⚪ out of scope
>
> This matrix is the handle for cross-version consistency checking: `v{version}_DevReport.md` walks
> it cell by cell against the actual code.

---

## Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {Change description} |
