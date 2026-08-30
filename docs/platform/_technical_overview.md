# Index

<!--
TEMPLATE NOTES (delete this comment block when using)
- Layer: **platform-level technical document**. Place at the root of `docs/`, drop the leading underscore in the filename → `technical_overview.md`.
- Distinction from the component-level `{component}_tech_design.md` (this boundary must be kept):

  | Dimension | This document (platform-level technical_overview.md) | Component-level {component}_tech_design.md |
  |------|-----------------------------------|----------------------------------|
  | Scope | Cross-component architecture, layering, global conventions | Classes, tables, APIs, source directories within a single component |
  | API | Defines only **conventions** (prefix, response body, error codes, pagination) | Defines **concrete endpoints** (paths, parameters, examples) |
  | Database | Defines only **conventions** and **database partitioning** | Defines **concrete table schemas** and indexes |
  | Dependencies | **Version lock table** (the sole authority) | References only, never re-specifies versions |
  | On conflict | **This document takes precedence** | A review must be filed to correct the component document |

- Once a global convention is written into this document, component documents **must not redefine it**, and may only reference the section number (e.g. "follows technical_overview.md §4.2").
- The platform-level document uses `# H1` headings for sections, with the first section being "Index" and section 0 being "Document Description"
  (unlike the component-level document, which uses a `## 1 Table of Contents` style).
- It is recommended to add an anchor `<a id="c4-2"></a>` to every section, to enable precise cross-document referencing.
-->

- [0 Document Description](#0-document-description)
- [1 Technical Architecture](#1-technical-architecture)
  - [1.1 Architecture Overview Diagram](#11-architecture-overview-diagram)
  - [1.2 Architecture Layers Overview](#12-architecture-layers-overview)
  - [1.3 Backend Service Architecture Pattern](#13-backend-service-architecture-pattern)
- [2 Project Directory Plan](#2-project-directory-plan)
- [3 Product Component Technical Description](#3-product-component-technical-description)
- [4 Technical Design Conventions](#4-technical-design-conventions)
  - [4.1 API Design Conventions](#41-api-design-conventions)
  - [4.2 Database Design Conventions](#42-database-design-conventions)
  - [4.3 Frontend Development Conventions](#43-frontend-development-conventions)
  - [4.4 Backend Development Conventions](#44-backend-development-conventions)
  - [4.5 Logging and Observability Conventions](#45-logging-and-observability-conventions)
  - [4.6 Security Conventions](#46-security-conventions)
  - [4.7 Version Management Conventions](#47-version-management-conventions)
- [5 Development Languages and Dependencies](#5-development-languages-and-dependencies)
  - [5.1 Languages and Runtimes](#51-languages-and-runtimes)
  - [5.2 Frontend Major Dependencies](#52-frontend-major-dependencies)
  - [5.3 Backend Major Dependencies — {LanguageA}](#53-backend-major-dependencies--languagea)
  - [5.4 Backend Major Dependencies — {LanguageB}](#54-backend-major-dependencies--languageb)
  - [5.5 Database and Middleware](#55-database-and-middleware)
  - [5.6 Infrastructure and Toolchain](#56-infrastructure-and-toolchain)
- [6 Deployment and Environment Overview](#6-deployment-and-environment-overview)
  - [6.1 Environment Types](#61-environment-types)
  - [6.2 Containerization and Orchestration](#62-containerization-and-orchestration)
  - [6.3 CI/CD Pipeline](#63-cicd-pipeline)
  - [6.4 Unified Deployment Directory (deployment/)](#64-unified-deployment-directory-deployment)
- [7 Appendix](#7-appendix)
  - [7.1 Reference Documents](#71-reference-documents)
  - [7.2 Glossary](#72-glossary)
  - [7.3 Chinese-English Glossary of Common Terms](#73-chinese-english-glossary-of-common-terms)
- [8 Directory and File Naming Rules](#8-directory-and-file-naming-rules)

---

<a id="c0"></a>

# 0 Document Description

| Item | Content |
|------|------|
| **Document Name** | {PlatformName} Technical Overview (technical_overview.md) |
| **Document Layer** | Platform-level — defines cross-component technical architecture and global conventions |
| **Document Version** | {x.y.z} |
| **Intended Readers** | Architects, AI developers, frontend/backend engineers, operations |
| **Downstream Documents** | Each component's `docs/components/{component_code_name}/{component_code_name}_tech_design.md` |
| **Corresponding Business Document** | [business_overview.md](./business_overview.md) |
| **Related Conventions** | [uiux_design_specification.md](./uiux_design_specification.md), [naming_convention.md](./naming_convention.md), [project_directory_plan.md](../project_directory_plan.md) |

> **Convention Precedence**: `technical_overview.md` (this document) > component `tech_design.md`.
> On conflict, this document takes precedence, and a review record must be submitted under `ai_dev_history/01_DocReviewRefine/` to correct the component document.

> **Document Boundary**: This document does not define any concrete table schemas, class members, or API endpoint definitions. Anything that is "specific to a single component"
> belongs in that component's `tech_design.md`; anything that "every component must follow" belongs in §4 of this document.

---

<a id="c1"></a>

# 1 Technical Architecture

<a id="c1-1"></a>

## 1.1 Architecture Overview Diagram

![Technical Architecture Overview](./technical_architecture.png)

> Source file: [technical_architecture.mmd](./technical_architecture.mmd)
> Drawing conventions: see [templates/mermaid/mmd_style_guide.md](../templates/mermaid/mmd_style_guide.md):
> white canvas, light-colored boxes categorized by layer/type, all text in black.

<a id="c1-2"></a>

## 1.2 Architecture Layers Overview

| Layer | Composition | Responsibility | Technology Choice |
|----|------|------|---------|
| Client Layer | {Web browser / mobile} | {Responsibility} | {Choice} |
| Gateway Layer | {API Gateway, authentication and authorization service} | {Responsibility} | {Choice} |
| Portal Layer | {Unified entry point: menu, view framework, dashboard} | {Responsibility} | {Choice} |
| Product Component Layer | {Individual business components, each with its own frontend/backend/database} | {Responsibility} | {Choice} |
| Shared Service Layer | {Workflow orchestrator, AI Agent service, etc.} | {Responsibility} | {Choice} |
| Data Layer | {Relational database, cache, file storage} | {Responsibility} | {Choice} |
| External Systems | {Cloud authentication, external information sources, third-party platforms} | {Responsibility} | {Choice} |

**Inter-layer call constraints** (must be stated explicitly, otherwise AI developers will cross layers arbitrarily):

| # | Rule | Explanation |
|---|------|------|
| 1 | {Component layers must not connect directly to each other's databases} | {Data can only be obtained via the other component's exposed REST API} |
| 2 | {The frontend must not access the database or filesystem directly} | {Everything goes through backend APIs} |
| 3 | {No foreign-key constraints across databases} | {Referential correctness is guaranteed by application logic} |
| 4 | {Whether an API Gateway exists at the current stage} | {If not, the frontend connects directly to each backend port — this must be stated explicitly here} |

<a id="c1-3"></a>

## 1.3 Backend Service Architecture Pattern

The platform adopts a **{component-independent backend service}** architecture pattern: {each product component's backend API service is an independent process,
with its own entry file, dependency management, configuration file, and listening port, and must be started independently}.

```
┌─────────────────────────────────────────────────────┐
│                Frontend ({Framework})                 │
│        {monorepo approach} — {unified entry}  :{3000} │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────┐  │
│   │{ComponentA} FE pkg│ │{ComponentB} FE pkg│ │ ...more│ │
│   └──────┬───────┘  └──────┬───────┘  └────┬─────┘  │
└──────────┼─────────────────┼───────────────┼─────────┘
           ▼                 ▼               ▼
┌──────────────────┐ ┌──────────────────┐ ┌────────────┐
│ {ComponentA} API │ │ {ComponentB} API │ │  ...more   │
│ {Language} — :{8080} │ │ {Language} — :{8081} │ │  services  │
│ Independent deps │ │ Independent deps │ │            │
│ Independent config│ │ Independent config│ │            │
└──────────────────┘ └──────────────────┘ └────────────┘
```

**Implemented backend services**:

| Product Component | Backend Entry | Default Port | Config File |
|---------|---------|---------|---------|
| `{component_code_name}` | `src/backend/{component_code_name}/{entry_path}` | {8080} | `config/config.yaml` |

**Design Notes**:

| # | Point | Explanation |
|---|------|------|
| 1 | Independent process | {Independently compiled, started, and stopped, without affecting each other} |
| 2 | Independent dependencies | {Each service manages its own dependency versions, not shared across a dependency tree} |
| 3 | Independent ports | {The frontend connects to the corresponding port per component} |
| 4 | Independent configuration | {Each service has one config, including port, CORS, etc.} |
| 5 | Frontend architecture difference | {State whether the frontend is merged into a single SPA, versus the backend's multi-process pattern} |

**Layer responsibility boundaries** (`handler` → `service` → `repository` → `model`):

| Layer | Allowed to Do | Must Not Do |
|----|-----------|-----------|
| `handler` | Parameter parsing, input validation, response wrapping, HTTP status codes | Access the database directly, write business logic |
| `service` | Business logic, transaction orchestration, composing across repositories | Directly concatenate SQL, be aware of HTTP/Gin/Request objects |
| `repository` | Data access, SQL/ORM, dynamic table name resolution | Write business rules, open business transactions |
| `model` | Data structure definitions, ORM tags | Any logic |

> Dependency injection approach: {approach}. Cross-layer calls are prohibited (e.g. handler calling repository directly).

---

<a id="c2"></a>

# 2 Project Directory Plan

The platform's complete directory plan (top level, documentation, source code, deployment) is in a separate document at the project root:

> 📄 [project_directory_plan.md](../project_directory_plan.md)

Technical summary:

```
src/
├── frontend/                       # Frontend monorepo, package directories use kebab-case
│   └── packages/{component-code-name}/
├── backend/                        # Backend services, directories use snake_case
│   └── {component_code_name}/
├── api/                            # OpenAPI contracts (the single contract between frontend and backend)
├── gateway/                        # API gateway configuration
├── auth/                           # Authentication and authorization configuration
└── scripts/                        # Build, development, and ops scripts
deployment/                         # Build artifacts and runtime environment
```

---

<a id="c3"></a>

# 3 Product Component Technical Description

| # | Component | Frontend Package | Backend Service | Port | Database | Implementation Language | Status |
|---|------|-------|---------|------|--------|---------|------|
| 1 | {Component1Name} (`{component_code_name}`) | `{component-code-name}` | `{component_code_name}` | {8080} | `{db_name}` | {Language} | {Planned/In Development/Live} |
| 2 | {Component2Name} (`{component_code_name}`) | `{component-code-name}` | `{component_code_name}` | {8081} | `{db_name}` | {Language} | {Status} |

<a id="c3-1"></a>

## 3.1 {Component1Name} ({component_code_name})

| Item | Content |
|------|------|
| **Technical Form** | {Frontend SPA package + REST service + N resident daemons} |
| **Dedicated Database** | `{db_name}` |
| **External API Prefix** | `/api/v1/{resource}` |
| **Called Cross-Module By** | {List "calling component → called endpoint"; fill "None" if none} |
| **Calls Cross-Module To** | {List "this component → other component's endpoint"; fill "None" if none} |
| **Resident Processes** | {Daemon name and responsibility; fill "None" if none} |

> Detailed technical design: see [{component_code_name}_tech_design.md](./components/{component_code_name}/{component_code_name}_tech_design.md).

<a id="c3-2"></a>

## 3.2 {Component2Name} ({component_code_name})

{Same structure as 3.1}

> Repeat section 3.x for each component. **Cross-module call relationships must be registered bidirectionally**:
> the caller records it once under "Calls Cross-Module To", the callee records it once under "Called Cross-Module By", and the two must be consistent.

---

<a id="c4"></a>

# 4 Technical Design Conventions

> This chapter is a **platform-wide mandatory convention**. Component-level `tech_design.md` files may only reference section numbers, not redefine them.

<a id="c4-1"></a>

## 4.1 API Design Conventions

| # | Rule | Explanation |
|---|------|------|
| 1 | Style | {RESTful} |
| 2 | Unified prefix and versioning | All endpoints use the `/api/v{n}/` prefix, with the version number explicitly written in the path |
| 3 | Resource naming | Plural kebab-case, e.g. `/source-media`, `/material-records` |
| 4 | Method semantics | GET for query / POST for create / PUT for full update / PATCH for partial update / DELETE for delete |
| 5 | Batch operations | Expressed as sub-resource verbs, e.g. `POST /{resource}/batch-delete`, `POST /{resource}/{id}/cancel` |
| 6 | Field naming | Request/response JSON fields are always `snake_case`, matching database column names |
| 7 | Pagination | List endpoints uniformly support `page` + `page_size`, with `total` included in the response |
| 8 | Unified response body | See §4.1.1, the single format across the whole platform |
| 9 | Error codes | See §4.1.2; components must not define custom error codes that conflict with the global set |
| 10 | Contract-first | Write `src/api/{component}/openapi.yaml` first, then implement frontend and backend |
| 11 | **Granularity: business objects** | By default, only standard CRUD APIs are provided for business objects; **do not build a dedicated API for a single UI page**. Special cases (complex editors, statistical reports) may add one cautiously. The more custom APIs, the worse the maintainability |
| 12 | Idempotency | Write operations other than POST should be idempotent whenever possible |
| 13 | Cross-module calls | {Rules: who may call whom; direct cross-database connections are prohibited; cross-module endpoints must be registered in both parties' documents} |

### 4.1.1 Unified Response Format

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

**Paginated list response** (fixed structure inside `data`):

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [],
    "total": 0,
    "page": 1,
    "page_size": 50
  }
}
```

| Field | Type | Explanation |
|------|------|------|
| `code` | int | {0 means success; for non-zero see §4.1.2} |
| `message` | string | {fixed as "success" on success; on failure, a user-facing error description that the frontend displays directly} |
| `data` | object / null | {business data; `null` or `{}` when there is no return value} |
| `items` | array | {array of records for the current page} |
| `total` | int | {total number of records matching the query (not the count on the current page)} |
| `page` | int | {current page number, starting from 1} |
| `page_size` | int | {number of records per page, default {50}} |

### 4.1.2 Global Error Codes

| HTTP | code | message example | Meaning | Trigger Scenario |
|------|------|-------------|------|---------|
| 200 | `0` | `success` | Success | — |
| 400 | `{40001}` | `{invalid parameter}` | Parameter validation failed | {Input validation failed} |
| 401 | `{40101}` | `{unauthorized}` | Not authenticated | {Token missing or expired} |
| 403 | `{40301}` | `{forbidden}` | No permission | {Authenticated but lacking permission for the operation} |
| 404 | `{40401}` | `{resource not found}` | Resource not found | {ID does not exist, dynamic table not created} |
| 409 | `{40901}` | `{conflict}` | Conflict | {Unique key duplicate} |
| 500 | `{50001}` | `{internal server error}` | Internal server error | {Uncaught exception} |

> Component-private error codes are allocated starting from `{component error code range}` and must be registered in the component's `tech_design.md`; they must not conflict with this table.

<a id="c4-2"></a>

## 4.2 Database Design Conventions

| # | Rule | Explanation |
|---|------|------|
| 1 | **Isolation principle** | Each product component uses an independent database (or schema). **Direct cross-module connections to another component's database are prohibited**; access must go through that component's API. Cross-database references **do not use foreign-key constraints**; referential correctness is ensured by application logic |
| 2 | Table names | `snake_case`, {singular / plural} form |
| 3 | Column names | `snake_case`, corresponding one-to-one with class member names (see [naming_convention.md](./naming_convention.md) §4) |
| 4 | Primary keys | Default auto-increment `INT`, named `{object_abbreviation}_id`; UUID may be used in distributed scenarios, and must be documented in the component document |
| 5 | Audit fields | Every table must include `created_at`, `updated_at`{, `created_by`} |
| 6 | Timestamp fields | Type uniformly `TIMESTAMPTZ`; `updated_at` is **auto-maintained by a DB trigger**, annotated in the documentation as `auto-update via DB trigger` |
| 7 | **Soft delete** | Uniformly use `deleted_at TIMESTAMPTZ NULL` (**do not use** `is_deleted BOOLEAN`). `NULL` = not deleted |
| 8 | **Active flag** | Uniformly use `is_active BOOLEAN NOT NULL DEFAULT true`. `is_active` and `deleted_at` are orthogonal in meaning and must not substitute for each other |
| 9 | Default field types | String defaults to `TEXT`; integer defaults to `INT`; float defaults to `{FLOAT}`. Exceptions must be documented in the component document |
| 10 | Enum storage | Store enum **literals** (e.g. `NEWS_FEED`) as `TEXT`; never use a native database enum type |
| 11 | **Structured fields** | Use `JSONB`. If a sub-field participates in queries or sorting, an **expression index must be created** (e.g. `(bias_indexes->>'score')::int`), and the "API parameter name → SQL expression" mapping must be documented in the API documentation |
| 12 | **Indexes must be documented** | Every field appearing in `WHERE` / `ORDER BY` / `JOIN` must have an index, and it must be annotated in a dedicated `Index` column in the table definitions of the component `tech_design.md` (`None` / `BTREE` / `UNIQUE` / `GIN`) |
| 13 | Immutable fields | Fields used as a table-sharding name or an external identifier (e.g. `code_name`) must not be modified after creation, and must be annotated bidirectionally in both the table definition and the UPDATE API |
| 14 | Migration management | {Current-stage approach: `CREATE TABLE IF NOT EXISTS` initialization scripts / formal approach: {golang-migrate} + `sql/migrations/`} |
| 15 | Dynamic table sharding | {If a mechanism exists for creating tables dynamically by `code_name`, it must document: when tables are created, the API's behavior when a table does not exist (skip or 404), and the table-name allowlist validation rule} |

### 4.2.1 Database Partitioning

Partitioned by business logic and business-object relationships; **do not put all data in one database**. Basic platform/module configuration **is not stored in the database**; it is stored in configuration files.

| Database Name | Purpose | Owning Module | Who May Read |
|---------|---------|---------|--------|
| `{db_name_1}` | {Purpose} | `{owner_component}` | {Owner writes; other modules read via API} |
| `{db_name_2}` | {Purpose} | `{owner_component}` | {Explanation} |

> Databases for other special needs are defined and documented by each module in its own `tech_design.md`.

<a id="c4-3"></a>

## 4.3 Frontend Development Conventions

| # | Rule | Explanation |
|---|------|------|
| 1 | Language and typing | {TypeScript strict}; {`any` is prohibited, except for extreme third-party compatibility cases} |
| 2 | Framework and version | See the version lock table in §5.2; **no component may upgrade on its own** |
| 3 | Directory naming | Use **kebab-case** under `src/frontend/packages/` |
| 4 | Component structure | {Function components + Hooks, files organized by functional module} |
| 5 | State management | {Approach, e.g. Zustand: one store per business entity + a global `uiStore`}; transient form state uses component-local `useState`, not the global store |
| 6 | API client | Each package uniformly wraps calls in `src/services/api.ts`; **making requests or hardcoding URLs directly in pages is prohibited** |
| 7 | Type definitions | `src/models/`, type names follow the `C`/`E`/`I` prefixes in [naming_convention.md](./naming_convention.md) |
| 8 | Styling and color scheme | Always follow [uiux_design_specification.md](./uiux_design_specification.md); not redefined here |
| 9 | Page design patterns | Always reference the pattern numbers in `uiux_design_specification.md` §2; do not invent new patterns |
| 10 | **API debug page** | Every component must provide an API debug page covering all endpoints of that module plus any external endpoints called cross-module by that module |

<a id="c4-4"></a>

## 4.4 Backend Development Conventions

| # | Rule | Explanation |
|---|------|------|
| 1 | Layering | See the responsibility boundary table in §1.3; **cross-layer calls are prohibited** |
| 2 | Directory naming | Use **snake_case** under `src/backend/` |
| 3 | Input validation | Done at the `handler` layer; concrete rules (regex, length, value ranges) go in the component `tech_design.md` appendix |
| 4 | Transactions | Opened at the `service` layer; `repository` does not commit on its own |
| 5 | Configuration | {Approach, e.g. Viper + environment variable overrides}; sensitive items go only through environment variables |
| 6 | **Dynamic table-name injection protection** | A table/column name assembled from user input **must first be validated against a regex allowlist** (e.g. `^[a-z][a-z0-9_]*$`, length {3–64}) before being concatenated into SQL; return 400 if it fails validation. Parameter values must always use parameterized queries; string concatenation is prohibited |
| 7 | Handling nonexistent tables | Cross-table aggregate queries encountering a dynamic table that has not been created should **skip it without error**; single-table queries return 404. This must be documented endpoint by endpoint in the component's API documentation |
| 8 | Read-only/system fields | System-computed fields (statistics, audit fields) must not appear in the writable field list for POST/PUT, and must be explicitly listed in the API documentation's "excluded writable fields" list |
| 9 | Enums and constants | Backend enum definitions must match the `E`-prefixed enums in `tech_design.md` §2 value for value |
| 10 | Unit testing | {Scope and requirements, e.g.: the service layer must have unit tests; handler/repository are optional} |

<a id="c4-5"></a>

## 4.5 Logging and Observability Conventions

| Level | Usage Scenario | Required Fields |
|------|---------|---------|
| `DEBUG` | {Detailed tracing during development, disabled in production} | {trace_id, key input parameters} |
| `INFO` | {Normal business milestones: service startup, task start/end} | {trace_id, target object id, duration} |
| `WARN` | {Recoverable anomalies: retries, degradation, missing configuration defaults} | {trace_id, reason} |
| `ERROR` | {Unrecoverable failures: request failures, task exceptions} | {trace_id, error stack, context} |

| # | Rule | Explanation |
|---|------|------|
| 1 | Output format | {Structured JSON logs} |
| 2 | Request tracing | {`trace_id` generation and propagation rules; must be propagated across cross-module calls} |
| 3 | Sensitive information | Passwords, tokens, and cookies must always be masked; raw values must never be written to logs |
| 4 | Daemon heartbeat | {How resident processes' liveness is determined and the thresholds used, plus known limitations} |
| 5 | Log retention | {Location, rotation policy, retention period} |

<a id="c4-6"></a>

## 4.6 Security Conventions

| # | Item | Rule |
|---|------|------|
| 1 | Authentication | {Approach, e.g. OAuth + JWT; signing algorithm; validity period} |
| 2 | Authorization | {Approach, e.g. RBAC; location of the permission matrix} |
| 3 | Transport | {HTTPS enforced; CORS allowlist of origins} |
| 4 | Secrets management | {Approach; **hardcoding into the codebase is prohibited**, committing to Git is prohibited} |
| 5 | Injection protection | Parameterized queries + table-name allowlist (see §4.4 item 6) |
| 6 | Privilege-escalation protection | {Location of object-level permission checks} |
| 7 | Auditing | {List of operations that must be logged for audit purposes} |

<a id="c4-7"></a>

## 4.7 Version Management Conventions

The platform adopts **independent per-component versioning** (Per-Component Versioning): each independently releasable unit
(document, frontend package, backend service, shared library) has a `VERSION` file in its directory, containing a single plain-text
version-number line (e.g. `0.0.0`, **without a `v` prefix**). Each component evolves independently at its own pace; version numbers are not required to be synchronized.

| Item | Rule |
|------|------|
| Version number format | `vA.B.C.D.E`, see `docs/version_plan.md` for details |
| VERSION file | Single plain-text line, without a `v` prefix |
| Git Tag | `{layer}/{component_name}/v{version}`, e.g. `be/{component_code_name}/v0.2.3` |
| CI/CD integration | Reads `VERSION` to determine image tags, artifact version numbers, and changelog |
| Document version | Every design document header is annotated with `**Document Version**: {x.y.z}`, and a Change Log is maintained at the end |

> Full conventions: see `docs/version_plan.md` (template: `templates/platform/_version_plan.md`)
> and [project_directory_plan.md](../project_directory_plan.md) §5.
> Each component's own version roadmap is in its `{component_code_name}_version_plan.md`.

---

<a id="c5"></a>

# 5 Development Languages and Dependencies

> **This chapter is the version lock table and the sole authority.** No component may use a language or major dependency not listed here;
> to add or upgrade one, **this chapter must be updated first**, followed by the component documentation and code.
> A component's `tech_design.md` "Major Dependencies" may only be a subset of this chapter and must not specify a different version.

<a id="c5-1"></a>

## 5.1 Languages and Runtimes

| Language / Runtime | Version | Lock Granularity | Purpose |
|--------------|------|---------|------|
| {Go} | {1.25.x} | {minor} | {Backend services and daemons} |
| {TypeScript} | {5.7.3} | {patch} | {Frontend} |
| {Python} | {3.12} | {minor} | {Data migration / AI tasks} |

<a id="c5-2"></a>

## 5.2 Frontend Major Dependencies

| Dependency | Version | Lock Granularity | Purpose |
|------|------|---------|------|
| {React} | {19.0.0} | {patch} | {UI framework} |
| {Vite} | {6.2.0} | {patch} | {Build tool} |
| {React Router} | {7.x} | {major} | {Routing} |
| {UI component library} | {5.x} | {major} | {Component library} |
| {State management} | {5.x} | {major} | {Global state} |
| {HTTP client} | {1.x} | {major} | {API calls} |
| {Package manager} | {9.x} | {major} | {Monorepo workspace} |

> Lock granularity explanation: `patch` = locked to the patch number; `major` = locked to the major version, using the latest patch under that major version.

<a id="c5-3"></a>

## 5.3 Backend Major Dependencies — {LanguageA}

| Dependency | Version | Purpose |
|------|------|------|
| {Web framework} | {1.x} | {HTTP routing and middleware} |
| {ORM} | {2.x} | {Data access} |
| {Configuration management} | {1.x} | {Config loading} |
| {Database driver} | {version} | {Database connectivity} |

<a id="c5-4"></a>

## 5.4 Backend Major Dependencies — {LanguageB}

| Dependency | Version | Purpose |
|------|------|------|
| {Dependency} | {Version} | {Purpose} |

<a id="c5-5"></a>

## 5.5 Database and Middleware

| Component | Version | Purpose |
|------|------|------|
| {PostgreSQL} | {17.x} | {Primary database} |
| {Redis} | {7.x} | {Cache} |
| {Object/file storage} | {version} | {Media files} |

<a id="c5-6"></a>

## 5.6 Infrastructure and Toolchain

| Tool | Version | Purpose |
|------|------|------|
| {Docker} | {version} | {Containerization} |
| {Orchestration approach} | {version} | {Service orchestration} |
| {CI platform} | — | {Pipeline} |
| {Migration tool} | {version} | {Database migration} |

---

<a id="c6"></a>

# 6 Deployment and Environment Overview

<a id="c6-1"></a>

## 6.1 Environment Types

| Environment | Identifier | Purpose | Data Source | Access Method |
|------|------|------|---------|---------|
| Development | `dev` | {Local development and integration} | {Sample data scripts} | {Direct local port} |
| Staging | `staging` | {Integration testing and acceptance} | {Anonymized data} | {Internal domain} |
| Production | `prod` | {Serving external users} | {Real data} | {Public domain + HTTPS} |

<a id="c6-2"></a>

## 6.2 Containerization and Orchestration

| Item | Approach |
|------|------|
| Image build | {One Dockerfile per component; location} |
| Image tag | {Generated by reading the VERSION file} |
| Orchestration | {docker-compose / K8s, manifest location} |
| Service inventory | {1 frontend + N backends + M daemons + database} |

<a id="c6-3"></a>

## 6.3 CI/CD Pipeline

| Stage | Trigger Condition | Action | Artifact |
|------|---------|------|------|
| {lint} | {PR} | {Static checks} | {Report} |
| {build} | {push to main} | {Compile frontend and backend} | {Artifact} |
| {test} | {after build} | {Unit tests} | {Test report} |
| {package} | {tests pass} | {Build image and tag it per VERSION} | {Image} |
| {deploy} | {manual confirmation} | {Release to target environment} | — |

<a id="c6-4"></a>

## 6.4 Unified Deployment Directory (deployment/)

```
deployment/
├── {subdirectory1}/                  # {Explanation}
├── {subdirectory2}/                  # {Explanation}
└── {config file}                     # {Explanation}
```

| Directory/File | Purpose | Committed to Git |
|----------|------|-----------|
| `{path}` | {Purpose} | {Yes/No (No if it contains secrets)} |

---

<a id="c7"></a>

# 7 Appendix

<a id="c7-1"></a>

## 7.1 Reference Documents

| # | Document | Layer | Description |
|---|------|------|------|
| 1 | [business_overview.md](./business_overview.md) | Platform-level | Business and product overview |
| 2 | [uiux_design_specification.md](./uiux_design_specification.md) | Platform-level | UI/UX design conventions |
| 3 | [naming_convention.md](./naming_convention.md) | Platform-level | Naming conventions |
| 4 | [project_directory_plan.md](../project_directory_plan.md) | Platform-level | Project directory plan |
| 5 | [APIs/api_endpoint_list.md](./APIs/api_endpoint_list.md) | Platform-level | Platform-wide API endpoint summary |
| 6 | [common_lib/common_lib_tech_design.md](./common_lib/common_lib_tech_design.md) | Shared library | Cross-module shared types and utilities |

<a id="c7-2"></a>

## 7.2 Glossary

| Term | Definition |
|------|------|
| {Idempotency} | {Executing the same request once versus multiple times has the same effect on system state} |
| {Soft delete} | {Records are not physically deleted; `deleted_at` is set to the time of deletion} |
| {Dynamic table sharding} | {Structurally identical tables created at runtime keyed by a business identifier (e.g. `code_name`)} |
| {Term} | {Definition} |

<a id="c7-3"></a>

## 7.3 Chinese-English Glossary of Common Terms

| Chinese | English | Code Identifier |
|------|------|---------|
| {Source Media} | {source media} | `source_media` |
| {Chinese} | {English} | `{code_name}` |

> Abbreviation rules: see [naming_convention.md](./naming_convention.md) §2. This table is a Chinese-English mapping only; it does not define abbreviations.

---

<a id="c8"></a>

# 8 Directory and File Naming Rules

| Scope | Naming Style | Example |
|------|---------|------|
| All `docs/` directories and files | **snake_case** | `{component_code_name}/`, `{component_code_name}_tech_design.md` |
| All backend code (`src/backend/`) | **snake_case** | `{component_code_name}/`, `source_media_handler.go` |
| All frontend code (`src/frontend/`) | **kebab-case** | `{component-code-name}/`, `source-media-list.tsx` |
| `ui_page_design/` subdirectories | Free format | `P{nn}  {PageName}/` (generated by a third-party tool, not required to be snake_case) |
| `.mmd` / `.png` diagrams | Same prefix as the document they belong to | `{component_code_name}_class_diagram.mmd` |

> **Exception**: generic filenames such as `VERSION`, `README.md`, `.gitkeep` keep their original form and are not subject to the rules above.

Detailed rules: see [naming_convention.md](./naming_convention.md) and
[project_directory_plan.md](../project_directory_plan.md) §1.1.

---

## Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {Description of change} |
