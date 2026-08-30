<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] Component-level sub-module / daemon / executable program design document.

[Storage Location] docs/components/{component_code_name}/sub_{sub_module_code_name}/{sub_module_code_name}_design.md
  - Each sub-module has its own directory, with the directory name prefixed with `sub_`, e.g.:
      docs/components/material_collector/sub_unified_feed_cltr/unified_feed_cltr_design.md
      docs/components/material_collector/sub_whole_site_cltr/whole_site_cltr_design.md
  - The filename replaces the leading `_` with the sub-module code name.

[Why This Layer Is Needed]
  - The component's `{component}_tech_design.md` §3 "Software Program Descriptions" gives each
    daemon / executable program only a short overview section (responsibilities + applicable input + a
    one-line description of how it runs).
  - A single daemon's main loop, retry rules, concurrency model, and configuration parameters often run to
    hundreds of lines of detail; putting it all in the main document would bloat tech_design to the point of
    being unmaintainable (it has actually reached 1500+ lines in real projects).
  - So the details are split out into this file, and the main document keeps only an overview plus a link to
    this file.

[Boundary with Other Documents]
  - §1 Business Description: **excerpted** from the corresponding section of `{component}_business_desc.md`;
    must not rewrite the business semantics on its own. The section must open with
    `> This section is excerpted from {component}_business_desc.md §x.x`, to make consistency checks easy during review.
  - The **authoritative definition** of class definitions, table structures, and REST endpoints lives in
    `{component}_tech_design.md`; this document only states "which columns of which table are read, and which
    table is written," without repeating the DDL or field types.
  - Global conventions (log levels, naming, injection protection) are referenced via `technical_overview.md §4.x`
    and not redefined here.
-->

# {SubModuleName} ({SubModuleNameLocal}) Design

**Document Version**: {x.y.z}
**Parent Component**: [{component_code_name}]({component_code_name}_tech_design.md) §3.{n}

---

## 1 Business Description

> This section is excerpted from [{component_code_name}_business_desc.md](../{component_code_name}_business_desc.md) §{x}.{y}.
> If the business description changes, update business_desc first, then sync this section; do not modify only this section.

### 1.1 {BusinessProcessName}

| Item | Detail |
|------|--------|
| **Actor** | {who performs it, e.g. "{SubModuleNameLocal} (daemon process)"} |
| **Action** | {what it does, e.g. "periodically fetches content from {SomeType} sources"} |
| **Input Data** | {input data/resources, e.g. "source URLs and scraping configuration"} |
| **Output/Result** | {output, e.g. "{BusinessObject} records + {Task} execution records"} |

{One to three paragraphs of elaboration. If this business process has multiple implementation modes, use a bullet list to describe the differences between modes:}

- **{ModeOne}** (`{mode_a_code_name}`): {describe how this mode operates, its applicable scenarios, pros and cons}
- **{ModeTwo}** (`{mode_b_code_name}`): {description}

{Additional description of key business conventions, e.g. deduplication rules, idempotency rules, data retention policy:}
{Example: "No deduplication by {Field} is performed when writing {BusinessObject} records: the same resource may be
scraped multiple times for various reasons, and this is later handled by {DownstreamComponent} filtering by time range."}

### 1.2 {TaskManagementProcess / SecondRelatedBusinessProcess}

| Item | Detail |
|------|--------|
| **Actor** | {who performs it} |
| **Action** | {what it does} |
| **Input Data** | {input} |
| **Output/Result** | {output} |

{Description.}

---

## 2 Technical Design

### 2.1 Program Responsibilities and Applicable Input

| Item | Content |
|------|------|
| **Program Name** | `{sub_module_code_name}` |
| **Executable** | `{binary_name}` (source: `src/backend/{component_code_name}/cmd/{sub_module_code_name}/`) |
| **Program Form** | {long-running daemon / scheduled task / one-off CLI tool} |
| **Responsibility** | {one-line responsibility} |
| **Applicable Input** | {what kind of input it processes, e.g. "sources with `media_type = {ENUM_A}` and `trigger_strategy = {ENUM_B}`"} |
| **Loading Condition** | {filter condition for loading work items from the database, e.g. "`is_active = true` and `deleted_at IS NULL`"} |
| **Instance Count** | {single instance / one child process per work item / horizontally scalable to N} |

> **The loading condition must be stated explicitly**: each time the daemon reloads the work item list, how it handles
> "newly deactivated / newly soft-deleted items" must be stated explicitly (whether it skips them, or actively cleans up
> their pending tasks). A past review (R3-05) caused rework because different daemons described this inconsistently.

### 2.2 Operation Mode and Trigger Strategy

| Item | Content |
|------|------|
| **Startup Method** | {command line / systemd / container; example startup command} |
| **Trigger Strategy** | {fixed-interval polling / cron expression / event-driven / external API trigger} |
| **Trigger Interval** | {e.g. "the polling interval is the smallest `interval_ms` among all work items"} |
| **Work Item List Refresh** | {e.g. "reloaded from the database every {n} cycles or every {m} minutes, to pick up configuration changes promptly"} |
| **Graceful Shutdown** | {behavior on receiving SIGTERM: finish the current task then exit / interrupt immediately and mark task status} |
| **Heartbeat** | {how it exposes "I'm still alive" externally: writes `{field}` in {some table} / an HTTP health-check endpoint} |

**Startup Command Example**:

```bash
# Normal run
{binary_name} --config {config_path}

# Debug run for a single work item
{binary_name} --config {config_path} --{work_item_flag} {value} --once
```

### 2.3 Main Loop Steps

| # | Step | Description | Behavior on Failure |
|---|------|------|-----------|
| 1 | {Load configuration} | {Description} | {Exit and output an ERROR log} |
| 2 | {Connect to database} | {Description} | {Exit after retrying {n} times} |
| 3 | {Load work item list} | {Query `{table}` per the loading condition in §2.1} | {Keep the previous cycle's list, log WARN} |
| 4 | {Select due work items} | {Compare `last_polled` against `interval_ms`, skip items not yet due} | — |
| 5 | {Create task record} | {Write a record with `exe_status = PLANNED` to `{table}_plan`} | {Skip this item, log ERROR} |
| 6 | {Execute scraping / processing} | {Describe the core processing logic} | {See §2.5 Failure and Retry Rules} |
| 7 | {Parse and write results} | {Batch write to `{table}_mrecord`} | {See §2.5} |
| 8 | {Roll forward task record} | {Move the `_plan` record into `{table}_done` and set the terminal `exe_status`} | {Log ERROR, compensate next cycle} |
| 9 | {Update `last_polled` / statistics fields} | {Description} | {Log WARN} |
| 10 | {Cleanup} | {Clean up leftover `PLANNED` tasks from deactivated/deleted work items, setting them to `CANCELED`} | {Log WARN} |
| 11 | {Sleep until next cycle} | {Description} | — |

> **The step numbers are this file's core value**: development plans (ai_dev/03_DevPlan) split work directly into
> phases by these step numbers, and reviews also locate issues by step number. Once published, steps should be
> appended to, not renumbered, wherever possible.

### 2.4 Data Read/Write

**Tables Read**:

| # | Table | Columns Read | Filter Condition | Purpose |
|---|---|-------|---------|------|
| 1 | `{table_a}` | `{col1}`, `{col2}`, `{col3}` | `is_active = true AND deleted_at IS NULL` | {Load work items} |
| 2 | `{table_b}_done` | `{col}` | `exe_name = '{sub_module_code_name}' ORDER BY {col} DESC LIMIT 1` | {Infer the last execution time} |

**Tables Written**:

| # | Table | Write Mode | Columns Written | Description |
|---|---|---------|-------|------|
| 1 | `{table}_plan` | INSERT | `{cols}` | {Create a pending task} |
| 2 | `{table}_plan` | UPDATE | `exe_status`, `{cols}` | {Update task status} |
| 3 | `{table}_done` | INSERT | `{cols}` | {Roll forward a completed task} |
| 4 | `{table}_mrecord` | Batch INSERT | `{cols}` | {Write scraped results, batch size {n}} |
| 5 | `{table_a}` | UPDATE | `{col}` | {Write back statistics fields} |

**Dynamic Table Handling**:

| Scenario | Handling |
|------|---------|
| Target shard table does not exist | {Created dynamically by this program via `CREATE TABLE IF NOT EXISTS` on first execution; template in tech_design §{n}} |
| Table name comes from user input | {`code_name` must first be validated against the whitelist regex `^[a-z][a-z0-9_]*$` before being concatenated into the table name; see technical_overview.md §4.4} |

> The **authoritative definition** of table structures and column types lives in
> [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) §{n}; this section does not repeat the DDL.

### 2.5 Failure and Retry Rules

| # | Failure Scenario | Retry Strategy | Final Failure Handling | Status Marking |
|---|---------|---------|-------------|---------|
| 1 | {Network timeout} | {Retry immediately within the process {n} times, with {backoff strategy} interval} | {Log ERROR, skip this item} | `exe_status = {FAILED}` |
| 2 | {Target returns 4xx} | {No retry} | {Log WARN, skip} | `exe_status = {FAILED}` |
| 3 | {Parsing failure} | {No retry} | {Log ERROR, keep the raw content in {field}} | `exe_status = {FAILED}` |
| 4 | {Database write failure} | {Retry {n} times} | {Log ERROR, abort this cycle} | `exe_status = {ERROR}` |
| 5 | {Process crash} | {Automatically restarted by the {parent process / systemd}} | {After restart, orphaned tasks are cleaned up by §2.3 step 10} | — |

> **Retries happen only within the process, and never involve resetting state across cycles** (this convention originates
> from tech_design review TD-14). If cross-cycle compensation is needed, a compensation step must be explicitly added to
> the §2.3 main loop — it must not rely on implicit behavior.

### 2.6 Concurrency and Resource Limits

| Item | Convention |
|------|------|
| **Concurrency Model** | {single serial goroutine/coroutine / one goroutine per work item, max concurrency {n} / one child process per work item} |
| **Max Concurrency** | {n} (config item `{cfg_key}`) |
| **Single Task Timeout** | {n} seconds (config item `{cfg_key}`) |
| **Memory Limit** | {description, or "no explicit limit"} |
| **Outbound Request Rate** | {minimum request interval of {n} ms per target site, to avoid being blocked} |
| **Database Connection Pool** | {max connections {n}, see technical_overview.md §4.2} |

**Child Process Management** (needed only for the "one child process per work item" mode):

| Scenario | Main Process Behavior |
|------|-----------|
| A new work item is discovered | {Starts a new child process} |
| A work item is deactivated / soft-deleted | {Kills the corresponding child process} |
| A child process crashes | {Automatically restarts after detecting a non-zero exit code, up to {n} times} |
| The main process exits | {Kills all child processes first, then exits} |

### 2.7 Configuration Parameters

| # | Config Item | Type | Default | Environment Variable | Description |
|---|-------|------|-------|---------|------|
| 1 | `{cfg_key_1}` | {int} | {5000} | `{ENV_VAR_1}` | {Polling interval (ms)} |
| 2 | `{cfg_key_2}` | {int} | {3} | `{ENV_VAR_2}` | {Number of failure retries} |
| 3 | `{cfg_key_3}` | {int} | {30} | `{ENV_VAR_3}` | {Single task timeout (seconds)} |
| 4 | `{cfg_key_4}` | {string} | {""} | `{ENV_VAR_4}` | {Description} |
| 5 | `{cfg_key_5}` | {bool} | {false} | `{ENV_VAR_5}` | {Description} |

> Config item naming follows the config property prefix rules in [naming_convention.md](../../../naming_convention.md) §3.4.
> Common configuration such as database connections is not listed here; see the environment configuration section of the
> component's tech_design.

### 2.8 Logging and Monitoring

**Logging**:

| Level | Trigger Scenario | Required Fields |
|------|---------|---------|
| INFO | {Start/end of each cycle, each work item finishes processing} | `{exe_name}`, `{work_item}`, `{duration_ms}`, `{count}` |
| WARN | {A single work item is skipped, a retry succeeds} | `{exe_name}`, `{work_item}`, `{reason}` |
| ERROR | {A single work item ultimately fails, a database error} | `{exe_name}`, `{work_item}`, `{error}`, `{stack}` |
| DEBUG | {Per-record parsing detail} | {Field} |

> Log format and `trace_id` propagation rules follow [technical_overview.md §4.5](../../../technical_overview.md).

**Monitoring**:

| # | Monitoring Item | Data Source | Display Location |
|---|-------|---------|---------|
| 1 | Running status (RUNNING / STOPPED / UNKNOWN) | {Inferred from the timestamp of the latest record in `{table}_done` where `exe_name = '{sub_module_code_name}'`, threshold {n} minutes} | {Component uiux P{nn} monitoring dashboard} |
| 2 | Recent error list | {The most recent {n} records in `{table}_done` with `exe_status = {FAILED}`} | Same as above |
| 3 | {Processing volume statistics} | {Description} | Same as above |

### 2.9 Known Limitations

| # | Limitation | Impact | Planned Fix Version |
|---|------|------|-------------|
| 1 | {e.g. "the heartbeat uses a fixed {n}-minute threshold and cannot distinguish 'idle, no tasks' from 'process hung'"} | {False STOPPED reports} | {v0.x / not currently planned} |
| 2 | {e.g. "no conflict detection is done across work items; duplicate records are produced when the same target is scraped by multiple work items"} | {Downstream must deduplicate} | {See the deduplication convention in business_desc} |
| 3 | {Limitation} | {Impact} | {Version} |

> Known limitations must be listed explicitly. Past experience (TD-15) shows that unstated limitations are repeatedly
> raised during review; once stated, they are considered known and accepted by the engineer.

---

## 3 Appendix

### 3.1 Glossary

| Term | English / Code Name | Definition |
|------|--------------|------|
| {Term1} | `{term_1}` | {Definition} |

> Must be consistent with any same-named term at the component or platform level; only list terms private to this sub-module here.

### 3.2 Related Documents

| # | Document | Related Section | Description |
|---|------|---------|------|
| 1 | [{component_code_name}_business_desc.md](../{component_code_name}_business_desc.md) | §{x}.{y} | Source of this document's §1 |
| 2 | [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) | §2 Class Definitions / §3.{n} Program Overview / §4 Database Definitions | Authoritative definitions of classes and tables |
| 3 | [{component_code_name}_version_plan.md](../{component_code_name}_version_plan.md) | §{n} v0.{x} | Version scope this sub-module belongs to |
| 4 | [technical_overview.md](../../../technical_overview.md) | §4.2 / §4.4 / §4.5 | Global database, backend, and logging conventions |

---

## Change Log

| Version | Date | Changes |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {Description of the change; if it originated from a review, note the item ID} |
