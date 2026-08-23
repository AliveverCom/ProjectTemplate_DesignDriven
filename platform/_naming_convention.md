# Project Naming Convention

**Document Version**: {x.y.z}

<!--
TEMPLATE NOTES (delete this comment block when using)
- Level: **platform-level naming convention**. Place in the `docs/` root directory; drop the leading underscore from the filename → `naming_convention.md`.
- This document is the **single authoritative** source for the abbreviation table and type-prefix table. Component-level documents must not invent their own abbreviations or prefixes;
  when a new abbreviation is needed, **update this document first**, then use it in component documents and code.
- Division of labor with other platform-level documents:
  - This document governs **how identifiers are named** (abbreviations, C/E/I/cfg prefixes, variable and column naming).
  - `technical_overview.md` §4 governs **technical specifications** (API shapes, table design rules).
  - `project_directory_plan.md` §1.1 governs **directory and filename casing style** (snake_case / kebab-case).
  The three do not overlap; cross-reference as needed.
- Note this document has a **scope limitation**: the C/E/I/cfg prefixes are used only in technical documents and code;
  business documents such as business_desc / business_process / uiux use natural names, without prefixes.
-->

> This document defines the naming conventions that the project's **technical design (tech_design) and code implementation** must consistently follow.
>
> **Scope**: `tech_design` documents, database schemas, class diagrams (`class_diagram.mmd`),
> ER diagrams (`db_schema.mmd`), source code.
>
> **Out of scope**: `business_desc` documents, `business_process` diagrams, `uiux` documents, and page display text.
> In business documents, business objects should use their **natural names** (such as `{MediaBase}`, `{Channel}`, `{MediaType}`),
> **without** technical prefixes such as C / E / I.

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Common Abbreviations](#2-common-abbreviations)
  - [2.1 Business Domain](#21-business-domain)
  - [2.2 General Purpose](#22-general-purpose)
  - [2.3 Usage Rules](#23-usage-rules)
- [3 Type Definition Naming Convention](#3-type-definition-naming-convention)
  - [3.1 Class Prefix: C](#31-class-prefix-c)
  - [3.2 Enum Prefix: E](#32-enum-prefix-e)
  - [3.3 Special Interface Prefix: I](#33-special-interface-prefix-i)
  - [3.4 Configuration Property Prefix: cfg](#34-configuration-property-prefix-cfg)
  - [3.5 Summary Table](#35-summary-table)
- [4 Variable & Column Naming Rules](#4-variable--column-naming-rules)
- [5 Change Log](#5-change-log)

---

## 2 Common Abbreviations

> This abbreviation table applies to **all document types** (including both business and tech).
> However, the type-definition prefixes in §3 (C / E / I / cfg) are used **only in technical documents and code**.

### 2.1 Business Domain

The following abbreviations are used for business-domain terms in the project, consistently applied in variable names, column names, file names, and similar contexts.

| English Full Name | Abbreviation | Description | Examples |
|-------------------|-------------|------|----------|
| {channel} | `{chn}` | {Broadcast channel} | `{chn_id}`, `{chn_tags}` |
| {program} | `{prog}` | {Program / show} | `{prog_id}`, `{prog_tags}` |
| {media} | `{media}` | {Media} | `{media_id}`, `{media_type}` (not abbreviated) |
| {source} | `{src}` | {Source} | `{src_medias}` |
| {target} | `{tgt}` | {Target} | `{tgt_medias}` |
| {material} | `{mtrl}` | {Material} | `{mtrl_id}`, `{mtrl_status}` |
| {collector} | `{cltr}` | {Collector} | `{cltr_status}` |
| {Business Term} | `{Abbreviation}` | {Description} | `{Example}` |

> Add rows according to the project's actual business domain. **Any new abbreviation must be added to this table first** before it may be used in code.

### 2.2 General Purpose

The following are general-purpose (non-business-domain) abbreviations.

| English Full Name | Abbreviation | Description | Examples |
|-------------------|-------------|------|----------|
| {configuration} | `{cfg}` | {Configuration} | `{cfg_scraper}` |
| {execution} | `{exe}` | {Execution} | `{exe_status}`, `{exe_start}` |
| {record} | `{rec}` | {Record} | `{rec_id}` |
| {instance} | `{ins}` | {Instance} | `{ins_id}` |
| {pipeline} | `{pip}` | {Pipeline} | `{pip_task}` |
| {description} | `{desc}` | {Description} | `{media_desc}` |
| {General Term} | `{Abbreviation}` | {Description} | `{Example}` |

### 2.3 Usage Rules

| # | Rule | Description |
|---|------|------|
| 1 | **Variable / Column Names** | Use lowercase `snake_case`; abbreviations are not capitalized (e.g., `{chn_id}`, `{prog_tags}`) |
| 2 | **Class / Enum / Interface Names** | Use `PascalCase`; the first letter of abbreviations is capitalized (e.g., `{CChannel}`, `{EMediaType}`) |
| 3 | **Consistency First** | The same concept must use the **same abbreviation** throughout the entire project — mixing is not allowed (e.g., channel must always use `{chn}`; `ch` or `chan` must not appear) |
| 4 | **Readability First** | If an abbreviation is not in this document's list, prefer the **full word** rather than inventing a non-obvious abbreviation. **New abbreviations must be added to this document before use** |
| 5 | **Boolean Properties** | Prefixed with `is_`, `has_`, `can_`, `need_`, etc.; the following word is **not abbreviated** (e.g., `is_active`, `need_login`) |
| 6 | **Cross-Module Consistency** | The same business object must use exactly the same field name across all modules; when a discrepancy is found, the **authoritative module** governs, and the other module's documents must be updated to match |

---

## 3 Type Definition Naming Convention

> **Important**: The C / E / I / cfg prefix rules in this section apply **only to** tech_design documents, database schemas,
> class diagrams, ER diagrams, and source code.
> In non-technical documents such as business_desc, business_process, and uiux, business objects and enums use **natural names, without prefixes**.

### 3.1 Class Prefix: C

All class definitions are named starting with the uppercase letter **C**, denoting Class.

| Convention | Format | Example |
|-----------|--------|---------|
| Class Name | `C` + PascalCase | `{CMediaBase}`, `{CSourceMedia}`, `{CChannel}` |
| Database Model Class | `C` + PascalCase + `Model` | `{CChannelModel}` |
| Service Class | `C` + PascalCase + `Service` | `{CChannelService}` |
| Repository Class | `C` + PascalCase + `Repo` | `{CChannelRepo}` |

### 3.2 Enum Prefix: E

All enum type definitions are named starting with the uppercase letter **E**, denoting Enum.

| Convention | Format | Example |
|-----------|--------|---------|
| Enum Name | `E` + PascalCase | `{EMediaType}`, `{ETriggerStrategy}`, `{EExeStatus}` |

Enum **values** themselves use all-uppercase `UPPER_SNAKE_CASE` (e.g., `{NEWS_FEED}`, `{WHOLE_SITE}`), **without any prefix**.

> **Enum Ownership Rule**: Each enum has exactly one **authoritative defining module**, defined in that module's `tech_design.md` §2.
> In other modules' class diagrams, it appears as a "reference" (dashed box + `<<enumeration - ref: {module}>>`),
> with the authoritative definition location noted in a comment.

### 3.3 Special Interface Prefix: I

All **special custom interfaces** (non-default CRUD) are named starting with the uppercase letter **I**, denoting Interface.
Default create/read/update/delete interfaces **do not need** the I prefix.

| Convention | Format | Example |
|-----------|--------|---------|
| Special Interface Name | `I` + PascalCase | `{IChannelTags}`, `{IBatchImport}` |
| Default CRUD | No prefix | `{CreateChannel}`, `{GetMediaBase}`, `{UpdateProgram}`, `{DeleteSourceMedia}` |

> **Criterion**: If an interface performs a Create / Read / Update / Delete operation on a **single business object**,
> it is a default CRUD interface and does not take the `I` prefix. All other interfaces (aggregate queries, batch operations, cross-object associations, virtual property computation, etc.) take the `I` prefix.

### 3.4 Configuration Property Prefix: cfg

Properties within a class that represent **configuration** start with lowercase `cfg`, denoting Configuration.
The **internal properties of a configuration class itself do not need** the `cfg` prefix again.

| Convention | Format | Example |
|-----------|--------|---------|
| Configuration Property in a Class | `cfg` + PascalCase (in a snake_case context, `cfg_xxx`) | `{cfg_retry_policy}`, `{cfg_scraper}` |
| Configuration Class Name | `C` + `Cfg` + PascalCase | `{CCfgRetryPolicy}`, `{CCfgScraper}` |
| Internal Property of a Configuration Class | Named normally, **no** `cfg` prefix | `{max_retries}`, `{timeout_ms}`, `{base_url}` |

**Example**:

```
{CSourceMedia}:
    ...
    cfg_scraper: {CCfgScraper}      # Configuration property, starts with cfg

{CCfgScraper}:                      # Configuration class, starts with CCfg
    max_retries: int                # Internal property of configuration class, no cfg prefix
    timeout_ms: int
    user_agent: string
```

### 3.5 Summary Table

| Type | Prefix | Case | Example |
|------|--------|------|---------|
| Class | `C` | PascalCase | `{CChannel}`, `{CMediaBase}` |
| Enum | `E` | PascalCase | `{EMediaType}`, `{EExeStatus}` |
| Special Interface | `I` | PascalCase | `{IChannelTags}`, `{IBatchImport}` |
| Config Property | `cfg` | snake_case or camelCase | `{cfg_retry_policy}`, `{cfgAuth}` |
| Config Class | `CCfg` | PascalCase | `{CCfgScraper}` |
| CRUD Interface | (none) | PascalCase | `{CreateChannel}`, `{GetMediaBase}` |
| Variable / Column | (none) | snake_case | `{chn_id}`, `{prog_tags}` |
| Enum Value | (none) | UPPER_SNAKE_CASE | `{NEWS_FEED}`, `{WHOLE_SITE}` |

---

## 4 Variable & Column Naming Rules

| Rule | Description | Example |
|------|-------------|---------|
| snake_case | Variable names and database column names uniformly use lowercase with underscores | `{media_id}`, `{chn_timezone}` |
| Prefix Indicates Ownership | Property names are prefixed with the abbreviation of their owning object (when the property could be ambiguous in context) | `{chn_id}`, `{prog_id}` |
| Omit Prefix When Unambiguous | Generic properties do not need a prefix | `{display_name}`, `{code_name}`, `{is_active}` |
| Foreign Key Naming | Target object abbreviation + `_id` | `{chn_id}` (→ Channel), `{media_id}` (→ MediaBase) |
| Boolean Fields | Start with `is_` / `has_` / `can_` / `need_` | `{is_active}`, `{need_login}` |
| Timestamp Fields | End with `_at` to indicate a point in time | `{created_at}`, `{updated_at}`, `{deleted_at}` |
| List / Array Fields | Use the **plural** form | `{programs}`, `{src_medias}` |
| Unique Code Name | Fields used as sharding table names or externally unique identifiers are uniformly named `code_name`, format `{^[a-z][a-z0-9_]*$}`, **immutable after creation** | `{code_name}` |
| Display Name | Fields shown to end users are uniformly named `display_name` | `{display_name}` |
| Status Fields | Enum-typed status fields uniformly end with `_status` | `{exe_status}`, `{dist_status}` |

> **Column Names Map One-to-One with Class Members**: Database column names must exactly match the corresponding class member names (after removing the type prefix).
> If the two are inconsistent, this is a cross-document consistency defect and should be raised in a review under `ai_dev_history/01_DocReviewRefine/`.

---

## 5 Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {Change description} |
