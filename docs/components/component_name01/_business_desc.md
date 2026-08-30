<!--
TEMPLATE NOTES (delete this comment block when using)

[DOCUMENT LEVEL]
This file is the **component-level business document** template.

- Location: `docs/components/{component_code_name}/`
- File naming: replace the leading `_` with the component code name.
  Example: component `crawler` → `crawler_business_desc.md`

[BOUNDARY AGAINST THE PLATFORM-LEVEL DOCUMENT]
`business_overview.md` in the `docs/` root is the **platform-level** business document.
The two have similar names but sit at completely different levels:

| Dimension | Platform-level `docs/business_overview.md` | Component-level `{component}_business_desc.md` (this template) |
|-----------|-------------------------------------------|---------------------------------------------------------------|
| Subject | The business landscape of the whole platform | The internal business of a single product component |
| Content | Platform positioning, responsibility split across all components, cross-component end-to-end business processes, platform-level user roles | This component's business objects, this component's internal business processes, this component's inputs and outputs |
| Process diagram | Cross-component end-to-end flow (`docs/business_process.mmd`) | This component's internal flow only (`{component}_business_process.mmd`) |
| Glossary | **Authoritative definition** of platform-wide terms | This component's private terms only; platform-wide terms are referenced, never redefined |

**HARD RULES**:
1. A component-level document **must not redefine** terms, roles, or cross-component
   processes already defined at the platform level. Reference them by section number
   instead, e.g. `see [business_overview.md](../../business_overview.md) §x.y`.
2. A component-level document **contains no technical implementation detail**
   (classes, tables, APIs, tech stack). Those belong in
   `{component}_tech_design.md` in the same directory.
3. Every business object described here must have a matching class in
   `{component}_tech_design.md §2 Class Definitions`. The field sets on both sides
   must stay identical; any divergence is a documentation defect.

[DOCUMENT CONVENTIONS]
- Chapter 1 must be the Table of Contents.
- All sections use numeric numbering (1, 1.1, 1.1.1).
- Text inside `{curly braces}` is a placeholder; replace it with real content.
- Optional sections state their keep/delete conditions inline; delete the whole
  chapter when not needed and renumber the following chapters.
-->

# {ComponentName} ({component_code_name}) Business Description

**Document Version**: {x.y.z}

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Component Overview](#2-component-overview)
  - [2.1 Description](#21-description)
  - [2.2 Key Concepts](#22-key-concepts)
  - [2.3 Product Form](#23-product-form)
  - [2.4 Main Inputs and Outputs](#24-main-inputs-and-outputs)
- [3 Business Objects](#3-business-objects)
  - [3.1 {ObjectName1} ({ObjectDisplayName1})](#31-objectname1-objectdisplayname1)
  - [3.2 {ObjectName2} ({ObjectDisplayName2})](#32-objectname2-objectdisplayname2)
  - [3.3 {ObjectName3} ({ObjectDisplayName3})](#33-objectname3-objectdisplayname3)
- [4 Business Process](#4-business-process)
  - [4.1 Process Overview](#41-process-overview)
  - [4.2 Process Description](#42-process-description)
    - [4.2.1 {SubProcess1Name}](#421-subprocess1name)
    - [4.2.2 {SubProcess2Name}](#422-subprocess2name)
- [5 Data Migration](#5-data-migration)
  - [5.1 Migration Background](#51-migration-background)
  - [5.2 Migration Overview](#52-migration-overview)
  - [5.3 Data Mapping](#53-data-mapping)
  - [5.4 Migration Tool Usage](#54-migration-tool-usage)
  - [5.5 Known Limitations](#55-known-limitations)
- [6 Appendix](#6-appendix)
  - [6.1 Glossary](#61-glossary)
  - [6.2 Enum Definitions](#62-enum-definitions)
  - [6.3 Scoring Standards](#63-scoring-standards)
- [Change Log](#change-log)

---

## 2 Component Overview

### 2.1 Description

{Use 2–5 paragraphs to explain what this component is, what responsibility it carries
within the platform, and what output it provides to which downstream components.
The first paragraph must independently answer "what does this component do?".}

{ComponentName} ({component_code_name}) is the {one-line positioning} component of this
platform. It is responsible for {core responsibility} and supplies {output artifact} to
the downstream components {DownstreamComponent1} (`{downstream_1}`) and
{DownstreamComponent2} (`{downstream_2}`).

The main capabilities of this component are:

- **{Capability1}**: {description}
- **{Capability2}**: {description}
- **{Capability3}**: {description}

> For this component's position in the platform-wide business chain, see
> [business_overview.md](../../business_overview.md) §1.2.{n}.

### 2.2 Key Concepts

> **OPTIONAL SECTION**: Keep this section when the component has several core concepts
> that must be understood before the rest of the document makes sense — especially when
> those concepts contain, derive from, or relate many-to-many to one another. Delete this
> section and renumber when the concepts are simple and self-evident.

Before reading the rest of this document, the following core concepts and their
relationships must be understood:

| Concept | English / Code Name | Definition | Relationship to Other Concepts |
|---------|--------------------|-----------|-------------------------------|
| {Concept1} | `{concept_1}` | {one-line definition} | {e.g. one {Concept1} may contain many {Concept2}} |
| {Concept2} | `{concept_2}` | {one-line definition} | {e.g. belongs to exactly one {Concept1}} |
| {Concept3} | `{concept_3}` | {one-line definition} | {e.g. {Concept1} and {Concept3} are many-to-many} |

{Use 1–3 paragraphs to clarify where these concepts are easily confused — for example,
the difference between two similarly named concepts, or a concept that means different
things in different contexts.}

### 2.3 Product Form

{State the product form of this component. Possible forms include: standalone daemon
program, backend REST API service, frontend page package, command-line tool, shared
library. Multi-form components must list every form.}

This component is delivered as {form summary}:

| # | Form | Name | Description |
|---|------|------|-------------|
| 1 | Daemon program | `{daemon_name}` | {responsibility; how it runs; trigger strategy} |
| 2 | REST API service | `{service_name}` | {which resources it exposes; integrated into unified_portal} |
| 3 | Frontend page package | `{package-name}` | {which pages it provides; presented as a menu group in unified_portal} |
| 4 | Command-line tool | `{cli_name}` | {purpose; when it is run} |
| 5 | Shared library | `{lib_name}` | {which components depend on it} |

> Technical implementation form (programming language, release form, deployment method)
> is described in
> [{component_code_name}_tech_design.md](./{component_code_name}_tech_design.md) §6.

### 2.4 Main Inputs and Outputs

| Direction | Name | Description |
|-----------|------|-------------|
| Input | {InputName1} | {data source; which component or external system provides it; data form} |
| Input | {InputName2} | {description} |
| Output | {OutputName1} | {what is produced; which downstream component consumes it; data form} |
| Output | {OutputName2} | {description} |

---

## 3 Business Objects

> Every business object listed in this chapter has a matching class definition in
> `{component_code_name}_tech_design.md §2 Class Definitions`. This chapter describes
> **business semantics** (what an attribute means to the business); the technical
> document describes **technical implementation** (type, storage requirements, indexes).
> The attribute sets on both sides must be identical.

### 3.1 {ObjectName1} ({ObjectDisplayName1})

{2–4 sentences of business description: what this object represents, who creates it,
what its lifecycle looks like, and who consumes it.}

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {attr1} | {int} | Y | {business meaning; value range or constraint} |
| {attr2} | {string} | Y | {business meaning} |
| {attr3} | {enum} | N | {business meaning; enum values explained in §6.2} |
| {attr4} | {datetime} | N | {business meaning; what an empty value means} |

**Business Rules**:

| # | Rule | Description |
|---|------|-------------|
| 1 | {Rule1} | {e.g. {attr2} is immutable once created} |
| 2 | {Rule2} | {e.g. {attr4} is mandatory when {attr3} = {value}} |

### 3.2 {ObjectName2} ({ObjectDisplayName2})

> **INHERITANCE PATTERN EXAMPLE** (use this pattern when the object derives from a base object)

{ObjectName2} inherits from {CBaseObject} ({BaseObjectDisplayName}).
The authoritative definition of the base class lives in
{the defining module, e.g. `common_lib` / `channel_management`}; see
[{base_module}_business_desc.md](../{base_module}/{base_module}_business_desc.md) §3.{n}.

**Attributes inherited from {CBaseObject}** (not repeated here; only this component's
special constraints on them are listed):

| Inherited Attribute | Special Constraint in This Component |
|--------------------|--------------------------------------|
| {base_attr1} | {e.g. in this component this field is always {value}} |
| {base_attr2} | {e.g. no special constraint} |

**Attributes added by {ObjectName2}**:

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {attr1} | {bool} | Y | {business meaning} |
| {attr2} | {string} | N | {business meaning; mandatory only when {attr1} = true} |

**Business Rules**:

| # | Rule | Description |
|---|------|-------------|
| 1 | {Rule} | {description} |

### 3.3 {ObjectName3} ({ObjectDisplayName3})

> **DYNAMIC SHARDED TABLE / TEMPLATE TABLE PATTERN EXAMPLE**
> (use this pattern when the object is stored across dynamically sharded tables)

{ObjectName3} is not a single collection. It is stored per {sharding dimension, e.g.
"source media"}: each {dimension object} owns a set of record collections prefixed with
its `code_name`.

| Record Collection | Naming Template | Purpose | Creation Timing |
|-------------------|-----------------|---------|-----------------|
| {Collection1} | `{code_name}_{suffix1}` | {purpose} | {e.g. created automatically the first time this {dimension object} performs {action}} |
| {Collection2} | `{code_name}_{suffix2}` | {purpose} | {creation timing} |

#### 3.3.1 {ObjectName3Base} ({BaseClassDisplayName})

All sharded collections share the same set of base attributes:

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {attr1} | {int} | Y | {business meaning} |
| {attr2} | {enum} | Y | {business meaning; enum values explained in §6.2} |
| {attr3} | {datetime} | Y | {business meaning} |

#### 3.3.2 `{code_name}_{suffix1}`

{Describe how this collection differs from the base class: which attributes it adds,
which attributes carry a different meaning here, when records are written, and when
they are cleaned up.}

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {extra_attr1} | {type} | {Y/N} | {business meaning} |

#### 3.3.3 `{code_name}_{suffix2}`

{Same structure as above.}

> Add or remove 3.x sections to match the number of business objects. Every object must
> have a matching class in tech_design §2.

---

## 4 Business Process

### 4.1 Process Overview

![{ComponentName} Business Process Diagram](./{component_code_name}_business_process.png)

> Source: [{component_code_name}_business_process.mmd](./{component_code_name}_business_process.mmd)
>
> Drawing conventions: see [templates/mermaid/](../../../templates/mermaid/) —
> white canvas, every box classified by type with its own light background color,
> all text in black.

{Use 1–2 paragraphs to summarize the overall flow: who initiates it, which major stages
it passes through, and under what conditions it terminates or loops.}

This component contains the following relatively independent business processes:

| # | Process | Trigger | Actor | Output |
|---|---------|---------|-------|--------|
| 1 | {SubProcess1Name} | {scheduled / manual / event-driven} | {role or program} | {output artifact} |
| 2 | {SubProcess2Name} | {trigger} | {role or program} | {output artifact} |

### 4.2 Process Description

#### 4.2.1 {SubProcess1Name}

| Item | Detail |
|------|--------|
| **Actor** | {who performs it: a specific user role, or a specific daemon program name} |
| **Action** | {what is done, in one sentence} |
| **Input Data** | {required data/resources and where they come from} |
| **Output/Result** | {data/artifacts produced and where they flow to} |

**Detailed Steps**:

| # | Step | Description |
|---|------|-------------|
| 1 | {Step1} | {what is done; decision conditions; how exceptions are handled} |
| 2 | {Step2} | {description} |
| 3 | {Step3} | {description} |

**Business Rules and Boundary Conditions**:

- {Rule1, e.g. skip this execution when {condition}}
- {Rule2, e.g. how the retry policy manifests in business terms after a failure}
- {Rule3, e.g. how concurrency conflicts with {other sub-process} are resolved}

#### 4.2.2 {SubProcess2Name}

| Item | Detail |
|------|--------|
| **Actor** | {who performs it} |
| **Action** | {what is done} |
| **Input Data** | {input} |
| **Output/Result** | {output} |

**Detailed Steps**:

| # | Step | Description |
|---|------|-------------|
| 1 | {Step1} | {description} |

**Business Rules and Boundary Conditions**:

- {Rule}

> Add or remove 4.2.x sections to match the number of sub-processes. Every sub-process
> must have a corresponding branch or subgraph in the §4.1 process diagram.

---

## 5 Data Migration

> **OPTIONAL SECTION**: Keep this chapter only when the component must migrate historical
> data from a legacy system or legacy project. Components with no data migration should
> delete the whole chapter and renumber the following chapters.

### 5.1 Migration Background

{Explain: what the legacy system is, why the migration is needed, the data volume, the
required migration time window, and whether the old and new systems run in parallel
during the migration.}

| Item | Content |
|------|---------|
| **Source system** | {legacy system name / legacy project path} |
| **Source storage** | {e.g. MySQL 5.7, database {old_db}} |
| **Target storage** | {e.g. PostgreSQL 17, database {new_db}} |
| **Data volume** | {order of magnitude of record count; estimated duration} |
| **Parallel strategy** | {one-shot cutover / dual-write in parallel / read-only migration} |

### 5.2 Migration Overview

{Use 1–2 paragraphs to describe the overall strategy: full or incremental, in what order
data sets are migrated, how re-entrancy is guaranteed (repeated runs must not produce
duplicate data), and how the result is verified.}

**Migration Scope**:

| # | Source Data Set | Target Data Set | Migration Mode | Description |
|---|-----------------|-----------------|----------------|-------------|
| 1 | {source_table_1} | {target_table_1} | {full / incremental} | {description} |
| 2 | {source_table_2} | {target_table_2} | {full / incremental} | {description} |

### 5.3 Data Mapping

#### 5.3.1 {source_table_1} → {target_table_1}

| Source Column | Source Type | Target Column | Target Type | Transformation Rule |
|---------------|-------------|---------------|-------------|---------------------|
| {src_col1} | {type} | {tgt_col1} | {type} | {direct mapping / type conversion / value mapping rule} |
| {src_col2} | {type} | {tgt_col2} | {type} | {e.g. legacy 0/1 maps to the new system's enum literals} |
| — | — | {tgt_col3} | {type} | {no counterpart in the source system; default value filled during migration} |
| {src_col4} | {type} | — | — | {column present in the source but NOT migrated — state the reason} |

#### 5.3.2 {source_table_2} → {target_table_2}

{Same structure as above.}

### 5.4 Migration Tool Usage

Migration tool: `{migration_tool_path}`

**Dry-run (preview what would be migrated; nothing is written to the database)**:

```bash
{migration_tool_command} --mode=dry-run --source={source_dsn} --target={target_dsn}
```

**Actual migration**:

```bash
{migration_tool_command} --mode=migrate --source={source_dsn} --target={target_dsn} [--batch-size={n}]
```

**Verify only (no re-migration; only compares source and target for consistency)**:

```bash
{migration_tool_command} --mode=verify --source={source_dsn} --target={target_dsn}
```

**Command-line Parameters**:

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `--mode` | Y | — | `dry-run` / `migrate` / `verify` |
| `--source` | Y | — | {source database connection string} |
| `--target` | Y | — | {target database connection string} |
| `--batch-size` | N | {1000} | {number of records processed per batch} |

### 5.5 Known Limitations

| # | Limitation | Impact | Handling |
|---|------------|--------|----------|
| 1 | {Limitation1} | {scope of impact} | {manual backfill / resolved in a later version / accepted} |
| 2 | {Limitation2} | {scope of impact} | {handling} |

---

## 6 Appendix

### 6.1 Glossary

> Only terms **private to this component** belong here. The authoritative definitions of
> platform-wide terms live in [business_overview.md](../../business_overview.md) §4.1 and
> are not repeated here.

| Term | English / Code Name | Definition |
|------|--------------------|-----------|
| {Term1} | `{term_1}` | {definition} |
| {Term2} | `{term_2}` | {definition} |

### 6.2 Enum Definitions

> **THE AUTHORITATIVE DEFINITION LIVES IN**
> [{component_code_name}_tech_design.md](./{component_code_name}_tech_design.md) §2.{n}
> **Enum Definitions**. This section gives the **business explanation** only: what each
> enum value means to the business and under what circumstances it is reached. The
> technical type, storage form, and default value are governed by tech_design.
>
> If an enum's authoritative definition lives in another module, the authoritative file
> path must be stated here, e.g.: the authoritative definition of `{EEnumName}` lives in
> `{other_module}`, see
> [{other_module}_tech_design.md](../{other_module}/{other_module}_tech_design.md) §2.{n}.

#### 6.2.1 {EEnumName1} ({EnumDisplayName1})

{One sentence on what this enum expresses.}

| Value | Business Meaning | Used In | Condition for Reaching This Value |
|-------|------------------|---------|-----------------------------------|
| `{VALUE_1}` | {meaning} | {§3.1 {ObjectName1}.{attr}} | {when this value applies} |
| `{VALUE_2}` | {meaning} | {§3.1 {ObjectName1}.{attr}} | {condition} |
| `{VALUE_3}` | {meaning} | {§3.2 {ObjectName2}.{attr}} | {condition} |

**State Transitions** (state enums only):

| From | To | Trigger Condition |
|------|----|-------------------|
| `{VALUE_1}` | `{VALUE_2}` | {condition} |
| `{VALUE_2}` | `{VALUE_3}` | {condition} |

#### 6.2.2 {EEnumName2} ({EnumDisplayName2})

{Same structure as above.}

### 6.3 Scoring Standards

> **OPTIONAL SECTION**: Keep this section only when the component has fields scored
> manually or algorithmically (bias, credibility, priority score, and the like).
> Components with no scored fields should delete this section.

#### 6.3.1 {score_field_1} ({ScoreFieldDisplayName1})

| Item | Content |
|------|---------|
| **Value range** | {e.g. integer from -10 to +10} |
| **Default / unprocessed value** | {e.g. {value}, meaning not yet scored} |
| **Scored by** | {filled manually / computed automatically by {component} / inherited from {upstream object}} |
| **Used by** | {which business processes read this score} |

| Value | Meaning | Scoring Criteria |
|-------|---------|------------------|
| {value range 1} | {meaning} | {what kind of content earns this score} |
| {value range 2} | {meaning} | {scoring criteria} |
| {value range 3} | {meaning} | {scoring criteria} |

#### 6.3.2 {score_field_2} ({ScoreFieldDisplayName2})

{Same structure as above.}

> **CONSISTENCY REQUIREMENT**: The "default / unprocessed value" in this section must
> match the default value defined for the corresponding field in tech_design exactly.
> This has historically been a hotspot for documentation conflicts (the business document
> says null/-1 while the technical document says the value is inherited from the upstream
> object). Cross-check it at every review.

---

## Change Log

| Version | Date | Changes |
|---------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {description of change} |
| {x.y.z} | {YYYY-MM-DD} | {initial version} |
