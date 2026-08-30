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
    v0.3_BE_DevPlan.md     -- backend-specific dev plan (this template)
    v0.3_FE_DevPlan.md     -- frontend-specific dev plan
    v0.3_DevReport.md      -- the version's dev report / progress review
    v0.3_Snapshot.md       -- saved context
    v0.3_Handover.md       -- AI-to-AI handover
    v0.3_ImprovePlan.md    -- improvement plan
  When multiple documents of the same type exist within one version, append a date suffix: v0.3_BE_DevPlan_20260320.md
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
  _BE_DevPlan.md (this file) = **backend-specific plan**. Referenced design document
      index (this component / platform conventions / shared library / previous-version
      code -- four categories), tech stack and architecture constraints, target
      directory structure, database script plan, development tasks by Phase,
      API endpoint routing table, development notes, completion status review.
  _FE_DevPlan.md = **frontend-specific plan**. Background and motivation, referenced
      document index, technical approach (architecture changes / new directories /
      API integration endpoint list), Phase tasks, final routing registration state,
      file change summary, verification method.

[Key Design Intent]
  §0 exists to solve the "AI cold-context start" problem -- an AI in a new session
  doesn't know which documents to read, so §0 uses a table to precisely specify
  file paths + required sections, avoiding missed reads or the wrong version being read.
  §6 captures the pitfalls hit in the previous version, to prevent repeating mistakes.
  §7 is filled in after development completes, so this plan also serves as a
  completion-status record.

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

# {ComponentDisplayName} Backend Dev Plan -- v{TargetVersion} {Version Codename}

**Document**: v{TargetVersion}_BE_DevPlan.md
**Date**: {YYYY-MM-DD}
**Component**: {component}
**Target**: v{TargetVersion} -- {one-sentence statement of what this version is meant to achieve}
**Developer**: AI ({model name})
**Prerequisite**: {prerequisite conditions, e.g.: v0.2 backend API + hardcoded mock data completed}
**Status**: {🟡 In Progress / 🟢 Fully Completed / ⚪ Not Started}

---

## 0 Referenced Design Documents Index

> **⚠️ Must Read Before Development**: the following files contain the complete design specifications needed for this component's backend development. AI developers must read each of these files before coding, to obtain full context.

### 0.1 This Component's Design Documents

| # | File Path | Required Sections | Notes |
|---|---------|---------|------|
| 1 | `docs/components/{component}/{component}_tech_design.md` | **Entire document** | Core technical design document: class definitions (§2), program description (§3), database definitions (§4), API definitions (§5, {n} endpoints total), source code directory layout (§{n}) |
| 2 | `docs/components/{component}/{component}_version_plan.md` | §{n} (v{TargetVersion} definition) | v{TargetVersion} feature scope and tech stack |
| 3 | `docs/components/{component}/{component}_business_desc.md` | §{n} (business objects), §{n} (business processes) | Business semantics, for understanding field meanings and process constraints |
| 4 | `docs/components/{component}/{component}_db_schema.mmd` | Entire document | Database ER diagram, reference for table creation |
| 5 | `docs/components/{component}/{component}_class_diagram.mmd` | Entire document | Class relationships and inheritance, reference for Model definitions |

### 0.2 Platform-Level Convention Documents

| # | File Path | Required Sections | Notes |
|---|---------|---------|------|
| {n} | `docs/naming_convention.md` | §2 (abbreviation table), §3 (type prefixes), §4 (variable naming) | Platform-wide naming convention: C-prefixed classes, E-prefixed enums, snake_case column names |
| {n} | `docs/technical_overview.md` | §{n} (database partitioning), §{n} (cross-module API call rules), §{n} (API design conventions) | Platform-wide technical design |
| {n} | `project_directory_plan.md` | §{n} (backend directory), §{n} (API definitions) | Backend code directory structure and OpenAPI contract paths |

### 0.3 Shared-Library Reference Documents

| # | File Path | Required Sections | Notes |
|---|---------|---------|------|
| {n} | `docs/common_lib/common_lib_tech_design.md` | §{n} ({shared type 1}), §{n} ({shared type 2}) | Cross-module shared type definitions in the shared library |
| {n} | `docs/common_lib/common_backend.md` | **Entire document** | Backend shared code conventions: unified response, middleware, database utilities, logging utilities |

### 0.4 Previous-Version Code Reference (v{PreviousVersion}, the basis for this rework)

| # | File Path | Reference Content |
|---|---------|---------|
| {n} | `src/backend/{component}/` | Complete v{PreviousVersion} code: {cmd/, config/, internal/(handler+service+model+enum+repository), pkg/, sql/} |
| {n} | `src/backend/{component}/{path}` | {The rework points for this file in this version} |
| {n} | `docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{PreviousVersion}_BE_DevPlan.md` | Previous version's dev plan (to understand the existing architecture and sample-data cross-reference keys) |

---

## 1 Tech Stack and Architecture Constraints

| Item | Value |
|------|-----|
| Backend language | {Go 1.25.x (go.mod uses 1.22 compatibility)} |
| Web framework | {Gin 1.x (already present in v{PreviousVersion})} |
| Configuration management | {Viper 1.x (already present in v{PreviousVersion})} |
| CORS | {gin-contrib/cors (already present in v{PreviousVersion})} |
| **Database** | **{PostgreSQL 17.x (new in v{TargetVersion})}** |
| **ORM** | **{GORM 2.x + gorm/driver/postgres (new in v{TargetVersion})}** |
| Migration tool | {golang-migrate (new in v{TargetVersion})} |
| Logging | {Approach} |
| Listening port | {8081} |
| API prefix | `/api/v1/` |

> Tech stack items new in this version are marked in **bold**, for quick identification of the rework scope.
> Versions must match the locked versions in `docs/technical_overview.md` §{n}; do not upgrade on your own.

### 1.1 v{TargetVersion} Architecture Change Summary

| Layer | v{PreviousVersion} State | v{TargetVersion} Target |
|----|-----------------|-----------------|
| handler | {Current state} | {Target} |
| service | {Stub implementation, returns mock data} | {Inject Repository + real business logic} |
| repository | {Interface definitions only} | {GORM implementation} |
| model | {Plain structs} | {Add GORM tags} |
| Data source | {internal/mock/ hardcoded} | {PostgreSQL} |

### 1.2 What v{TargetVersion} Explicitly Does Not Include

> Explicitly state **what is not being done**, to prevent the AI from implementing beyond scope and blurring the version boundary.

| # | Excluded Item | Planned Version |
|---|---------|---------|
| 1 | {daemon collection process} | v{n} |
| 2 | {user authentication and permissions} | v{n} |
| 3 | {data migration tooling} | v{n} |
| 4 | {caching layer} | {not yet planned} |

---

## 2 Target Directory Structure

The target shape of `src/backend/{component}/` after v{TargetVersion} is complete (**bold** marks items new/reworked in this version):

```
src/backend/{component}/
├── cmd/
│   └── {service_name}/
│       └── main.go                 # Service entry point
├── config/
│   ├── config.go                   # Config struct
│   └── config.yaml                 # Default config
├── internal/
│   ├── handler/
│   │   ├── router.go               # Route registration
│   │   └── {resource}_handler.go   # Per-resource handler
│   ├── service/
│   │   └── {resource}_service.go   # Business logic
│   ├── repository/
│   │   ├── {resource}_repo.go      # Interface definition
│   │   └── {resource}_repo_gorm.go # GORM implementation
│   ├── model/
│   │   └── {resource}.go           # Data model
│   ├── enum/
│   │   └── {enum_name}.go          # Enum
│   └── db/
│       └── db.go                   # Database connection management
├── pkg/
│   ├── response.go                 # Unified response wrapper
│   └── {util}.go
├── sql/
│   ├── init_databases.sql          # DB creation script
│   ├── migrations/                 # Migration scripts
│   └── sample_data.sql             # Sample data
├── go.mod
└── go.sum
```

{Additional notes on the responsibilities of key directories.}

---

## 3 Database Script Plan

### 3.1 init_databases.sql -- DB Creation Script

| Item | Content |
|------|------|
| Path | `src/backend/{component}/sql/init_databases.sql` |
| Responsibility | {Create database, schema, extensions, dedicated role} |
| Idempotency | {Use IF NOT EXISTS, safely re-runnable} |
| Execution | `{psql -U postgres -f init_databases.sql}` |

### 3.2 migrations/ -- {Migration Tool} Migration Scripts

| Seq | File Name | Content |
|------|--------|------|
| {000001} | `{000001_create_{table}.up.sql}` / `.down.sql` | {Create {table} table + indexes} |
| {000002} | `{000002_xxx.up.sql}` / `.down.sql` | {Description} |

| Convention | Rule |
|------|------|
| Naming | `{seq}_{action}_{object}.{up|down}.sql` |
| Reversible | Every up must have a matching down |
| Immutable | Migration scripts that have already been run must not be modified, only added to |

### 3.3 sample_data.sql -- Sample Data Script

| Item | Content |
|------|------|
| Path | `src/backend/{component}/sql/sample_data.sql` |
| Record count | {{n} rows per main table, covering each enum value and boundary case} |
| Purpose | {Local development, API integration testing, frontend integration} |
| Idempotency | {TRUNCATE first then insert / use fixed primary keys + ON CONFLICT DO NOTHING} |

### 3.4 Sample Data Cross-Reference Key Table

> Frontend-backend integration testing needs fixed IDs to align; this table is the contract between the frontend mock and the backend sample data.

| {Main Object} | {Primary Key Value} | {code_name} | Related {Sub-object} | Notes |
|---------|---------|------------|---------------|------|
| {Object 1} | {1} | `{code_name_1}` | {Sub-object list} | {Covers {enum value} scenario} |
| {Object 2} | {2} | `{code_name_2}` | {Sub-object list} | {Covers {boundary} scenario} |

---

## 4 Development Task List

> Tasks within a Phase can run in parallel; Phases must run strictly in series.
> The "Status" column is filled in during development: 🟢 Done / 🟡 Partial / 🔴 Not Implemented.

### Phase 1: {Phase name, e.g. Dependency Update}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 1.1 | {Task description} | `{file path}` | {Implementation points, notes} | |
| 1.2 | {Task description} | `{file path}` | {Points} | |

### Phase 2: {Phase name, e.g. Config Extension}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 2.1 | {Task description} | `{file path}` | {Points} | |
| 2.2 | {Task description} | `{file path}` | {Points} | |

### Phase 3: {Phase name, e.g. Database Connection Management}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 3.1 | {Task description} | `{file path}` | {Points} | |

### Phase 4: {Phase name, e.g. Model and ORM Tags}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 4.1 | {Task description} | `{file path}` | {Field-level points: JSONB serialization, timestamp fields, soft delete} | |

### Phase 5: {Phase name, e.g. Database Scripts}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 5.1 | {Task description} | `{file path}` | {Points} | |

### Phase 6: {Phase name, e.g. Repository Implementation}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 6.1 | {Task description} | `{file path}` | {Query construction, pagination, sort-field whitelist, dynamic table name validation} | |

### Phase 7: {Phase name, e.g. Service Layer Refactor}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 7.1 | {Task description} | `{file path}` | {Business rules, transaction boundaries, cascade handling} | |

### Phase 8: {Phase name, e.g. Handler Layer Input Validation}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 8.1 | {Task description} | `{file path}` | {Source of validation rules: tech_design appendix "Input Validation Rules"} | |

### Phase 9: {Phase name, e.g. Entry Point Update}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 9.1 | {Task description} | `{file path}` | {Dependency wiring order, graceful shutdown} | |

### Phase 10: {Phase name, e.g. Unit Tests}

| # | Task | Output File | Notes | Status |
|---|------|---------|------|------|
| 10.1 | {Task description} | `{file path}` | {Coverage layers, mocking approach} | |

---

## 5 API Endpoint Routing Table

> This table is the acceptance checklist for handler route registration; endpoints must exactly match `{component}_tech_design.md` §5.

| # | Method | Endpoint | Handler | Service | Auth | tech_design Section | Status |
|---|--------|----------|---------|---------|------|-----------------|------|
| 1 | GET | `/api/v1/{resource}` | `{Handler}.List` | `{Service}.List` | {Yes/No} | §5.1.1 | |
| 2 | GET | `/api/v1/{resource}/:{id}` | `{Handler}.Get` | `{Service}.Get` | {Yes/No} | §5.1.2 | |
| 3 | POST | `/api/v1/{resource}` | `{Handler}.Create` | `{Service}.Create` | {Yes/No} | §5.1.3 | |
| 4 | PUT | `/api/v1/{resource}/:{id}` | `{Handler}.Update` | `{Service}.Update` | {Yes/No} | §5.1.4 | |
| 5 | DELETE | `/api/v1/{resource}/:{id}` | `{Handler}.Delete` | `{Service}.Delete` | {Yes/No} | §5.1.5 | |
| 6 | POST | `/api/v1/{resource}/batch-delete` | `{Handler}.BatchDelete` | `{Service}.BatchDelete` | {Yes/No} | §5.2.4 | |

**Total endpoints**: {n}.

---

## 6 Development Notes

### 6.1 v{TargetVersion} Version Boundary

{Reiterate what this version does and does not do. Reference §1.2. Whenever the design documents mark a feature as belonging to a later version, skip it and leave a TODO comment in the code stating the target version.}

### 6.2 Dynamic Table Safety

{If this component uses table names concatenated from user input (e.g. `{code_name}_mrecord`):}

- The table-name parameter **must** be validated against a whitelist regex before entering SQL: `^[a-z][a-z0-9_]*$`, length {3-64}.
- On validation failure, return {400} directly; it must not reach the repository layer.
- String concatenation must not be used to construct WHERE clause values; always parameterize.

### 6.3 Handling a Table That Does Not Exist

{Dynamic tables are only created {at what point}. The convention for API access to a table that has not yet been created:}

| Scenario | Handling |
|------|---------|
| {Single-table query, table does not exist} | {Return 404 with a clear message} |
| {Cross-table aggregation, some table does not exist} | {Skip that table without erroring, only aggregate over tables that exist} |

### 6.4 {ORM} Usage Notes

| # | Note |
|---|---------|
| 1 | {How JSONB fields are serialized and the custom type implementation} |
| 2 | {Soft delete: use `deleted_at` rather than the ORM's default soft-delete semantics, must be configured explicitly} |
| 3 | {`updated_at` is maintained by a database trigger; the ORM side must not write it automatically} |
| 4 | {Dynamic table names are specified via `Table()`, not via AutoMigrate} |
| 5 | {Sort fields must go through a whitelist mapping; user-supplied sort_by must never be concatenated directly into ORDER BY} |
| 6 | {Sorting on a JSONB sub-field requires an expression: `(jsonb_col->>'field')::int`, and confirm a matching expression index already exists} |

### 6.5 Cross-Database Queries

{Platform convention: each component has its own independent database, **cross-database JOINs are prohibited**. When data from another module is needed, call it through a REST API across modules.
 This component's cross-module call list:}

| Direction | Counterpart Component | Endpoint | Purpose |
|---------|---------|------|------|
| {This component calls another} | {component_x} | `{GET /api/v1/xxx}` | {Purpose} |
| {Another calls this component} | {component_y} | `{GET /api/v1/yyy}` | {Purpose} |

### 6.6 Response Format Convention

Consistent with v{PreviousVersion}, all endpoints return a unified structure:

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

`data` for paginated endpoints:

```json
{
  "items": [],
  "total": 0,
  "page": 1,
  "page_size": 50
}
```

### 6.7 Logging Strategy

| Level | Usage Scenario | Required Fields |
|------|---------|---------|
| DEBUG | {SQL statements, request bodies} | {trace_id} |
| INFO | {request in/out, key business actions} | {trace_id, method, path, status, latency} |
| WARN | {recoverable exceptions, degradation} | {trace_id, reason} |
| ERROR | {unhandled exceptions, dependency failures} | {trace_id, error, stack} |

> Sensitive fields such as passwords and tokens must always be masked before being logged.

### 6.8 Naming Convention Quick Reference

| Object | Rule | Example |
|------|------|------|
| Class / struct | `C` prefix + PascalCase | `{CSourceMedia}` |
| Enum | `E` prefix + PascalCase | `{EExeStatus}` |
| Interface | `I` prefix + PascalCase | `{IRepository}` |
| Database table | snake_case | `{source_media}` |
| Database column | snake_case | `{created_at}` |
| API path | plural kebab-case | `{/api/v1/source-media}` |
| Backend directory | snake_case | `{material_collector}` |
| Config property | `cfg` prefix | `{cfgDbHost}` |

> Full rules are in `docs/naming_convention.md`.

---

## 7 Completion Status Review ({YYYY-MM-DD})

> This section is filled in after development is complete, so this plan also serves as a completion-status record.
> The full version-level progress review is issued separately as `v{TargetVersion}_DevReport.md` (same directory, 02_DevPlanAndReport);
> this section only records the completion status of each Phase's tasks within this plan.

### 7.1 Overview

| Phase | Task Count | 🟢 Done | 🟡 Partial | 🔴 Not Implemented | Notes |
|-------|-------|--------|--------|----------|------|
| Phase 1 {Phase name} | {n} | {n} | {n} | {n} | |
| Phase 2 {Phase name} | {n} | {n} | {n} | {n} | |
| Phase 3 {Phase name} | {n} | {n} | {n} | {n} | |
| **Total** | **{N}** | **{N}** | **{N}** | **{N}** | |

### 7.2 🟡 Partial Item Details

| # | Task | Completed Part | Remaining Part | Reason | Follow-up Plan |
|---|------|-----------|-----------|------|---------|
| {n.n} | {Task} | {Done} | {Not done} | {Reason} | {Complete in v{n} / move to open items} |

### 7.3 Deviations From the Design Documents

| # | Design Document Requirement | Actual Implementation | Deviation Reason | Needs Doc Update? |
|---|-------------|---------|---------|---------------|
| 1 | {tech_design §x.y requirement} | {Actual approach} | {Reason} | {Yes/No} |
