# Subagent Task-Book Standard and Templates

**Document Version**: 1.4.0

<!--
TEMPLATE NOTES (delete this block when using)
- Purpose: every task the Chief Architect dispatches to a subagent must follow the skeleton in §1;
  §2 holds one template per task type. The goal is to cap each instance's *context size × tool calls*
  (the dominant Opus cost) without lowering adversarial-review or implementation quality.
- Usage: copy to `docs/ai_dev/99_Prompts/_TaskBook.md` (keep the underscore — it is a standing
  standard, not a per-version document), replace `{curly brace}` placeholders, delete this block.
- Origin: distilled from a real project after measuring that 75% of spend went to Opus subagents
  re-reading a 560 KB design document and third-party engine sources on every review round.
-->

---

## 1 Task-Book Skeleton (every task book contains all rows)

| Section | Mandatory content | Notes |
|---|---|---|
| Role and task | Role name (`FirstName-JobTitle-Model`), task id (D/T/R/r), expected duration (≈10 min; terminated at 20 min) | One task book = one independently committable deliverable |
| **Read list** | Each entry as "file → section", **pointing at a volume** (e.g. `{component_code_name}_tech_design_runtime.md §3.4.1`, `tech_testing_verification_kpi.md K32b`), with the expected reading range | Never "read `{component_code_name}_tech_design.md`"; the main document is read only for its §1.0 index and the §2 classes actually needed |
| **Do-not-read / do-not-touch list** | Do not read: third-party engine or library sources (engine facts are cited by `EF-nn` id from `{component_code_name}_tech_design_engine_facts.md`), other volumes, `docs/ai_dev/` process documents (except the ruling table of a review report). Do not touch: any file outside the file-partition table | Review and document tasks **never open engine or repository sources to check a document**; implementation tasks read sources only for their "may modify" files and direct headers |
| **Shared knowledge pack** | The knowledge this task really needs is **pasted into the task book**: (1) **section-level read ranges**: documents by "volume + section number + section title" (e.g. "`{component_code_name}_tech_design_runtime.md` section 3.4.1 Main-loop phase order"), code by "file + function / symbol name" (e.g. "`kpi_kernel.py`, function `k25_pair_events`"); **never line numbers** - they go stale with every revision and cannot be traced across versions, whereas a section title or symbol is located with a single grep and stays traceable; (2) an existing sample to copy (file + function / table name); (3) the three to five design sentences the task relies on (with EF ids); (4) task-specific verification commands and expected values. The role files already carry a **resident knowledge pack** (build/test commands, toolchain, coding-standard digest, file map, known pitfalls); the task book adds only what is task-specific | Goal: no cold-start exploration. A measured scaffolding task spent 70 tool calls, half of them finding its way around; with the packs the cap is 35 |
| Tool-call budget | Caps: review ≤ 30, drafting/revision ≤ 25, **implementation ≤ 35**, acceptance run per tier list; report before exceeding; **the report states the actual tool-call count** (the Chief uses it to tune the packs) | Every context re-read is a cost |
| Deliverable and verification | Artefact path, verification commands (full paths), commit message template (with the attribution trailer lines) | One commit per task; `git add` only your own files |
| Report format | Word cap + mandatory items (hash, counts, unclear points handed to the Chief, never change the design yourself) | The report is the Chief's verification input, not a narrative |
| Discipline line | Every id carries a one-line meaning; versions written in full (`{component_code_name}/vA.B.C`); product name in full; binary paths written in full (`{binary_full_path}`) | As in `CLAUDE.md` |

**Concurrency**: the latest engineer instruction in `prompt_history.md` wins; count in-flight instances before dispatching.

---

## 2 Templates

### 2.1 Design adversarial review R1 (Opus, fresh instance)

```
You are Oscar-Architect-Opus, an independent reviewer who did not take part in drafting; read-only, no file changes, no git. Task: adversarial review R1 of the {component_code_name}/vA.B.C "<topic>" design. ≈15 min, one pass.
Read list: <volume §x.y, one per line>; upstream authority <business_overview §n / tech_ topic §n>.
Do not read: engine/library sources and repository sources (engine facts: cite EF ids from {component_code_name}_tech_design_engine_facts.md; facts missing from the register are listed as "pending verification" for the Chief — never verify them yourself); other volumes; docs/ai_dev/.
Requirements: §0 independent recomputation (closed forms / timetables / item counts); engine facts cited by EF id, checked against the design clauses; B/M/m grading; each finding = location / problem / evidence / impact / proposed options / empty "Chief ruling" row.
Report: docs/components/{component_code_name}/ai_dev/01_DocReviewRefine/vA.B.C_design_review_r1.md (Version v0.1.0, empty ruling table at the end).
Report back ≤ 250 words: B/M/m counts, one line per finding.
```

### 2.2 Design adversarial review R2+ (Opus, **re-use the R1 instance**, diff only)

```
(SendMessage to the R1 instance) Continuation: {component_code_name}/vA.B.C design review R<n>. The drafter has applied the R<n−1> Chief rulings (commit <hash>).
Read only: the changed regions of `git diff <prev>..<hash> -- <volume paths>` and their knock-on effects (list the sections); the ruling table at the end of the R<n−1> report (closure check).
Do not re-read the full text; do not read engine sources (cite EF ids).
Output: vA.B.C_design_review_r<n>.md with a closure table; when there is no B and M ≤ 3, state "may be closed after Chief verification". Report back ≤ 200 words.
```

### 2.3 Drafter applies rulings (Opus, re-use the drafting instance)

```
Continuation: apply the R<n> Chief rulings one by one. Read only: the ruling table at the end of the R<n> report (sole authority); the affected sections of the volumes being revised. Do not read other volumes or sources.
Versions: <file → target version> (header line only); one new row at the top of each change log (listing the finding ids and their landing sections); no process wording in the body, no links into docs/ai_dev/.
Every numeric change is recomputed by you with the derivation written down (a Python script is fine). No git.
Report back ≤ 250 words + table: finding id → landing → one-line change; anything that cannot be applied literally goes to the Chief.
```

### 2.4 Dev-plan independent review D0 (Opus, fresh instance)

```
Read only: the full DevPlan; the target row of {component_code_name}_version_plan.md; the design-volume sections cited by the D items (listed one by one); the src/ tree and build files (only to check the file partition — do not read implementations).
Focus: completeness (every "implemented in vA.B.C" design clause ↔ a D item), executability of the file partition (new / may-modify / do-not-touch, build-file and shared-header conflict surfaces), acyclic waves, prerequisites before the acceptance run, acceptance criteria.
Report vA.B.C_devplan_review_r1.md; report back ≤ 250 words.
```

### 2.5 Implementation task (Sonnet: backend / test development)

```
You are <Ben-X / Tina-X>-…-Sonnet, taking <D item / T sub-task> from DevPlan §3 (≈10 min, commit after each T).
May modify / create: <the two columns of DevPlan §3.2 for this D item, verbatim>; do not touch: <third column, verbatim>; build files / shared headers are frozen (if applicable).
Read list: <volume §x.y with section title, one per line>; the existing implementation of the may-modify files and their direct headers. Do not read other volumes or engine sources (engine facts: EF ids from {component_code_name}_tech_design_engine_facts.md; implement against the adapter-layer contract in the interfaces volume).
Shared knowledge pack: <the three to five design sentences this task relies on, with EF ids>; <sample to copy: file + function / symbol name>; <task-specific verification commands and expected values>. Build/test commands, toolchain, coding-standard digest, file map and known pitfalls are in your role file - not repeated here.
Build target: <the single build target this instance may build>; if this is a parallel wave, do not build a shared build tree that another instance is also building — build only your own target, or wait for the Chief's single post-wave build.
If this task runs a smoke check or self-test: afterward, move any produced records out of `{kpi_results_dir}` to a temporary directory and restore `{kpi_history_file}` to its committed state; do not leave smoke/self-check output in the evidence directory.
Tool-call budget 35; report the actual count.
Verification: <build command + unit test / golden / script commands, full paths>.
Commit: <message template + attribution trailer>. Report back ≤ 150 words: hash, counts, deviations from the design (never change the design yourself).
```

### 2.6 Code review r1 (Opus) and r2 closure check (Sonnet when r1 has no Blocker)

```
r1: read only the files touched by `git diff <base>..<head>` + the matching volume sections (listed) + the coding standard; never read unrelated sources in full. Output a CR list (B/M/m).
r2 (Sonnet when r1 has no B): verify each CR item fixed against the fix commits' diff, build and tests pass; read only the files involved in the fixes; output a closure table. If r1 had a B, r2 stays Opus and reviews only the fix diff.
```

### 2.7 Acceptance run (Sonnet: test execution)

```
The build is done by the Chief before dispatch and its SHA-256 recorded; nobody rebuilds during the acceptance run. Read only: DevPlan §4/§5 and the build/run commands in the build volume; the expected values in the KPI volume (only the keys to be checked).
Artefacts: KPI result files, per-tier execution records; report per-tier counts and the exact value of any expected FAIL (must match bit-for-bit).
```

---

## 3 Chief-side checklist (before dispatching)

1. Every read-list entry is **volume-level** with a section; no whole-file pointers.
2. The do-not-read list explicitly names engine sources / other volumes / docs/ai_dev.
3. In-flight instances ≤ the current concurrency limit; C++ tasks in the same wave touch disjoint file sets.
4. From R2 on, reviews resume the existing instance via SendMessage; every engine fact has an EF id, missing ones get a one-off verification task first.
5. Report word cap and mandatory items are written (incl. the tool-call count); the commit trailer lines are given.
6. The task book has a "Shared knowledge pack" section (section-level read ranges - section number + title, or function name, never line numbers - sample location, design sentences, task-specific verification); the role files' resident knowledge packs are kept current by the Chief (every change committed).
7. No task that modifies repository files is dispatched while an acceptance/evidence run is in progress; drafting that must proceed in parallel is stashed or moved to a separate worktree, and any formatting-fix pass is closed before the frozen build used as evidence.
8. Smoke/first-run task books point at the production KPI/orchestration functions (`<kpi_script>`), never an ad-hoc parallel script; KPI wiring gets an independent review scoped to its actual changes.
9. Each implementation instance in a parallel wave is told the single build target it may build (or that the Chief builds once after all of the wave's commits land) — no shared build tree touched by two instances concurrently.
10. Any task that runs a smoke check or self-test states that produced records must be moved out of `{kpi_results_dir}` and that `{kpi_history_file}` must be restored to its committed state.

---

## Change Log

| Version | Date | Author | Change |
|---|---|---|---|
| 1.4.0 | 2026-09-12 | {Author} | Implementation template and Chief-side checklist gain build-target isolation for parallel waves (no shared build tree) and smoke/self-check artefact isolation from the KPI evidence directory (`{kpi_results_dir}` / `{kpi_history_file}`) |
| 1.3.0 | 2026-09-11 | {Author} | Chief-side checklist gains two items: no file-modifying dispatch during an acceptance/evidence run; smoke/first-run task books use the production KPI functions, not ad-hoc scripts |
| 1.2.0 | 2026-09-06 | {Author} | Read-range anchors are section number + title (documents) or function / symbol name (code), never line numbers; role files gain a "How to locate" rule (grep the heading, then read only that range) |
| 1.1.0 | 2026-09-06 | {Author} | Added the "Shared knowledge pack" section (line-level read ranges, sample to copy, design sentences, task-specific verification); implementation tool-call cap 60 -> 35; reports state the tool-call count; role files carry resident knowledge packs |
| 1.0.0 | 2026-09-06 | {Author} | Created: task-book skeleton (read / do-not-read lists, tool-call budget, report format), seven templates (review R1 / R2+ instance re-use, drafter revision, D0, implementation, code review r1/r2, acceptance run), Chief-side checklist |
