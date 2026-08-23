# Common Version Plan

<!--
TEMPLATE NOTES (delete this comment block when using)
- Level: **platform-level version specification**. Place it in the `docs/` root; drop the leading underscore → `version_plan.md`.
- Difference from the component-level `{component}_version_plan.md` (hold this boundary):

  | Dimension | This document (platform-level version_plan.md) | Component-level {component}_version_plan.md |
  |-----------|-----------------------------------------------|--------------------------------------------|
  | Content | Version number **format** and **milestone semantics** | What each v0.x of that component **actually delivers** |
  | Count | Exactly one per platform | One per component |
  | Change rate | Very low (a change ripples across the whole platform) | Follows the component's iterations |
  | Relationship | Defines "what v0.3 means" | Defines "which features this component ships in v0.3" |

- Component-level template: `templates/component/_version_plan.md`.
- Once this document is final, component documents may only **reference** the milestone semantics defined here; they must not redefine what v0.x means.
-->

This document defines the unified version numbering convention for **all internal components** of the {PlatformName} platform.

---

<a id="c1"></a>

## 1 Version Number Format

```
vA.B.C.D.E
```

| Segment | Name | Meaning | Example Change |
|---------|------|---------|----------------|
| **A** | Major business version | Major change to the business model (core features redesigned, architecture substantially reworked) | v**2**.0.0 |
| **B** | Minor business version | Change at the sub-business or feature-module level (new product features, revised business processes) | v1.**3**.0 |
| **C** | Major code update | Significant change in the code implementation (new APIs, core module refactoring) | v1.3.**2** |
| **D** | Minor fix | Bug fixes or tiny feature updates; **rarely used** | v1.3.2.**1** |
| **E** | Pre-release tag | Alpha, Beta and other pre-release versions; **rarely used** | v1.3.2.1.**beta1** |

> **Notes**:
> - Segments 4 (D) and 5 (E) are rarely used — enable them only when patches or pre-releases need to be distinguished at a fine granularity.
> - While no source code exists yet (for example, the documentation-only stage), a version number may use just the first two segments (such as `v0.1`).
> - The value inside a `VERSION` file carries **no `v` prefix** (`0.1.0`, not `v0.1.0`).

---

<a id="c2"></a>

## 2 Version Milestones

The standard milestones every product component passes through, from documentation to integration:

| Version | Milestone | What Changes at This Milestone |
|---------|-----------|--------------------------------|
| `v0.1` | **Baseline Design Docs** | The component's business, technical and UI/UX documents exist for the first time, together with the page UI designs. No source code yet. |
| `v0.2` | **First Source Code** | Frontend code exists for the first time, running on hardcoded data. No backend, no database. |
| `v0.3.1` | **Technical Design Completed** | The technical design stops being partial: classes, database, backend logic, CRUD APIs and special queries are all specified, and the UI/UX document specifies how every feature calls the API. Still no new code. |
| `v0.3.2` | **Backend Implementation** | Backend and database exist for the first time, implemented in one pass from the completed design. Frontend, backend and database all exist but are **not yet wired together**. |
| `v0.4` | **First Integration** | The three tiers are wired together for the first time, making this the first fully runnable version of the component. |
| `v0.5` | **Pipelines & Daemons** | Long-running and batch processing exists for the first time — design document first, then implementation. |
| `v0.6` | **Platform Integration** | The component stops being standalone: it is mounted into the platform framework, respects the shared login, and its cross-component calls work. |
| `v0.7` – `v0.8` | **Reserve** | No fixed milestone. Used for features added mid-course, requirement changes and refactoring. |
| `v1.0` | **First Official Release** | First official release of the system. |
| `v1.0.x+` | **Continuous Iteration** | Bug fixes, feature polishing and performance tuning on top of the first release. |
| `v1.1.0+` | **Feature Expansion** | New sub-business features, or substantial changes to existing ones. |

> The milestone names in this table are the **same names** used in §3 and in every component-level
> version plan. One version never carries two different names — if you find a document using a
> different label for the same version, that document is the defect.

---

<a id="c3"></a>

## 3 Version Ladder

The standard path a component follows from nothing to official release.
**Every component-level version_plan must partition its own versions according to these semantics.**

| Version | Code Name | Deliverables |
|---------|-----------|--------------|
| `v0.1` | **Baseline Design Docs** | Product / business documentation complete. Backend technical design covers **entity definitions only** — no other backend design yet. Frontend UI/UX document main content complete. Page UI design complete: every page has its own `.html` and `.png` under `ui_page_design/`. |
| `v0.2` | **Frontend Only (Hardcoded Data)** | Pure frontend running on hardcoded data. Create the `deployment/` directory with one-click scripts: build all frontends, start frontend services, kill frontend services. Document these commands in `README.md`. |
| `v0.3.1` | **Technical Design Completed** | Complete the backend technical design: class design, database design, remaining backend logic, standard CRUD API design, special query structure design. Complete the frontend UI/UX document with the per-feature design of how each function calls the backend API. |
| `v0.3.2` | **Backend Implementation** | Implement everything specified in the backend technical design in a single pass: API contract definitions, backend class implementation, backend database access implementation, backend service encapsulation. Database schema init script, data init script, demo data init script. Extend `deployment/` with one-click build / start / kill for all backends, plus one-click build-all / start-all / kill-all. Switch `README.md` to the build-all / start-all / kill-all commands so the user can run everything in one shot. Further extend `deployment/` with an Ubuntu provisioning script — the database is provided at the OS level by default; all product modules are built as Docker images. |
| `v0.4` | **Three-Tier Integration** | Frontend, backend and database integration and testing. |
| `v0.5` | **Pipelines & Daemons** | If the component has data pipelines, compute pipelines or daemons, implement them here. Technical design document first, then development and testing. |
| `v0.6` | **Platform Integration** | End-to-end integration of the component, its pipelines and the platform framework. |
| `v0.7` – `v0.8` | **Reserve** | Typically used for features added mid-course, requirement changes, and code refactoring. |
| `v1.0` | **First Official Release** | First official release of the system. |

**Delivery acceptance criteria per stage** (referenced by Dev Plan and Dev Report):

| Version | Frontend criterion | Backend criterion | Database criterion |
|---------|-------------------|-------------------|--------------------|
| `v0.1` | {All page UI designs done: `.html` + `.png` per page} | {Entity definitions only} | — |
| `v0.2` | {Every page is clickable end to end, data comes from `mock/`} | — | — |
| `v0.3.1` | {UI/UX doc specifies the API call design for every feature} | {Technical design complete: classes, DB, CRUD APIs, special queries} | {Schema design complete} |
| `v0.3.2` | {Unchanged} | {Endpoints implement real business logic} | {Schema init + data init + demo data scripts run successfully} |
| `v0.4` | {Every page uses `services/api.ts`; `mock/` is no longer referenced by production code} | {Unchanged} | {Unchanged} |
| `v0.5` | — | {Pipelines / daemons implemented and tested} | {Unchanged} |
| `v0.6` | {All routes integrated into the platform framework} | {Cross-component calls work} | — |
| `v1.0` | {Official release} | {Official release} | {Official release} |

> **Deviation is allowed**: in practice one side may run ahead (for example the backend has reached `v0.3.2` while the frontend is still at `v0.2`).
> Such a "progress gap" must be recorded explicitly, with a catch-up plan, in `ai_dev_history/02_DevPlanAndReport/v{version}_DevReport.md`.

---

<a id="c4"></a>

## 4 Component-Level Version Control (VERSION file)

This platform uses a **per-component versioning** strategy: every independently releasable unit
(documentation, frontend package, backend service, shared library) carries a `VERSION` file in its
directory containing a single plain-text line with the version number (such as `0.0.0`, **without the
`v` prefix**). Components evolve at their own pace; version numbers are not required to stay in sync.

| Item | Rule |
|------|------|
| File content | A single plain-text line with the version number; no comments, no blank lines |
| Git tag | `{layer}/{component_name}/v{version}`, e.g. `be/{component_code_name}/v0.2.3` |
| CI/CD | Reads `VERSION` to determine the image tag, artifact version and changelog |
| Document version | Every design document carries `**Document Version**: {x.y.z}` in its header and maintains a Change Log at the end |

The full description of this strategy (Git tag naming rules, CI/CD integration and the complete
inventory of `VERSION` files across the project) is documented in:

> 📄 [project_directory_plan.md — §5 Component-Level Version Control](../../project_directory_plan.md#c5)

---

<a id="c5"></a>

## 5 Division of Labour Between This Document and Component Version Plans

| Document | Location | Responsibility | Owner |
|----------|----------|----------------|-------|
| **This document (platform-level)** | `docs/version_plan.md` | Defines the version number **format**, **milestone semantics** and `VERSION` file strategy | Architect; changes require a platform-wide impact assessment |
| **Component-level version plan** | `docs/components/{component_name}/{component_name}_version_plan.md` | Defines, **for that component**, the goal, feature scope, tech stack and out-of-scope items of each v0.x | Component owner; updated as the component iterates |

**Usage conventions**:

| # | Convention |
|---|------------|
| 1 | A component-level version plan **must not redefine** the general meaning of v0.x; it may only state "this component ships these specific features in v0.3" |
| 2 | Every version section of a component-level version plan must contain: **version goal / feature scope / tech stack / out-of-scope items** |
| 3 | "Out-of-scope items" is a **mandatory field** — spell out what this version deliberately does not do, so an AI developer cannot expand the scope on its own |
| 4 | The dev plan (`ai_dev_history/02_DevPlanAndReport/v{version}_DevPlan.md`) takes one version of the component version plan as its **authoritative scope definition** and must not exceed it |
| 5 | The dev report (`ai_dev_history/02_DevPlanAndReport/v{version}_DevReport.md`) uses the component version plan as its baseline document and checks completion item by item |

> Component-level template: `templates/component/_version_plan.md`.

---

## Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {Change description} |
