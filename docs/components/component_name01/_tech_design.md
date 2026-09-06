<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level]
This file is a **Component-Level Technical Design** template.

- Location: `docs/components/{component_code_name}/`
- File naming: replace the leading `_` with the component code name.
  For example, component `crawler` → `crawler_tech_design.md`

[Boundary Between This and the Platform-Level Document]
`technical_overview.md` at the root of `docs/` is the **platform-level** technical document; the two are at completely different levels:

| Dimension | Platform-Level `docs/technical_overview.md` | Component-Level `{component}_tech_design.md` (this template) |
|------|-------------------------------------|-------------------------------------------|
| Describes | The technical architecture of the entire platform | The technical implementation of a single product component |
| Content | Architecture layering, backend service architecture patterns, cross-module API call rules, database partitioning, global technical standards (API/DB/frontend/backend/logging/security/versioning) | This component's class definitions, database tables, API endpoints, source code directory |
| Tech stack | The **authoritative list** of platform-wide unified language versions, dependency versions, and middleware versions | Only declares which of these this component uses; versions must match the platform level |
| Standards | Defines the standards themselves | References the standard's section number and explains how this component complies |

**Hard Rules**:
1. Component-level documents **must not redefine** technical standards that already exist at the platform level (unified response format, error code system,
   naming conventions, logging conventions, layering rules, etc.). Where needed, reference them as
   `Follows [technical_overview.md](../../technical_overview.md) §4.x`.
2. Tech stack versions **must not conflict with** `technical_overview.md §5`. If this component needs a new language or
   major dependency, the platform-level document must be updated first, then referenced here.
3. Every class in §2 of this document must be traceable to a corresponding business object in
   `{component}_business_desc.md §3 Business Objects`; the property sets on both sides must match.
4. Every endpoint defined in §5 of this document must exactly match the endpoint paths referenced in
   `{component}_uiux.md §2.3 Page-Object-API Mapping`, and be reflected in sync in `{component}_api.mmd`.

[Document Conventions]
- Section 1 must be the Table of Contents.
- All sections use numeric numbering (1, 1.1, 1.1.1).
- Content inside `{curly braces}` is a placeholder; replace with actual content when using.
- Optional sections are marked in the body with keep/delete conditions; delete the entire section if not needed and renumber.
-->

# {ComponentName} ({component_code_name}) Technical Design

**Document Version**: {x.y.z}

---

## 1 Table of Contents

### 1.0 Document Structure Index (main document + volumes)

<!-- Keep this table only when the design has been split into volumes (project_directory_plan 3.1 rule 7). Chapter numbers are globally unique across the main document and its volumes; a moved chapter leaves a one-line stub in the main document pointing at its volume. -->

| File | Chapters | Content | Who reads it |
|---|---|---|---|
| `{component_code_name}_tech_design.md` (this file) | 1, 2, ... + stubs of moved chapters | Core entities / value types / enums, index | Every task starts here (1.0 + the chapter-2 classes it needs) |
| `{component_code_name}_tech_design_{volume}.md` | {n} | {what the volume holds} | {which tasks need it} |
| `{component_code_name}_tech_design_engine_facts.md` | engine facts register | Third-party engine behavioural facts `EF-nn` (source location, chosen settings, dependent clauses, pinning tests) | Any task touching engine semantics - **cite EF ids, never read the engine source** |
| `{component_code_name}_tech_design_changelog_archive.md` | change-log rows older than v{A}.{B}.0 | Archived history | Tracing only |

**Reference convention**: references always read "`{component_code_name}_tech_design section x.y`" and are resolved through this table; a volume carries its own version number and change log, and revising a volume does not bump the main document.

### 1.1 Chapter Index

- [1 Table of Contents](#1-table-of-contents)
- [2 Class Definitions](#2-class-definitions)
  - [2.1 Class Diagram](#21-class-diagram)
  - [2.2 {CClassName1}](#22-cclassname1)
  - [2.3 {CClassName2}](#23-cclassname2)
  - [2.4 {CBaseClass} (Reference)](#24-cbaseclass-reference)
  - [2.5 Enum Definitions](#25-enum-definitions)
- [3 Software Program Descriptions](#3-software-program-descriptions)
  - [3.1 {ProgramName1}](#31-programname1)
  - [3.2 {ProgramName2}](#32-programname2)
- [4 Database Definitions](#4-database-definitions)
  - [4.0 Database Schema Diagram](#40-database-schema-diagram)
  - [4.1 {table_name_1}](#41-table_name_1)
  - [4.2 {code_name}_{suffix} (Dynamic Sharded Table)](#42-code_name_suffix-dynamic-sharded-table)
- [5 API Definitions](#5-api-definitions)
  - [5.0 API Overview Diagram](#50-api-overview-diagram)
  - [5.1 {Resource1} CRUD](#51-resource1-crud)
  - [5.2 {Resource2} Query](#52-resource2-query)
  - [5.3 Common Pagination Response Format](#53-common-pagination-response-format)
  - [5.4 Common Error Code Definitions](#54-common-error-code-definitions)
  - [5.5 Request/Response JSON Schema Examples](#55-requestresponse-json-schema-examples)
- [6 Development Technology](#6-development-technology)
  - [6.1 Operating System](#61-operating-system)
  - [6.2 Software Form](#62-software-form)
  - [6.3 Programming Language](#63-programming-language)
  - [6.4 Major Dependencies](#64-major-dependencies)
  - [6.5 Release Form](#65-release-form)
- [7 Environment Configuration](#7-environment-configuration)
  - [7.1 Environment Variable List](#71-environment-variable-list)
  - [7.2 Configuration File Format](#72-configuration-file-format)
- [8 Build & Run](#8-build--run)
- [9 Server Parameters](#9-server-parameters)
- [10 Source Code Directory Layout](#10-source-code-directory-layout)
- [11 Appendix](#11-appendix)
  - [11.1 Glossary](#111-glossary)
  - [11.2 code_name Naming Rules](#112-code_name-naming-rules)
  - [11.3 Input Validation Rules](#113-input-validation-rules)
  - [11.4 Logging Conventions](#114-logging-conventions)
  - [11.5 Test Strategy](#115-test-strategy)
- [Change Log](#change-log)

---

## 2 Class Definitions

> Type naming follows [naming_convention.md](../../naming_convention.md):
> classes use the `C` prefix, enums use the `E` prefix, interfaces use the `I` prefix, config properties use the `cfg` prefix.

### 2.1 Class Diagram

![Class Diagram](./{component_code_name}_class_diagram.png)

> Source file: [{component_code_name}_class_diagram.mmd](./{component_code_name}_class_diagram.mmd)
>
> Drawing conventions: see [templates/mermaid/](../../../templates/mermaid/):
> white canvas, class boxes colored by category (classes in this module / referenced classes / enums / abstract base classes), all text in black.
> Classes referenced from other modules use a dashed border and are annotated on the diagram with `<<ref: {module}>>`.

**Class ownership in this diagram**:

| Class | Owning Module | Diagram Representation |
|----|---------|---------|
| {CClassName1} | This module | Solid border |
| {CClassName2} | This module | Solid border |
| {CBaseClass} | `{other_module}` | Dashed border, `<<abstract - ref: {other_module}>>` |
| {EEnumName1} | This module | Solid border, `<<enumeration>>` |
| {EEnumName2} | `{other_module}` | Dashed border, `<<enumeration - ref: {other_module}>>` |

### 2.2 {CClassName1}

**Maps to Business Object**: [{component_code_name}_business_desc.md](./{component_code_name}_business_desc.md) §3.1 {ObjectName1} ({ObjectDisplayName1})

{1–3 sentences describing this class's responsibility, lifecycle, and which services use it.}

| Member | Type | UI/DB Display or Storage Requirements | Description |
|--------|------|---------------------------------------|-------------|
| {member_id} | {int} | {PK, auto-increment; right-aligned on the list page, no `#` prefix} | {business meaning} |
| {display_name} | {string} | {NOT NULL UNIQUE; column 1 on the list page, 14px bold} | {business meaning} |
| {code_name} | {string} | {NOT NULL UNIQUE; immutable after creation, the basis for naming all sharded tables} | {business meaning; naming rules in §11.2} |
| {some_enum} | {EEnumName1} | {NOT NULL; displayed as a Tag badge} | {business meaning; enum defined in §2.5.1} |
| {some_json} | {CSubStruct} | {NOT NULL, JSONB; sub-fields require an expression index} | {business meaning} |
| {is_active} | {bool} | {NOT NULL DEFAULT true; list row uses the inactive style when false} | {business meaning} |
| {deleted_at} | {datetime?} | {NULL means not deleted; list row uses the soft-deleted style when non-NULL} | {soft delete timestamp} |
| {created_at} | {datetime} | {NOT NULL DEFAULT NOW(); read-only} | {creation time} |
| {updated_at} | {datetime} | {NOT NULL DEFAULT NOW(); auto-update via DB trigger; read-only} | {last update time} |

**Field-Level Constraints**:

| # | Field | Constraint | Description |
|---|------|------|------|
| 1 | {code_name} | Immutable after creation | {Reason: used as the sharded table name prefix; changing it would cause data loss} |
| 2 | {stat_field} | System-computed value, not writable via API | {refreshed periodically by {daemon_name}} |

### 2.3 {CClassName2}

**Maps to Business Object**: [{component_code_name}_business_desc.md](./{component_code_name}_business_desc.md) §3.2 {ObjectName2} ({ObjectDisplayName2})

{CClassName2} inherits from {CBaseClass} (see §2.4). The table below **lists only the members newly added by this class**;
inherited members are covered in §2.4.

| Member | Type | UI/DB Display or Storage Requirements | Description |
|--------|------|---------------------------------------|-------------|
| {member1} | {bool} | {NOT NULL DEFAULT false} | {business meaning} |
| {member2} | {string?} | {NULL; required only when {member1} = true; password-type fields must be masked in the UI} | {business meaning} |

### 2.4 {CBaseClass} (Reference)

> **How to write a Reference class**: the **authoritative definition of this class is not in this module**. It is duplicated here only to fully show the inheritance relationship;
> any change must be made in the authoritative module first, then synced here.

| Item | Content |
|------|------|
| **Authoritative Definition Module** | `{other_module}` |
| **Authoritative Definition File** | [{other_module}_tech_design.md](../{other_module}/{other_module}_tech_design.md) §2.{n} |
| **Authoritative Class Diagram** | [{other_module}_class_diagram.mmd](../{other_module}/{other_module}_class_diagram.mmd) |
| **How This Module Uses It** | {e.g.: {CClassName2} inherits from this class; this module's {table_name} table includes all of its columns} |

| Member | Type | UI/DB Display or Storage Requirements | Description |
|--------|------|---------------------------------------|-------------|
| {base_member1} | {int} | {PK, auto-increment} | {business meaning} |
| {base_member2} | {string} | {NOT NULL UNIQUE} | {business meaning} |

> **Sync Check Item**: every review must verify that the fields in this section are consistent with the authoritative module's definition.
> Historically, "the base class field was changed in one module but not the other" is the most frequent cross-module documentation defect.

### 2.5 Enum Definitions

> The **business explanation** of enums is in [{component_code_name}_business_desc.md](./{component_code_name}_business_desc.md) §6.2.
> This section is the **authoritative technical definition**: the value literals, storage type, and default value.

#### 2.5.1 {EEnumName1}

| Item | Content |
|------|------|
| **Authoritative Definition** | This module |
| **Storage Type** | {TEXT} (stores the enum literal; the database's native enum type is not used) |
| **Default Value** | `{DEFAULT_VALUE}` |
| **Used In** | {§2.2 {CClassName1}.{member}; §4.1 {table_name}.{column}} |

| Value | Meaning | Description |
|-------|------|------|
| `{VALUE_1}` | {meaning} | {when this value applies; whether it is a terminal state} |
| `{VALUE_2}` | {meaning} | {description} |
| `{VALUE_3}` | {meaning} | {description} |

#### 2.5.2 {EEnumName2}

| Item | Content |
|------|------|
| **Authoritative Definition** | {This module / `{other_module}`, see [{other_module}_tech_design.md](../{other_module}/{other_module}_tech_design.md) §2.{n}} |
| **Storage Type** | {TEXT} |
| **Default Value** | `{DEFAULT_VALUE}` |
| **Used In** | {§2.3 {CClassName2}.{member}} |

| Value | Meaning | Description |
|-------|------|------|
| `{VALUE_1}` | {meaning} | {description} |

---

## 3 Software Program Descriptions

> **OPTIONAL SECTION**: keep this section only if this component includes resident daemon processes, CLI tools, or multiple executable programs.
> Components that are a pure REST service + frontend package should delete this entire section and renumber the following sections.

This component includes the following executable programs:

| # | Program | Form | Applicable Input | Trigger Mode | Detailed Design |
|---|------|------|---------|---------|---------|
| 1 | {ProgramName1} | {daemon} | {applicable data type/config condition} | {scheduled polling / event-driven} | [sub_{program_1}/{program_1}_design.md](./sub_{program_1}/{program_1}_design.md) |
| 2 | {ProgramName2} | {daemon} | {applicable condition} | {trigger mode} | [sub_{program_2}/{program_2}_design.md](./sub_{program_2}/{program_2}_design.md) |

> **Design-Splitting Convention**: the full design for each daemon/CLI program (detailed main loop, state machine, error handling,
> configuration items) is split into a standalone design document under the `sub_{program_name}/` subdirectory. This section keeps only a **summary**,
> letting the reader quickly understand which programs make up this component and where each one's boundaries lie.
>
> **Constraint Note**: regarding execution timing, concurrency conflicts, and read/write ordering of shared data between multiple daemons —
> if these are harmless from a business standpoint, this section's introduction should explicitly state "timing conflicts between daemons are not a concern",
> to avoid reviews repeatedly getting bogged down in race-condition discussions that have no real impact.

### 3.1 {ProgramName1}

| Item | Detail |
|------|--------|
| **Responsibility** | {one-sentence description} |
| **Applicable Input** | {load condition, e.g.: records in `{table}` where `is_active = true and deleted_at IS NULL`} |
| **Trigger Mode** | {e.g.: polls at the minimum trigger interval; reloads the config list every round} |
| **Concurrency Model** | {single process/single thread / main process + one child process per input / coroutine pool} |
| **Output** | {which tables it writes to; which logs it produces} |

**Main Loop Steps**:

| # | Step | Description |
|---|------|------|
| 1 | {load config} | {where it loads from; how often it reloads; how it detects additions/deactivations/deletions} |
| 2 | {evaluate trigger} | {trigger condition; skip condition} |
| 3 | {execute task} | {what it does; which records it writes} |
| 4 | {cleanup} | {what is cleaned up; how leftover tasks for deactivated/deleted inputs are handled} |

**Failure Retry Rules**:

| Failure Type | Retry Strategy | Limit | Behavior After Limit Exceeded |
|---------|---------|------|-----------|
| {network timeout} | {immediate in-process retry} | {n times} | {mark the task as {status}, write an error log} |
| {parse failure} | {no retry} | — | {mark the task as {status}} |

**Known Limitations**:

| # | Limitation | Impact | Notes |
|---|------|------|------|
| 1 | {limitation} | {impact} | {whether it is planned to be resolved in a later version} |

### 3.2 {ProgramName2}

{Same structure as above.}

---

## 4 Database Definitions

> Database partitioning, naming conventions, soft delete conventions, and indexing requirements follow
> [technical_overview.md](../../technical_overview.md) §4.2.
>
> This component has its own independent database: `{db_name}`. **Cross-database JOINs are prohibited**; cross-module data must always be obtained via REST API.

### 4.0 Database Schema Diagram

![Database Schema](./{component_code_name}_db_schema.png)

> Source file: [{component_code_name}_db_schema.mmd](./{component_code_name}_db_schema.mmd)
>
> Drawing conventions: see [templates/mermaid/](../../../templates/mermaid/):
> white canvas, table boxes colored by purpose (config table / business data table / dynamic sharded table template / reference table), all text in black.

### 4.1 {table_name_1}

{1–2 sentences describing this table's purpose, who writes to it, who reads it, and the expected data volume.}

| Column | Type | Constraints | Index | Description |
|--------|------|-------------|-------|-------------|
| {col_id} | {BIGSERIAL} | {PK} | {PK} | {business meaning} |
| {display_name} | {TEXT} | {NOT NULL, UNIQUE} | {UNIQUE} | {business meaning} |
| {code_name} | {TEXT} | {NOT NULL, UNIQUE} | {UNIQUE} | {business meaning; immutable after creation, naming rules in §11.2} |
| {some_enum} | {TEXT} | {NOT NULL} | {BTREE} | {stores the {EEnumName1} literal, see §2.5.1} |
| {some_json} | {JSONB} | {NOT NULL} | {sub-field expression index: `(({some_json}->>'{sub_field}')::int)`} | {business meaning} |
| {is_active} | {BOOLEAN} | {NOT NULL DEFAULT true} | {BTREE} | {business meaning} |
| {deleted_at} | {TIMESTAMPTZ} | {NULL} | {BTREE} | {soft delete timestamp; NULL means not deleted} |
| {created_at} | {TIMESTAMPTZ} | {NOT NULL DEFAULT NOW()} | {BTREE} | {creation time} |
| {updated_at} | {TIMESTAMPTZ} | {NOT NULL DEFAULT NOW()} | {None} | {last update time; auto-update via DB trigger} |

> **Mandatory Indexing Requirement**: any column that appears in an API's `WHERE` filter condition or `ORDER BY` sort field
> must have its index type documented in this table. A column whose Index cell is `None` must not appear as a
> query parameter or sort parameter in §5 — this is a standing review check item.

**Index Summary**:

| Index Name | Type | Field(s) | Purpose |
|--------|------|------|------|
| `{idx_name_1}` | {BTREE} | {col} | {supports the {param} filter in §5.1.1} |
| `{idx_name_2}` | {BTREE} | {col_a, col_b} | {supports the composite sort in §5.1.1} |

### 4.2 {code_name}_{suffix} (Dynamic Sharded Table)

> **How to write a dynamic sharded table template**: this component dynamically creates data tables by {sharding dimension}, with the table name template
> `{code_name}_{suffix}`, where `{code_name}` is taken from §4.1 `{table_name_1}.code_name`.

| Item | Content |
|------|------|
| **Table Name Template** | `{code_name}_{suffix}` |
| **Sharding Dimension** | {e.g.: one table per information source} |
| **Creation Timing** | {e.g.: dynamically created by {daemon_name} the first time the {dimension object} performs {action}} |
| **Handling When the Table Does Not Exist** | {single-table query API returns 404; cross-table aggregation API skips tables that don't exist, without erroring} |
| **Table Creation Safety** | {the table name is built by concatenating `code_name`, which must first be validated against the §11.2 regex to prevent SQL injection} |
| **Cleanup Policy** | {e.g.: after a {dimension object} is soft-deleted, its sharded table is retained / cleaned up by {daemon}} |

**Table Structure**:

| Column | Type | Constraints | Index | Description |
|--------|------|-------------|-------|-------------|
| {col_id} | {BIGSERIAL} | {PK} | {PK} | {business meaning} |
| {parent_code_name} | {TEXT} | {NOT NULL} | {BTREE} | {redundantly stores the code_name of the owning {dimension object}} |
| {exe_status} | {TEXT} | {NOT NULL} | {BTREE} | {stores the {EEnumName} literal} |
| {exe_name} | {TEXT} | {NOT NULL} | {BTREE} | {the name of the program that wrote this record; supports querying by program name} |
| {created_at} | {TIMESTAMPTZ} | {NOT NULL DEFAULT NOW()} | {BTREE} | {creation time} |

> Add or remove §4.x subsections according to the number of tables.

---

## 5 API Definitions

> API design conventions (prefixes, resource naming, method semantics, unified response body, batch operation naming) follow
> [technical_overview.md](../../technical_overview.md) §4.1.
>
> This component's external API prefix: `/api/v1/`, listening port in §9.
>
> **Cross-Module Call Note**: this component's {list the endpoints called across modules} is also called by `{other_module}`,
> per [technical_overview.md](../../technical_overview.md) §4.{n}. These endpoints must be shown in a "cross-module call" group
> on the caller's API debug page.

**Endpoint Overview**:

| # | Group | Endpoint Count | Description |
|---|------|-------|------|
| 1 | §5.1 {Resource1} CRUD | {5} | {description} |
| 2 | §5.2 {Resource2} Query | {4} | {description} |
| | **Total** | **{n}** | |

> **Consistency Requirement**: the endpoint count and paths must agree across three places —
> this chapter, the API debug page section of `{component_code_name}_uiux.md`, and `{component_code_name}_api.mmd`.

### 5.0 API Overview Diagram

![API Overview](./{component_code_name}_api.png)

> Source file: [{component_code_name}_api.mmd](./{component_code_name}_api.mmd)
>
> Drawing conventions: see [templates/mermaid/](../../../templates/mermaid/):
> white canvas, nodes colored by type (page / this module's API / cross-module caller), all text in black.
> Each API group uses one subgraph, with the subgraph title carrying this document's section number.

### 5.1 {Resource1} CRUD

{1–2 sentences describing which pages this group of endpoints serves and which business object it corresponds to.}

**Writable Field Scope** (shared by POST / PUT):

| Category | Fields | Description |
|------|------|------|
| **Writable** | {list all writable fields} | {submitted by the client} |
| **Not Writable — System-Generated** | {id}, {created_at}, {updated_at} | {generated by the database} |
| **Not Writable — System-Computed** | {stat_field_1}, {stat_field_2} | {refreshed periodically by {daemon_name}; client submissions are ignored} |
| **Not Writable — Status Field** | {deleted_at} | {changeable only via the DELETE endpoint} |
| **Immutable After Creation** | {code_name} | {see the field-level constraints in §2.2} |

#### 5.1.1 Get {Resource1} List

| Item | Detail |
|------|--------|
| **Endpoint** | `GET /api/v1/{resource-1}` |
| **Description** | {description} |
| **Authentication** | {Required / Optional / None} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{filter_1}` | query | {string} | N | {filter condition; corresponds to {col} in §4.1, which has a {BTREE} index} |
| `{filter_2}` | query | {bool} | N | {filter condition} |
| `include_deleted` | query | {bool} | N | {default false; returns soft-deleted records when true} |
| `keyword` | query | {string} | N | {fuzzy search field scope: {col_a}, {col_b}; case-insensitive} |
| `sort_by` | query | {string} | N | {**must exhaustively enumerate every legal value**; never write "etc."}: `{col_1}`, `{col_2}`, `{col_3}` |
| `sort_order` | query | {string} | N | `asc` / `desc`, defaults to `{desc}` |
| `page` | query | {int} | N | {default 1} |
| `page_size` | query | {int} | N | {default 50, max {200}} |

> **Special Note on Sort Fields**: if a `sort_by` value is actually a JSONB sub-field (e.g. `{sub_field}`),
> the SQL expression mapping must be documented here: `ORDER BY (({some_json}->>'{sub_field}')::int)`,
> otherwise implementers will mistakenly assume there is a standalone column of the same name.

**Request Example**:

```
GET /api/v1/{resource-1}?{filter_1}={value}&sort_by={col_1}&sort_order=asc&page=1&page_size=50
```

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "{col_id}": 1,
        "{display_name}": "{example value}",
        "{code_name}": "{example_code_name}",
        "{some_enum}": "{VALUE_1}",
        "{is_active}": true,
        "{deleted_at}": null,
        "{created_at}": "2026-01-01T00:00:00Z",
        "{updated_at}": "2026-01-01T00:00:00Z"
      }
    ],
    "total": 1,
    "page": 1,
    "page_size": 50
  }
}
```

**Error Responses**:

| HTTP | code | Trigger Condition |
|------|------|---------|
| 400 | {40001} | {invalid parameter format, e.g. sort_by not in the list of legal values} |
| 500 | {50001} | {internal server error} |

#### 5.1.2 Get a Single {Resource1}

| Item | Detail |
|------|--------|
| **Endpoint** | `GET /api/v1/{resource-1}/{id}` |
| **Description** | {description} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{id}` | path | {int} | Y | {description} |

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "{col_id}": 1,
    "{display_name}": "{example value}"
  }
}
```

**Error Responses**:

| HTTP | code | Trigger Condition |
|------|------|---------|
| 404 | {40401} | {record does not exist} |

#### 5.1.3 Create {Resource1}

| Item | Detail |
|------|--------|
| **Endpoint** | `POST /api/v1/{resource-1}` |
| **Description** | {description} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{display_name}` | body | {string} | Y | {description; length {1–128}} |
| `{code_name}` | body | {string} | Y | {description; format validation in §11.2; immutable after creation} |
| `{some_enum}` | body | {string} | Y | {legal values in §2.5.1} |

**Field-Level Validation Rules**: see §11.3. The server must complete all validation before writing to the database; return 400 if non-compliant.

**Request Example**:

```json
{
  "{display_name}": "{example value}",
  "{code_name}": "{example_code_name}",
  "{some_enum}": "{VALUE_1}"
}
```

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "{col_id}": 1
  }
}
```

**Error Responses**:

| HTTP | code | Trigger Condition |
|------|------|---------|
| 400 | {40001} | {field validation failed; the message returns the specific field name} |
| 409 | {40901} | {`{code_name}` or `{display_name}` already exists} |

#### 5.1.4 Update {Resource1}

| Item | Detail |
|------|--------|
| **Endpoint** | `PUT /api/v1/{resource-1}/{id}` |
| **Description** | {full update; writable field scope is described at the top of this section} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{id}` | path | {int} | Y | {description} |
| `{display_name}` | body | {string} | Y | {description} |

> **Immutable Field Note**: if the request body includes `{code_name}`, the server must verify it matches the existing value;
> if it does not match, return 400 — it must not be silently ignored.

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

#### 5.1.5 Delete {Resource1} (Soft Delete)

| Item | Detail |
|------|--------|
| **Endpoint** | `DELETE /api/v1/{resource-1}/{id}` |
| **Description** | {soft delete: sets `deleted_at` to the current time; the record is not physically deleted} |
| **Authentication** | {Required} |

**Cascade Handling**:

| Related Data | Handling |
|---------|---------|
| {related table/sharded table} | {no cascade handling / marks {status} records as {terminal state}} |

> Whether cascading occurs must be stated explicitly — leaving it blank leads implementers to each guess independently.

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

### 5.2 {Resource2} Query

{Same structure as 5.1; add or remove endpoints as needed.}

#### 5.2.1 {Endpoint Name}

| Item | Detail |
|------|--------|
| **Endpoint** | `{METHOD} /api/v1/{resource-2}/{path}` |
| **Description** | {description} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{param}` | {path/query/body} | {type} | {Y/N} | {description} |

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

#### 5.2.2 Batch {Action}

| Item | Detail |
|------|--------|
| **Endpoint** | `POST /api/v1/{resource-2}/batch-{action}` |
| **Description** | {batch operation; a single-item operation still uses an array with 1 element} |
| **Authentication** | {Required} |

**Request Example**:

```json
{
  "{id_list}": [1, 2, 3]
}
```

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "succeeded": 3,
    "failed": 0
  }
}
```

> Add or remove §5.x subsections according to the number of resource groups.

### 5.3 Common Pagination Response Format

> Follows [technical_overview.md](../../technical_overview.md) §4.1.1. This section only restates this component's specific values.

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

| Field | Type | Description |
|------|------|------|
| `items` | array | {records on the current page} |
| `total` | int | {total number of matching records} |
| `page` | int | {current page number, starting at 1} |
| `page_size` | int | {records per page; fixed at {50} for this component} |

### 5.4 Common Error Code Definitions

> Global error codes are in [technical_overview.md](../../technical_overview.md) §4.1.2.
> This section lists only the error codes **specific to this component**; they must not conflict with the global error codes.

| HTTP | code | message | Trigger Condition |
|------|------|---------|---------|
| 400 | {40001} | {invalid parameter} | {parameter validation failed} |
| 404 | {40401} | {not found} | {record does not exist; or the dynamic sharded table has not been created yet} |
| 409 | {40901} | {conflict} | {unique constraint violation} |
| 500 | {50001} | {internal error} | {internal server error} |

### 5.5 Request/Response JSON Schema Examples

> Used for frontend type generation and the API debug page's parameter templates. The full OpenAPI contract is in
> `src/apis/{component_code_name}/openapi.yaml`.

**{CClassName1} Full JSON Structure**:

```json
{
  "{col_id}": 1,
  "{display_name}": "{example value}",
  "{code_name}": "{example_code_name}",
  "{some_enum}": "{VALUE_1}",
  "{some_json}": {
    "{sub_field_1}": 0,
    "{sub_field_2}": 0
  },
  "{is_active}": true,
  "{deleted_at}": null,
  "{created_at}": "2026-01-01T00:00:00Z",
  "{updated_at}": "2026-01-01T00:00:00Z"
}
```

**Field Descriptions**:

| Field | JSON Type | Nullable | Description |
|------|----------|------|------|
| `{col_id}` | number | N | {description} |
| `{some_json}` | object | N | {sub-field structure description} |
| `{deleted_at}` | string \| null | Y | {ISO 8601; null means not deleted} |

---

## 6 Development Technology

> **Version Consistency Requirement**: the language versions, dependency versions, and middleware versions listed in this section **must exactly match**
> [technical_overview.md](../../technical_overview.md) §5.
> If this component needs a new language or major dependency, the platform-level document must be updated first, then referenced here.

### 6.1 Operating System

| OS | Version | Notes |
|----|---------|-------|
| {Linux} | {Ubuntu 22.04} | {production} |
| {Windows} | {11} | {development} |

### 6.2 Software Form

| Module | Form | Description |
|--------|------|-------------|
| {module_1} | {Service} | {REST API service} |
| {module_2} | {Daemon} | {resident collection process} |
| {module_3} | {Frontend} | {frontend page package} |

### 6.3 Programming Language

| Language | Version | Usage | Corresponding Platform-Level Section |
|----------|---------|-------|--------------|
| {Go} | {1.25.x} | {backend service and daemons} | technical_overview.md §5.1 |
| {TypeScript} | {5.x} | {frontend} | technical_overview.md §5.1 |

### 6.4 Major Dependencies

| Dependency | Version | Purpose | Corresponding Platform-Level Section |
|-----------|---------|---------|--------------|
| {Gin} | {1.x} | {web framework} | technical_overview.md §5.3 |
| {GORM} | {2.x} | {ORM} | technical_overview.md §5.3 |
| {React} | {18.x} | {frontend framework} | technical_overview.md §5.2 |
| {PostgreSQL} | {17.x} | {database} | technical_overview.md §5.5 |

### 6.5 Release Form

| Artifact | Form | Description |
|----------|------|-------------|
| {artifact_1} | {Binary} | {single executable file} |
| {artifact_2} | {Static file directory} | {frontend build output} |
| {artifact_3} | {Docker image} | {image name and tag rules} |

---

## 7 Environment Configuration

> **OPTIONAL SECTION**: keep this section only if this component is an independently deployable service/daemon (requiring environment variables or a config file).
> Pure frontend packages or pure libraries should delete this entire section and renumber the following sections.

### 7.1 Environment Variable List

| Variable | Required | Default | Description |
|--------|------|-------|------|
| `{COMPONENT}_DB_DSN` | Y | — | {database connection string} |
| `{COMPONENT}_PORT` | N | {8081} | {HTTP listening port} |
| `{COMPONENT}_LOG_LEVEL` | N | {INFO} | {log level: DEBUG/INFO/WARN/ERROR} |

> **Sensitive Information**: passwords, tokens, and secrets must always be injected via environment variables — **never write them into a config file and commit it to the repository** —
> following [technical_overview.md](../../technical_overview.md) §4.6.

### 7.2 Configuration File Format

Config file path: `{config/config.yaml}`

```yaml
server:
  port: {8081}
  read_timeout_ms: {5000}
  write_timeout_ms: {5000}

database:
  max_open_conns: {20}
  max_idle_conns: {5}

log:
  level: {INFO}
  format: {json}
```

| Config Item | Type | Default | Description |
|--------|------|-------|------|
| `server.port` | int | {8081} | {description; can be overridden by an environment variable} |
| `database.max_open_conns` | int | {20} | {description} |

---

## 8 Build & Run

> **OPTIONAL SECTION**: keep this section only if this component has a buildable executable artifact.

**Build**:

```bash
{build_command}
```

**Run (Development)**:

```bash
{run_dev_command}
```

**Run (Production)**:

```bash
{run_prod_command}
```

**Health Check**:

```bash
curl http://localhost:{8081}/healthz
```

| Check | Endpoint | Expected Response |
|--------|------|---------|
| Liveness | `GET /healthz` | `{"code":0,"message":"success"}` |
| Readiness | `GET /readyz` | {description: includes database connectivity check} |

---

## 9 Server Parameters

> **OPTIONAL SECTION**: keep this section only if this component is a backend service.

| Parameter | Value | Description |
|------|-----|------|
| Listening Port | {8081} | {matches the port allocation table in technical_overview.md §3} |
| API Prefix | `/api/v1/` | {follows technical_overview.md §4.1} |
| Read Timeout | {5000 ms} | {description} |
| Write Timeout | {5000 ms} | {description} |
| Max DB Connections | {20} | {description} |
| Idle DB Connections | {5} | {description} |
| Graceful Shutdown Wait | {10 s} | {description} |

---

## 10 Source Code Directory Layout

> Directory naming follows [naming_convention.md](../../naming_convention.md):
> `src/backend/` uses snake_case, `src/frontend/` uses kebab-case.

**Backend**:

```
src/backend/{component_code_name}/
├── cmd/                        # Entry points for each executable program
│   ├── server/                 # REST API service entry point            [v0.3.2+]
│   └── {daemon_name}/          # Daemon program entry point              [v0.5+]
├── config/                     # Config loading                          [v0.3.2+]
├── internal/
│   ├── handler/                # HTTP layer: param parsing, validation, response wrapping [v0.3.2+]
│   ├── service/                # Business logic layer                    [v0.3.2+]
│   ├── repository/             # Data access layer (interfaces + implementation) [v0.3.2+]
│   ├── model/                  # Data structure definitions (maps to §2 class definitions) [v0.3.2+]
│   ├── enum/                   # Enum definitions (maps to §2.5)         [v0.3.2+]
│   └── mock/                   # Hardcoded mock data (test use only)     [v0.3.2+]
├── pkg/                        # Reusable utility packages (response wrapping, error codes, etc.) [v0.3.2+]
└── sql/                        # Database scripts                        [v0.3.2+]
    ├── init_databases.sql      # DB creation script
    ├── migrations/             # Migration scripts
    └── sample_data.sql         # Sample data
```

**Frontend**:

```
src/frontend/packages/{component-code-name}/
├── src/
│   ├── pages/                  # Page components, map to P01–P{nn} in uiux.md   [v0.2+]
│   │   └── {page-name}/
│   ├── components/             # Components reused within this package         [v0.2+]
│   ├── models/                 # TypeScript type definitions (maps to §2 class definitions) [v0.2+]
│   ├── mock/                   # Hardcoded data                                [v0.2 used / removed at v0.4+]
│   ├── services/               # API client wrapper                           [v0.4+]
│   ├── hooks/                  # Data hooks                                    [v0.4+]
│   ├── stores/                 # State management                             [v0.2+]
│   └── routes.tsx              # Route registration (maps to §4 route definitions in uiux.md) [v0.2+]
└── package.json
```

**Version Scope Description**:

| Version | This Component's Directory Scope |
|------|----------------|
| {v0.1} | {Baseline design docs only; backend entity definitions defined in documentation, no code yet} |
| {v0.2} | {Frontend only; `mock/` supplies all data; no `services/` or `hooks/`} |
| {v0.3.1} | {Technical design completed; no new code directories} |
| {v0.3.2} | {Adds all backend code: `cmd/server`, `handler`, `service`, `repository`, `model`, `enum`, `pkg`, `sql/`; backend `mock/` becomes test-only} |
| {v0.4} | {Three-tier integration; frontend adds `services/`, `hooks/`; frontend `mock/` references are removed} |
| {v0.5+} | {Adds `cmd/{daemon_name}/` for each daemon program} |

> Detailed version scope is in [{component_code_name}_version_plan.md](./{component_code_name}_version_plan.md).

---

## 11 Appendix

### 11.1 Glossary

> Contains only technical terms **private to this component**. Platform-wide common terms are in
> [technical_overview.md](../../technical_overview.md) §7.2.

| Term | Definition |
|------|-----------|
| {term1} | {definition} |
| {term2} | {definition} |

### 11.2 code_name Naming Rules

`code_name` is {explain its purpose, e.g.: the naming basis for all dynamic sharded tables}, so its format must be strictly constrained.

| Item | Rule |
|------|------|
| **Regex** | `^[a-z][a-z0-9_]*$` |
| **Length** | {3–64} characters |
| **Case** | all lowercase snake_case |
| **Mutability** | immutable after creation |
| **Validation Timing** | {validated on the frontend before submission + enforced on the server before writing to the database}; returns 400 if non-compliant |

**Frontend Auto-Generation Rules**:

| # | Rule | Description |
|---|------|------|
| 1 | Auto-generated when {display_name} loses focus | {fully lowercased; spaces and punctuation replaced with underscores} |
| 2 | When non-ASCII characters are present | {code_name is left blank; the user must fill it in manually before saving} |
| 3 | Before saving | {the frontend validates the format locally first; if non-compliant, it prompts the user and does not send the request} |

> **Security Requirement**: `code_name` is concatenated into `CREATE TABLE` / query statements to build table names.
> The server must re-validate it against the regex before any concatenation — **it must never trust the client-side validation result**.

### 11.3 Input Validation Rules

> Validation is completed at the handler layer, following [technical_overview.md](../../technical_overview.md) §4.4.

| Field | Rule | Message on Failure |
|------|------|-----------------|
| `{display_name}` | {non-empty; length 1–128} | {`{display_name} is required`} |
| `{code_name}` | {regex `^[a-z][a-z0-9_]*$`; length 3–64} | {`invalid {code_name} format`} |
| `{some_enum}` | {must be within the legal value set in §2.5.1} | {`invalid {some_enum} value`} |
| `{some_int}` | {range {min}–{max}} | {`{some_int} out of range`} |
| `{url_field}` | {valid URL; scheme limited to http/https} | {`invalid url`} |
| `{conditional_field}` | {required when `{trigger_field}` = {value}} | {`{conditional_field} is required when ...`} |

### 11.4 Logging Conventions

> Level definitions, output format, and trace_id propagation follow
> [technical_overview.md](../../technical_overview.md) §4.5. This section lists only this component's specific conventions.

| Scenario | Level | Required Fields | Example |
|------|------|---------|------|
| {HTTP request} | INFO | {method, path, status, duration_ms, trace_id} | {example} |
| {daemon polling starts} | INFO | {exe_name, {code_name}} | {example} |
| {task failure} | ERROR | {exe_name, {code_name}, error, retry_count} | {example} |
| {external request retry} | WARN | {url, attempt, error} | {example} |

**Masked Fields**: {list the fields that must be masked, e.g. `{media_pwd}`, `token`}.

### 11.5 Test Strategy

| Layer | Test Type | Coverage Requirement | Tool |
|----|---------|---------|------|
| {service} | {unit tests} | {core business logic branches} | {tool} |
| {repository} | {integration tests} | {queries that depend on a real database} | {tool} |
| {handler} | {interface tests} | {parameter validation and error codes} | {tool} |
| {frontend} | {no automated testing / explanation} | {follows uiux_design_specification.md §17} | — |

**Test Data**: {describe the source, e.g. `sql/sample_data.sql`}.

---

## Change Log

| Version | Date | Change Description |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {change description} |
| {x.y.z} | {YYYY-MM-DD} | {initial version} |
