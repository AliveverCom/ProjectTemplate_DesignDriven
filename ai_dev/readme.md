# AI Development Process Documentation Templates (ai_dev)

This directory is a collection of templates for **AI development process documentation**
(`ai_dev_history/`). The numbered directories represent **stages of the AI development
lifecycle**; when materialized, they are created under `ai_dev_history/` using directories with
the same numbers/names.

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Directory Structure and Scope Boundaries](#2-directory-structure-and-scope-boundaries)
- [3 Target Locations](#3-target-locations)
- [4 File Naming Rules](#4-file-naming-rules)
- [5 Unified Marker Sets](#5-unified-marker-sets)
- [6 Review Iteration Protocol](#6-review-iteration-protocol)
- [7 Template Inventory](#7-template-inventory)
- [8 Lifecycle Flow](#8-lifecycle-flow)

---

## 2 Directory Structure and Scope Boundaries

```
ai_dev/
├── readme.md                   # this file
├── _prompt_history.md          # → ai_dev_history/prompt_history.md (root directory, not inside a numbered subdirectory)
│
├── 01_DocReviewRefine/         # document-level review and refine
├── 02_DevPlanAndReport/        # dev plan / dev report / snapshot / AI handover / improve plan
├── 03_CodeReviewRefine/        # code-level review and refine
├── 04_DebugFix/                # debugging and defect fixes
└── 99_Prompts/                 # reusable prompt library
```

**Scope boundaries** (the sole criterion for deciding which directory a new document belongs in):

| Directory | Review/Work Target | What It Collects | What It Does NOT Collect |
|-----------|---------------------|-------------------|----------------------------|
| `01_DocReviewRefine` | **Design documents** (`.md` / `.mmd` / HTML design mockups) | Cross-document consistency review, technical design review, front-end/back-end design completeness review, HTML mockup vs. uiux doc review, general UI/UX pattern extraction | Source code review → `03` |
| `02_DevPlanAndReport` | **The delivery process of one version** | Dev Plan (overall / BE / FE), Dev Report, Snapshot, AI Handover, Improve Plan, naming-normalization and directory-reorganization plans | Review reports → `01`/`03` |
| `03_CodeReviewRefine` | **Source code** | Front-end/back-end code review, code-vs-design-document deviation review, refactoring suggestions | Investigation of a single defect → `04` |
| `04_DebugFix` | **A single defect / a batch of defects** | Defect investigation records, integration-debugging failure records, batch fix summary reports | Systematic code review → `03` |
| `99_Prompts` | **The prompts themselves** | Reusable standard prompt templates | Raw prompts that actually occurred → `prompt_history.md` |

`99_Prompts` is numbered 99 because it does not belong to any single stage of the lifecycle — it
is a toolbox that spans the whole process.

`prompt_history.md` sits in the **root** of `ai_dev_history/`, not inside any numbered
subdirectory — it is a single accumulating file that spans the entire development lifecycle, one
per component (and one for the platform), appended to and never recreated.

---

## 3 Target Locations

When the templates are copied into an actual project, the numbered directory names are **kept
exactly as they are**:

| Level | Target Path |
|-------|--------------|
| Platform-level | `docs/ai_dev_history/{numbered_dir}/` |
| Component-level | `docs/components/{component_code_name}/ai_dev_history/{numbered_dir}/` |

```
docs/components/{component_code_name}/ai_dev_history/
├── prompt_history.md
├── 01_DocReviewRefine/
├── 02_DevPlanAndReport/
├── 03_CodeReviewRefine/
├── 04_DebugFix/
└── 99_Prompts/                 # components typically don't create this; they use the platform-level one directly
```

---

## 4 File Naming Rules

The leading `_` in template file names is only a **template marker**; rename per the table below
when copying.

### 4.1 `02_DevPlanAndReport/` — Starts with the Version Number (Hard Rule)

**Every document in this directory is named starting with its version number, without
exception**:

```
v{version}_{DocType}[_{YYYYMMDD}].md
```

| Document Type | File Name Example |
|-----------------|----------------------|
| Overall version dev plan | `v0.3_DevPlan.md` |
| Backend dev plan | `v0.3_BE_DevPlan.md` |
| Frontend dev plan | `v0.3_FE_DevPlan.md` |
| Dev report / progress review | `v0.3_DevReport.md` |
| Snapshot | `v0.3_Snapshot.md` |
| AI-to-AI handover | `v0.3_Handover.md` |
| Improve plan | `v0.3_ImprovePlan.md` |
| Naming-normalization plan | `v0.3_RenamePlan.md` |
| Directory-reorganization plan | `v0.3_ReorganizePlan.md` |

- When there are multiple documents of the same type within one version, append a date suffix:
  `v0.3_Snapshot_20260320.md`.
- When one plan deliberately spans **two versions** — typically finishing off leftovers from the
  previous version while delivering the next one — chain the version numbers in ascending order:
  `v0.1_v0.2_FE_DevPlan.md`. The name still starts with a version number, so the file still sorts
  and groups correctly. Do not use this form to avoid deciding which version work belongs to;
  use it only when the plan genuinely delivers both.
- The version number is taken from the component's `{component}_version_plan.md`; cross-version
  reorganization-type plans use the **current version number at the time the plan is
  initiated**.
- Write the version number to however many segments are actually in use (`v0.3` / `v1.2.3` /
  `v1.2.3.4` are all valid).

### 4.2 Other Directories — Type + Date + Round

```
{type}_{YYYYMMDD}[-r{round}].md
```

| Directory | File Name Example |
|-----------|----------------------|
| `01_DocReviewRefine` | `docs_review_20260314-r10.md`<br>`tech_design_review_20260315_r3.md`<br>`design_completeness_review_20260308-r1.md`<br>`html_review_20260308-r2.md`<br>`uiux_pattern_summary_20260309-v1.md` |
| `03_CodeReviewRefine` | `FE_code_review_20260314-r1.md`<br>`BE_code_review_20260320-r2.md`<br>`code_review_20260314-r1.md` (combined front-end/back-end review) |
| `04_DebugFix` | `debug_FE_API_20260316-r1.md`<br>`debug_daemon_crash_20260402.md`<br>`fix_report_20260318-r1.md` |
| `99_Prompts` | `prompt_library.md` (single file, append-only) |
| Root directory | `prompt_history.md` (single file, append-only) |

> **Round numbers never reset across dates.** Example: `docs_review_20260310-r6.md` →
> `docs_review_20260314-r7.md`.
> The round number reflects **which round of the same review thread this is**, not how many
> times it happened that day.

---

## 5 Unified Marker Sets

| Category | Markers |
|----------|---------|
| **Severity** | 🔴 Critical / High · 🟠 High / Medium · 🟡 Medium · 🔵 Low |
| **Progress** | 🟢 Done · 🟡 Partial · 🔴 Not Implemented · ⚪ Out of Scope |
| **Verification** | ✅ Pass · ❌ Fail |
| **Reading Priority** | 🔴 Must Read · 🟠 Important · 🟡 Reference |
| **Outstanding Issue Tiers** | P1 Blocking · P2 Important · P3 Maintenance |

---

## 6 Review Iteration Protocol

This is the most central human-AI collaboration mechanism in this project; every report in
`01_DocReviewRefine` and `03_CodeReviewRefine` follows it.

### 6.1 The "Engineer's Response" Column Is the Core of the Protocol

The **last column of every issue table in every review report is fixed as "Engineer's
Response"**.
When an AI generates the report, this column **must always be left empty** — it must not be
filled in on the engineer's behalf, guessed at, or given placeholder text like "pending
confirmation". It is filled in manually by the engineer, row by row. Typical responses look
like:

- `Changed per your suggestion`
- `Not changing this. Reason: bikeshedding over variable names is not allowed — only the
  property's own naming needs to be self-consistent`
- `Changed per Option A`
- `Noted explicitly. These two fields will be periodically refreshed by another daemon in the
  future; until then, all reviews should ignore them`

### 6.2 The First Chapter of Round N's Report Is the "Previous-Round Verification" Table

These are not new issues. The verification result has only three possible values:

| Value | Meaning | Follow-up Action |
|-------|---------|---------------------|
| ✅ Done | The previous round's issue has been fixed per the response and verified | Does not appear in subsequent reports |
| 🟡 Partial | Partially fixed | Re-listed in this round's issue list, **keeping the original ID** |
| 🔴 Not Addressed | Not changed | Re-listed in this round's issue list, **keeping the original ID** |

### 6.3 Fixed Issues Are Not Restated, and New vs. Old Issues Are Not Distinguished

> The engineer does not care whether an issue is old or new — either way, it must be fully
> resolved or answered.

The "Note" blockquote at the top of the report must explicitly state this round's **filter
rule**, for example:

> This report only covers issues at 🔴 High and 🟡 Medium severity; all 🔵 Low-severity issues
> are ignored.
> This report only lists **newly discovered** issues not covered by R1/R2.

### 6.4 Issue IDs Are Globally Unique and Never Reused

There are two numbering schemes; only one is used within a given report:

| Scheme | Rule | Applies To |
|--------|------|------------|
| `R{round}-{seq}` | Recounted from 01 within each round; global uniqueness is guaranteed by the round number | Cross-document consistency review, HTML review |
| `{prefix}-{seq}` | Continuously increments across rounds (e.g. r2 ends at TD-18, r3 starts at TD-19) | Deep single-document review (`TD-` for technical design, `UP-` for a component-specific topic) |

Issue IDs get **written back into the body of the design document** as traceable annotations,
for example:

```markdown
> **⚠️ To Be Completed (R7-04)**: The complete UI/UX design spec for this page will be filled in
> by the engineer before development of the corresponding version.
```

Therefore, once an ID is issued it is frozen — it must not be reused or renumbered.

### 6.5 Architectural Disagreements Must Offer Options A/B with a Recommendation Marked

For issues that require the engineer to make a decision (rather than simply fix an error), the
report must be written as:

> **Option A** (recommended): {approach}, {trade-off}
> **Option B**: {approach}, {trade-off}

This lets the engineer close the loop with a single reply like "go with Option A", instead of
just describing the problem and leaving the engineer to figure out a solution.

---

## 7 Template Inventory

| Directory | Template File | Purpose |
|-----------|-----------------|---------|
| Root | `_prompt_history.md` | Raw prompt history archive (reverse-chronological, single accumulating file) |
| `01_DocReviewRefine` | `_docs_review.md` | Cross-document consistency review report |
| | `_tech_design_review.md` | Deep single-document review report |
| | `_design_completeness_review.md` | Front-end/back-end design document completeness review (whether development can proceed independently from the docs alone) |
| | `_html_review.md` | Page-by-page review of HTML mockups against the uiux document |
| | `_uiux_pattern_summary.md` | Proposal for extracting a general UI/UX pattern (whether to promote it to a platform-wide standard) |
| `02_DevPlanAndReport` | `_DevPlan.md` | Overall version dev plan |
| | `_BE_DevPlan.md` | Backend dev plan |
| | `_FE_DevPlan.md` | Frontend dev plan |
| | `_DevReport.md` | Dev report / progress review |
| | `_Snapshot.md` | Snapshot (a snapshot taken when a session is passively interrupted) |
| | `_Handover.md` | AI-to-AI handover (a task brief for an active handoff) |
| | `_ImprovePlan.md` | Improve plan (a checklist pending engineer confirmation or additions) |
| | `_RenamePlan.md` | Directory and file naming normalization execution plan |
| | `_ReorganizePlan.md` | Directory reorganization plan |
| `03_CodeReviewRefine` | `_CodeReview.md` | Front-end/back-end code review report |
| `04_DebugFix` | `_DebugRecord.md` | Investigation and fix record for a single defect |
| | `_FixReport.md` | Summary fix report for a batch of defects |
| `99_Prompts` | `_PromptLibrary.md` | Reusable standard prompt library |

---

## 8 Lifecycle Flow

```
       {component}_business_desc / tech_design / uiux written
                        │
                        ▼
        ① 01_DocReviewRefine   ──►  doc review → Engineer's Response → refine → review again
                        │            (until design_completeness_review judges it "independently developable")
                        ▼
        ② 02_DevPlanAndReport  ──►  v{x}_DevPlan / BE / FE
                        │            ├─ interrupted midway → v{x}_Snapshot
                        │            └─ handed off to someone else → v{x}_Handover
                        ▼
                     coding implementation
                        │
                        ▼
        ③ 03_CodeReviewRefine  ──►  code review → Engineer's Response → fix
                        │            (if judged to be "the doc's fault" → sent back to ①)
                        ▼
        ④ 04_DebugFix          ──►  debug_*.md investigation → fix_report_*.md summary
                        │
                        ▼
        ② 02_DevPlanAndReport  ──►  v{x}_DevReport filled in with delivery conclusions
                        │            └─ outstanding items → v{x}_ImprovePlan
                        ▼
                   proceed to the next version
```

Throughout: substantive prompts → `prompt_history.md`; prompts that prove reusable and get
distilled → `99_Prompts/_PromptLibrary.md`.

---

## Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-08-23 | Initial version: 5 numbered directories (01/02/03/04/99) + root-level `prompt_history.md`, 18 templates total |
