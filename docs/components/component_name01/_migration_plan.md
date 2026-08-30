<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] Component-level legacy-system data migration plan.

[Storage Location] docs/components/{component_code_name}/old_projects_migration/{old_system_code_name}_migration_plan.md
  - Each component groups all "legacy project migration" documents together under the
    `old_projects_migration/` directory, separate from that component's regular design
    documents, e.g.:
      docs/components/material_collector/old_projects_migration/data_migration_plan.md
      docs/components/material_collector/old_projects_migration/main_DailyNewsRss_tech_design.md
  - In the file name, replace the leading `_` with the legacy system's code name (or just use
    `data_migration_plan.md` directly).
  - If the legacy system itself also needs a technical design kept on file, create a separate
    `{old_system}_tech_design.md` in the same directory.

[Boundary with Other Documents]
  - The **authoritative definition** of the target-side table structure, column types, and
    constraints lives in `{component}_tech_design.md` §Database Definitions;
    this document only records "source column → target column" mapping and conversion rules,
    and does not repeat the DDL.
  - Which version the migration belongs to and whether it blocks other features is defined by
    `{component}_version_plan.md`; this document only references it.
  - Business-semantic discrepancies discovered during migration (e.g. a field's semantics in the
    legacy system not matching the new business object) must be written back into the
    "Data Migration" section of `{component}_business_desc.md`; this document only provides the
    technical explanation.

[Key Points to Fill In]
  - §4 Data Mapping is the core of this document and must be filled in exhaustively, table by
    table and column by column; the "Conversion Rule" column must not be left blank or contain a
    vague description other than "direct mapping".
  - §5 must give the complete, copy-pasteable commands for all three run modes: dry-run / migrate
    / verify-only, so the engineer can run them as-is.
  - §9 Known Limitations must explicitly state "which data cannot be migrated, why, and how it
    will be compensated for".
-->

# {OldSystemName} → {NewComponentName} Data Migration Plan

| Item | Content |
|------|---------|
| **Document Name** | Data Migration Plan from {OldSystemName} to {NewComponentName} ({component_code_name}) |
| **Document Version** | {x.y.z} |
| **Creation Date** | {YYYY-MM-DD} |
| **Target Version** | `v0.{n}`, see [{component_code_name}_version_plan.md](../{component_code_name}_version_plan.md) §{n} |
| **Source System** | {OldSystemName} ({SourceDBType}, code path `{old_source_path}`) |
| **Target System** | {component_code_name} ({TargetDBType}, database name `{target_db_name}`) |
| **Migration Method** | {One-time full migration / Full + incremental catch-up / Batched migration} |
| **Executor** | {AI / Engineer} |
| **Status** | {🟡 In Progress / 🟢 Done / ⚪ Not Started} |

---

## 1 Migration Background and Goals

### 1.1 Background

{Explain the legacy system's origin, current operating state, and why it is being migrated.
For example: "{OldSystemName} is the {Language} monolithic program used before this platform
launched, and has accumulated {n} thousand historical {BusinessObject} records. The new
{component_code_name} component on the new platform has taken over all of that system's business
responsibilities, and the historical data must be migrated into the new database to preserve
data continuity for {DownstreamComponent}."}

### 1.2 Goals

| # | Goal | Acceptance Criteria |
|---|------|---------------------|
| 1 | {Migrate all historical data from {SourceTable}} | {Target table row count = source table valid row count, zero discrepancy} |
| 2 | {Field semantics converted correctly} | {A sample of {n} records passes field-by-field comparison} |
| 3 | {Migration process is repeatable} | {Repeated execution does not produce duplicate data (idempotent)} |
| 4 | {Legacy system can be shut down and decommissioned} | {Once migration completes and verification passes, the legacy system no longer writes new data} |

### 1.3 Non-Goals (Not Done This Time)

| # | Not Doing | Reason |
|---|-----------|--------|
| 1 | {Migrate {SomeTable}} | {This table's data is stale / no longer used by the new business} |
| 2 | {Dual writes and real-time sync} | {The legacy system is shut down right after migration, so dual writes are unnecessary} |
| 3 | {Semantic cleansing and backfilling of historical data} | {Belongs to {DownstreamComponent}'s preprocessing pipeline} |

---

## 2 Source System Overview

### 2.1 Source System Basic Information

| Item | Content |
|------|---------|
| **System Name** | {OldSystemName} |
| **Implementation Language** | {Python / C# / PHP ...} |
| **Source Code Location** | `{old_source_path}` |
| **Database Type** | {MySQL 5.7 / SQLite / MongoDB ...} |
| **Database Name** | `{old_db_name}` |
| **Character Set / Collation** | {utf8mb4 / utf8mb4_general_ci} |
| **Data Volume** | {approx. n thousand rows, m GB} |
| **Data Time Range** | {YYYY-MM ~ YYYY-MM} |
| **Access Method** | {source of the connection string, whether a read-only account is used} |

### 2.2 Source Table Inventory

| # | Source Table | Row Count (approx.) | Migrate? | Target Table | Notes |
|---|--------------|----------------------|----------|---------------|-------|
| 1 | `{old_table_1}` | {100,000} | ✅ Yes | `{new_table_1}` | {notes} |
| 2 | `{old_table_2}` | {5,000} | ✅ Yes | `{new_table_2}` | {notes} |
| 3 | `{old_table_3}` | {2,000,000} | ✅ Yes | `{new_table_3_template}` (sharded by `{key}`) | {one-to-many sharding, see §4.3} |
| 4 | `{old_table_4}` | {300} | ❌ No | — | {stale, not migrated} |
| 5 | `{old_table_5}` | {50} | ❌ No | — | {replaced by a config file in the new system} |

### 2.3 Source System Technical Design Record

{If the legacy system lacks documentation, a concise technical design record should be produced
first, to make it easier to investigate historical data issues later:}

> See [{old_system_code_name}_tech_design.md](./{old_system_code_name}_tech_design.md) for details.

---

## 3 Target System Overview

### 3.1 Target Database Basic Information

| Item | Content |
|------|---------|
| **Component** | {component_code_name} |
| **Database Type** | {PostgreSQL 17.x} |
| **Database Name** | `{target_db_name}` |
| **Schema** | `{schema_name}` |
| **DB Creation Script** | `src/backend/{component_code_name}/sql/init_databases.sql` |
| **Migration Script Directory** | `src/backend/{component_code_name}/sql/migrations/` |

### 3.2 Target Table Inventory

| # | Target Table | Already Exists? | Creation Method | Notes |
|---|--------------|------------------|------------------|-------|
| 1 | `{new_table_1}` | ✅ Created in v0.3 | Migration script | {notes} |
| 2 | `{new_table_2}` | ✅ Created in v0.3 | Migration script | {notes} |
| 3 | `{new_table_3_template}` | ❌ Created dynamically | {created one by one via `CREATE TABLE IF NOT EXISTS` by the migration tool, per `{key}`} | {see the shard-table template in tech_design §{n}} |

> The **authoritative definition** of the table structure is in
> [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) §{n};
> this document does not repeat the DDL.

### 3.3 Migration Preconditions

| # | Precondition | How to Check | Status |
|---|---------------|---------------|--------|
| 1 | Target database and tables created (done in v0.3) | {run `\dt` to confirm the tables exist} | {🟢 / ⚪} |
| 2 | Source database read-only account is reachable | {run `SELECT 1`} | {🟢 / ⚪} |
| 3 | Target database has sufficient disk space (≥ {n} GB) | {`df -h`} | {🟢 / ⚪} |
| 4 | Current target database data has been backed up | {`pg_dump` output path} | {🟢 / ⚪} |
| 5 | Legacy system has stopped writing | {confirm the daemon has stopped} | {🟢 / ⚪} |

---

## 4 Data Mapping

> **This chapter is the sole basis for the migration tool's implementation.** Every column
> must have an explicit conversion rule; "TBD" or blank entries are not allowed. If a column
> genuinely has no corresponding relationship, write "not migrated, target column uses default
> value {x}".

### 4.1 `{old_table_1}` → `{new_table_1}`

| # | Source Column | Source Type | Target Column | Target Type | Conversion Rule | Notes |
|---|----------------|--------------|-----------------|---------------|------------------|-------|
| 1 | `{old_col_id}` | `INT` | `{new_col_id}` | `INT PK` | Direct mapping | {Whether to keep the original ID: keeping it preserves foreign-key relationships; if not kept, an ID mapping table must be built} |
| 2 | `{old_col_name}` | `VARCHAR(255)` | `{new_col_display_name}` | `TEXT NOT NULL UNIQUE` | Direct mapping after `TRIM()`; use `'{fallback}'` when empty | {source data has {n} empty values} |
| 3 | — | — | `{new_col_code_name}` | `TEXT NOT NULL UNIQUE` | **Source has no corresponding column**: generated as snake_case from `{new_col_display_name}` (lowercased, punctuation and spaces converted to `_`); append `_{seq}` on conflict | {Must satisfy `^[a-z][a-z0-9_]*$`, because it is used to construct shard table names} |
| 4 | `{old_col_type}` | `TINYINT` | `{new_col_media_type}` | `TEXT` | Enum value mapping, see §4.4 | {source uses numbers, target uses enum literals} |
| 5 | `{old_col_flag}` | `TINYINT(1)` | `{new_col_is_active}` | `BOOLEAN NOT NULL DEFAULT true` | `0 → false`, `1 → true`, `NULL → true` | |
| 6 | `{old_col_deleted}` | `TINYINT(1)` | `deleted_at` | `TIMESTAMPTZ NULL` | `0 → NULL`; `1 →` the value of `{old_col_updated}`, or the migration execution time if empty | **Soft-delete field semantic change**: legacy `is_deleted` boolean → new `deleted_at` timestamp (see technical_overview.md §4.2 Rule 6) |
| 7 | `{old_col_a}` / `{old_col_b}` / `{old_col_c}` | `INT` ×3 | `{new_col_json}` | `JSONB NOT NULL` | The three columns are merged into a JSON object: `{"a": {old_col_a}, "b": {old_col_b}, "c": {old_col_c}}`; use default value `{n}` when the source column is `NULL` | {structured field merge} |
| 8 | `{old_col_time}` | `DATETIME` | `created_at` | `TIMESTAMPTZ` | Interpreted using the source database's time zone `{TZ}`, then converted to UTC | **The time zone must be specified explicitly**, otherwise all values will be shifted uniformly |
| 9 | — | — | `updated_at` | `TIMESTAMPTZ` | Uniformly set to the value of `created_at` during migration | |
| 10 | `{old_col_unused}` | `TEXT` | — | — | **Not migrated**: {reason} | |

### 4.2 `{old_table_2}` → `{new_table_2}`

| # | Source Column | Source Type | Target Column | Target Type | Conversion Rule | Notes |
|---|----------------|--------------|-----------------|---------------|------------------|-------|
| 1 | `{old_col}` | `{type}` | `{new_col}` | `{type}` | {rule} | {notes} |

### 4.3 `{old_table_3}` → `{new_table_3_template}` (Sharded)

The source is a single large table; on the target side it is split by `{key_field}` into
multiple shard tables `{key_value}_{suffix}`.

| # | Source Column | Source Type | Target Column | Target Type | Conversion Rule | Notes |
|---|----------------|--------------|-----------------|---------------|------------------|-------|
| 1 | `{old_col_fk}` | `INT` | — | — | **Not written as a column**; used to determine the target shard table name: look up the `{new_col_code_name}` generated in §4.1 first, then concatenate as `{code_name}_{suffix}` | Shard key |
| 2 | `{old_col}` | `{type}` | `{new_col}` | `{type}` | {rule} | |

**Shard-Table Handling Rules**:

| # | Scenario | Handling |
|---|----------|----------|
| 1 | Target shard table does not exist | Create it from the template with `CREATE TABLE IF NOT EXISTS` (including indexes) |
| 2 | Shard table name is invalid | Skip that batch of data and record it in the error report; `code_name` must match `^[a-z][a-z0-9_]*$` |
| 3 | The source row's foreign key was not migrated in §4.1 | Skip it and count it under "orphan rows" |

### 4.4 Enum Value Mapping Table

| Source Value | Source Meaning | Target Enum Value | Notes |
|---------------|-----------------|---------------------|-------|
| `{0}` | {meaning} | `{ENUM_A}` | |
| `{1}` | {meaning} | `{ENUM_B}` | |
| `{2}` | {meaning} | `{ENUM_C}` | |
| Other / NULL | {unknown} | `{ENUM_DEFAULT}` | Counted in the warning statistics |

> The **authoritative definition** of the target enum is in
> [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) §{n} Enum Definitions.

### 4.5 Foreign Key Cross-Reference Table

{When the migration involves cross-table references, explain how the IDs correspond:}

| Source Table | Source Key | Target Table | Target Key | Correspondence Strategy |
|---------------|-------------|----------------|--------------|---------------------------|
| `{old_table_1}` | `{old_col_id}` | `{new_table_1}` | `{new_col_id}` | {Keep the original ID / create a new auto-increment ID and maintain an in-memory mapping table `map[old_id]new_id`} |

---

## 5 Migration Tool Design

### 5.1 Tool Basic Information

| Item | Content |
|------|---------|
| **Tool Name** | `{migration_tool_name}` |
| **Implementation Language** | {Go 1.25.x / Python 3.12} |
| **Source Code Directory** | `src/backend/{component_code_name}/cmd/{migration_tool_name}/` (or `{old_projects_migration_src_path}`) |
| **Dependencies** | {source DB driver, target DB driver, config library; versions per technical_overview.md §5} |
| **Artifact** | {executable `{binary}` / script entry point `main.py`} |

### 5.2 Directory Structure

```
{migration_tool_path}/
├── cmd/{migration_tool_name}/main.go   # entry point; parses args and mode
├── internal/
│   ├── source/          # source DB reads (read-only)
│   ├── transform/       # implementation of the §4 mapping and conversion rules
│   ├── sink/            # target DB writes (including dynamic shard-table creation)
│   ├── verify/          # §7 verification logic
│   └── report/          # migration report and error-detail output
├── config/
│   └── {config_file}    # source/target DB connection strings, batch size, concurrency
└── README.md
```

### 5.3 Run Modes

**Mode 1: Dry-run (preview, no writes to the target database)**

```bash
{migration_tool_name} \
  --config {config_path} \
  --mode dry-run \
  --tables {old_table_1},{old_table_2} \
  --report {report_dir}/dryrun_{YYYYMMDD}.md
```

- Fully executes reading and conversion, but **performs no writes whatsoever**.
- Output: the row count to be migrated, the list of shard tables to be created, conversion
  warnings (unknown enum values, empty-value fallbacks, orphan rows).

**Mode 2: Full Migration**

```bash
{migration_tool_name} \
  --config {config_path} \
  --mode migrate \
  --tables {old_table_1},{old_table_2},{old_table_3} \
  --batch-size {1000} \
  --report {report_dir}/migrate_{YYYYMMDD}.md
```

- Migrates in the step order given in §6; commits one transaction per batch of `{1000}` rows.
- Idempotency guarantee: {use `INSERT ... ON CONFLICT DO NOTHING` / clear the target table by
  `{key}` before migrating}.

**Mode 3: Verify Only (no re-migration)**

```bash
{migration_tool_name} \
  --config {config_path} \
  --mode verify \
  --sample-size {200} \
  --report {report_dir}/verify_{YYYYMMDD}.md
```

- Only performs the row-count comparison and sample comparison from §7; performs no writes.

### 5.4 Common Parameters

| Parameter | Type | Default | Notes |
|-----------|------|---------|-------|
| `--config` | string | — | Config file path (required) |
| `--mode` | enum | `dry-run` | `dry-run` / `migrate` / `verify` |
| `--tables` | string | all | Comma-separated whitelist of source tables |
| `--batch-size` | int | {1000} | Rows committed per batch |
| `--sample-size` | int | {200} | Number of records for sample verification |
| `--continue-on-error` | bool | `false` | Whether to continue when a single row fails |
| `--report` | string | stdout | Report output path |

---

## 6 Execution Steps

| # | Step | Command / Notes | Owner | Status |
|---|------|------------------|-------|--------|
| 1 | Back up the target database | `pg_dump {target_db_name} > {backup_path}` | {Executor} | {⚪} |
| 2 | Confirm the source database's read-only connectivity | {command} | {Executor} | {⚪} |
| 3 | Stop the legacy system from writing | {command / operational notes} | {Executor} | {⚪} |
| 4 | Run dry-run | See §5.3 Mode 1 | {Executor} | {⚪} |
| 5 | Review the dry-run report | {focus on: row counts, shard-table list, number of conversion warnings} | {Engineer} | {⚪} |
| 6 | Migrate `{old_table_1}` → `{new_table_1}` | See §5.3 Mode 2, `--tables {old_table_1}` | {Executor} | {⚪} |
| 7 | Migrate `{old_table_2}` → `{new_table_2}` | Same as above | {Executor} | {⚪} |
| 8 | Migrate `{old_table_3}` → shard tables | Same as above (depends on the `code_name` generated in step 6) | {Executor} | {⚪} |
| 9 | Run verification | See §5.3 Mode 3 | {Executor} | {⚪} |
| 10 | Review the verification report | {all comparison items must pass before proceeding to the next step} | {Engineer} | {⚪} |
| 11 | Update the component's `VERSION` and Change Log | {notes} | {Executor} | {⚪} |
| 12 | Decommission the legacy system | {notes} | {Engineer} | {⚪} |

> **Status markers**: 🟢 Done | 🟡 In Progress | 🔴 Failed | ⚪ Not Started
>
> Steps 6/7/8 have an **ordering dependency** (shard table names depend on the `code_name`
> generated by the main table); they must not be run in parallel or out of order.

---

## 7 Verification Plan

### 7.1 Row Count Comparison

| # | Comparison Item | Source-side Query | Target-side Query | Allowed Discrepancy |
|---|------------------|--------------------|---------------------|----------------------|
| 1 | `{old_table_1}` total row count | `SELECT COUNT(*) FROM {old_table_1} WHERE {ValidCondition}` | `SELECT COUNT(*) FROM {new_table_1}` | 0 |
| 2 | `{old_table_2}` total row count | {query} | {query} | 0 |
| 3 | `{old_table_3}` total row count | `SELECT COUNT(*) FROM {old_table_3}` | Sum of `COUNT(*)` across all shard tables | {0 / number of orphan rows} |
| 4 | Number of shard tables | `SELECT COUNT(DISTINCT {old_col_fk}) FROM {old_table_3}` | Number of shard tables actually created | 0 |

### 7.2 Sample Comparison

| # | Sampling Strategy | Sample Size | Comparison Content |
|---|--------------------|--------------|----------------------|
| 1 | Random sampling | {200} records | Column-by-column comparison against the expected value per the §4 mapping |
| 2 | Boundary sampling: earliest / latest records | {10} each | Focused verification of time-zone conversion for time fields |
| 3 | Special-value sampling: source is NULL / empty string / unknown enum value | {20} each | Verify fallback rules |
| 4 | Long-text / special-character records | {20} records | Verify character set and escaping |

### 7.3 Unique Key Conflict Handling

| # | Conflict Scenario | Handling |
|---|---------------------|----------|
| 1 | `{new_col_display_name}` duplicate | {append `_{seq}` to deduplicate, and record it in the report} |
| 2 | `{new_col_code_name}` duplicate after generation | {append `_{seq}`; since it determines the shard table name, uniqueness must be guaranteed} |
| 3 | Primary key duplicate (migration re-run) | {`ON CONFLICT DO NOTHING`, to guarantee idempotency} |

### 7.4 Verification Report Format

```
{report_dir}/verify_{YYYYMMDD}.md
├── 1 Row-count comparison results table (source / target / diff / conclusion ✅❌)
├── 2 Sample comparison results table (sample ID / mismatched column / source value / target value)
├── 3 Conversion warning summary (n unknown enum values, n empty-value fallbacks, n orphan rows)
└── 4 Conclusion (✅ Pass / ❌ Fail + list of blocking items)
```

---

## 8 Rollback Plan

| # | Rollback Scenario | Rollback Action | Data Loss |
|---|---------------------|-------------------|-----------|
| 1 | Issue found during the dry-run stage | No rollback needed (nothing was written) | None |
| 2 | Migration fails midway | {delete the rows written in this run by `{key}`; or restore the whole database from the step 1 backup} | None |
| 3 | Mapping error found after migration completes | {restore the step 1 backup → fix the §4 mapping → rerun the entire process} | New business data generated after the migration (which is why the system must be shut down for the duration of the migration) |
| 4 | Shard table created incorrectly | {`DROP TABLE` the incorrect shard table → fix the `code_name` generation rule → rerun step 8} | None |

**Example Rollback Commands**:

```bash
# Restore the whole database
psql -d {target_db_name} -f {backup_path}

# Roll back only the rows written by this migration run
psql -d {target_db_name} -c "DELETE FROM {new_table_1} WHERE {migration_marker_condition};"
```

> **Suggestion**: have the migration tool write a `{migration_marker_column}` for every row
> (e.g. `created_at = {migration batch time}` or a dedicated marker column), so that
> "rolling back only this migration run" stays possible, instead of only being able to restore
> the whole database.

---

## 9 Known Limitations and Outstanding Issues

| # | Limitation / Outstanding Issue | Impact | Handling | Status |
|---|----------------------------------|--------|----------|--------|
| 1 | {e.g. "About {n}% of historical data in the `{col}` column of source table `{old_table_3}` is missing and cannot be backfilled"} | {the target column uses the default value; downstream statistics are affected} | {explained in the Data Migration section of business_desc; handled as an empty value by {DownstreamComponent}} | {Accepted} |
| 2 | {e.g. "The source database is missing time-zone information; data from before {YYYY} may have a ±{n}-hour timestamp discrepancy"} | {affects the precision of date-based filtering} | {Accepted, no correction made} | {Accepted} |
| 3 | {e.g. "Migration of `{old_table_x}` has not yet been implemented"} | {this portion of the data is temporarily missing} | {planned to be completed in v0.{n}} | {🔴 Not Implemented} |
| 4 | {limitation} | {impact} | {handling} | {status} |

> Known limitations must be explicitly listed and confirmed by the engineer. Limitations that
> are not written down will keep being raised repeatedly in subsequent reviews under
> ai_dev/01_DocReviewRefine.
>
> If a limitation causes the actual migration scope to deviate from the version definition in
> [{component_code_name}_version_plan.md](../{component_code_name}_version_plan.md), the
> deviation must also be recorded in the version_plan's Change Log.

---

## Change Log

| Version | Date | Changes |
|---------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {change description} |
