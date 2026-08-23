<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Tier] AI Development Process Documents —— `02_DevPlanAndReport`

[What This Directory Holds] Dev plans, dev reports, saved-state snapshots, AI handovers, improve plans,
       naming normalization and directory reorganization plans. In short, the two document types
       "what is planned" and "what was actually delivered."

[Storage Location]
  - Platform level: docs/ai_dev_history/02_DevPlanAndReport/
  - Component level: docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/

[File Naming —— hard rule for this directory] Every document must start with its version number:
       `v{version}_{DocType}[_{YYYYMMDD}].md`
  - v0.3_DevPlan.md / v0.3_BE_DevPlan.md / v0.3_FE_DevPlan.md / v0.3_DevReport.md
  - v0.3_Snapshot.md / v0.3_Handover.md / v0.3_ImprovePlan.md
  - v0.3_RenamePlan.md / v0.3_ReorganizePlan.md
  - When multiple documents of the same type exist within one version, append a date suffix: v0.3_ReorganizePlan_20260326.md
  - The version number comes from {component}_version_plan.md; a cross-version reorganization plan like
    this one uses the **current version at the time it was initiated**

[This File's Purpose] A directory reorganization plan. When a directory (typically ai_dev_history/) has
       accumulated too many files flat in one level and become hard to search, establish subdirectories
       by document type and batch-archive into them.

[Difference From Other Documents in This Directory]
  - v{x}_RenamePlan.md   —— only renames **files/directories**, without changing their level.
  - This file (ReorganizePlan) —— only **moves locations** and creates subdirectories, without renaming
    files. The two are often used together, but should be split into two documents and executed in two
    steps to make rollback and Git rename detection easier.
  - v{x}_DevPlan.md —— changes functional code; unrelated to this file.

[Trigger Condition] File count in a single directory > 20 and there is a clear type grouping.

[Characteristic] This kind of plan is typically **written as it is executed** (status is filled back in
       as completed immediately after each step runs); the document itself is the execution record and
       does not need an engineer-response column.

[Template File Name] The leading `_` is only a template marker; rename the file per the naming rule above when copying it.
-->

# v{version} {TargetDirectory} Directory Reorganization Plan

**Document**: v{version}_ReorganizePlan[_{YYYYMMDD}].md
**Version**: v{version}
**Date**: {YYYY-MM-DD}
**Component**: {component} / {TargetDirectory}
**Target**: {Sort the historical documents under {TargetDirectory} into subdirectories by category to improve maintainability}
**Developer**: AI Agent
**Status**: {🟢 Completed / 🟡 In Progress}

---

## 1 Background and Motivation

{As `{TargetDirectory}/` accumulates documents through iterative development ({enumeration of document
 types}), all files sit flat at the same level, and the file count has reached {n}+, reducing
 findability and manageability. Subdirectories need to be established by document type.}

**Directory State Before Reorganization**:

| Item | Value |
|------|-----|
| Total file count | {n} |
| Directory levels | {all flat in the root directory} |
| Main document types | {type enumeration} |
| Earliest file date | {YYYY-MM-DD} |
| Latest file date | {YYYY-MM-DD} |

---

## 2 Target Directory Structure

> See `templates/ai_dev/readme.md` for the standard structure. Reorganization should always align with
> the standard structure; do not invent a new classification scheme.

```
{TargetDirectory}/                     # ai_dev_history/
├── prompt_history.md                  # Cumulative record of raw prompts (kept in the root directory)
├── 01_DocReviewRefine/                # Document-level review and refine
│   ├── docs_review_*.md               # Cross-document consistency review
│   ├── tech_design_review_*.md        # In-depth technical design review
│   ├── design_completeness_review_*.md # Design completeness review
│   ├── html_review_*.md               # HTML design mockup review
│   └── uiux_pattern_summary_*.md      # UI/UX common-pattern distillation
├── 02_DevPlanAndReport/               # Plans / reports / snapshots / handovers (file names start with the version number)
│   ├── v{x}_DevPlan.md                # Version dev plan
│   ├── v{x}_BE_DevPlan.md             # Backend dev plan
│   ├── v{x}_FE_DevPlan.md             # Frontend dev plan
│   ├── v{x}_DevReport.md              # Version dev report
│   ├── v{x}_Snapshot.md               # Saved state snapshot
│   ├── v{x}_Handover.md               # AI-to-AI handover
│   ├── v{x}_ImprovePlan.md            # Continued improvement plan
│   ├── v{x}_RenamePlan.md             # Naming normalization plan
│   └── v{x}_ReorganizePlan.md         # Directory reorganization plan (this file)
├── 03_CodeReviewRefine/               # Code-level review and refine
│   └── {FE|BE}_code_review_*.md
├── 04_DebugFix/                       # Debugging and defect fixes
│   ├── debug_*.md
│   └── fix_report_*.md
└── 99_Prompts/                        # Reusable prompt library
    └── prompt_library.md
```

**Design Principles**:

| # | Principle | Notes |
|---|------|------|
| 1 | {Only two levels} | {Do not create a third-level subdirectory, to avoid over-nesting} |
| 2 | {Categorize by development lifecycle stage} | {The numbered directories 01→99 correspond to document review → development → code review → debugging → prompts, not to a calendar month} |
| 3 | {prompt_history.md stays in the root directory} | {A single file that is continually appended to; it is the entry point document of this directory and is not filed into any numbered subdirectory} |
| 4 | {Numbered directory names stay consistent with templates/ai_dev/} | {The template directory and the storage directory share the same names, for easy cross-reference when fetching a template} |
| 5 | {File names inside 02 start with the version number} | {Makes it easy to cluster by version; `ls` sorts by version} |

---

## 3 Classification Rules

| Subdirectory | Match Rule | File Count |
|--------|---------|--------|
| `01_DocReviewRefine/` | {File name contains `docs_review`, `tech_design_review`, `design_completeness_review`, `html_review`, or `uiux_pattern_summary`} | {n} |
| `02_DevPlanAndReport/` | {File name contains `_dev_plan_`, `_implementation_`, `_progress_review_`, `_improvement_plan_`, `_rename_plan_`, or `_reorganize_plan_`} | {n} → {n} (including this file) |
| `03_CodeReviewRefine/` | {File name contains `FE_review`, `BE_review`, or `code_review`} | {n} |
| `04_DebugFix/` | {File name starts with `debug_`, or contains `fix_report`} | {n} |
| `99_Prompts/` | {`prompt_library.md` and other reusable prompt collections} | {n} |
| Kept in root | `prompt_history.md` {(formerly `{component}_raw_prompts.md`)} | {1} |

**Edge Case Handling**:

| File | Classification | Reason |
|------|------|------|
| `{file name}` | `{subdirectory}` | {reason} |
| `{file name}` | {root directory} | {reason} |

> For old files filed into `02_DevPlanAndReport/` whose file name does not already start with a version
> number, they must also be renamed per `v{version}_{DocType}.md`; the rename entries are listed
> separately in `v{version}_RenamePlan.md` — this document is only responsible for moving them.

---

## 4 Execution Steps

| # | Action | Command/Notes | Status |
|---|------|----------|------|
| 1 | Create subdirectories | `mkdir -p 01_DocReviewRefine 02_DevPlanAndReport 03_CodeReviewRefine 04_DebugFix 99_Prompts` | {🟢 Completed} |
| 2 | Move document-review-type files | `git mv docs_review_*.md tech_design_review_*.md design_completeness_review_*.md html_review_*.md uiux_pattern_summary_*.md 01_DocReviewRefine/` | {🟢 Completed} |
| 3 | Move plans-and-reports-type files | `git mv *_dev_plan_*.md *_implementation_*.md *_progress_review_*.md *_improvement_plan_*.md *_rename_plan_*.md *_reorganize_plan_*.md 02_DevPlanAndReport/` | {🟢 Completed} |
| 4 | Move code-review-type files | `git mv FE_review_*.md BE_review_*.md code_review_*.md 03_CodeReviewRefine/` | {🟢 Completed} |
| 5 | Move debug-type files | `git mv debug_*.md fix_report_*.md 04_DebugFix/` | {🟢 Completed} |
| 6 | Move the prompt library | `git mv prompt_library.md 99_Prompts/` | {🟢 Completed} |
| 7 | Verify the result | {Confirm the root directory contains only `prompt_history.md` plus the five numbered subdirectories} | {🟢 Completed} |
| 8 | Update cross-references | {Scan and update links pointing to the moved files, see §5} | {🟢 Completed} |

> Use `git mv` rather than `mv`, so Git can more accurately detect the change as a rename.

---

## 5 Impact Scope

- **No code impact**: {Only involves moving document files under the `docs/` directory; does not affect any source code or the build process.}
- **Cross-references**: {These historical documents are {not referenced / referenced} by code or CI.}
  - {If referenced, list the reference points that need updating:}

| # | File Containing the Reference | Old Path | New Path | Status |
|---|-------------|--------|--------|------|
| 1 | `{file}` | `{ai_dev_history/xxx.md}` | `{ai_dev_history/01_DocReviewRefine/xxx.md}` | {🟢} |
| 2 | `{file}` | `{ai_dev_history/dev_plan/xxx.md}` | `{ai_dev_history/02_DevPlanAndReport/xxx.md}` | {🟢} |

- **Git tracking**: {Git automatically recognizes these as renames, preserving full history.}
- **Going forward**: {New documents are filed directly into the corresponding numbered subdirectory per the §3 classification rules, rather than placed flat in the root directory.}

---

## 6 State After Reorganization

| Item | Before Reorganization | After Reorganization |
|------|-------|--------|
| Root directory file count | {n} | {1} |
| `01_DocReviewRefine/` file count | — | {n} |
| `02_DevPlanAndReport/` file count | — | {n} |
| `03_CodeReviewRefine/` file count | — | {n} |
| `04_DebugFix/` file count | — | {n} |
| `99_Prompts/` file count | — | {n} |
| Maximum directory depth | {1} | {2} |

**Verification**:

```bash
# Confirm the root directory contains only prompt_history.md plus the five numbered subdirectories
ls -la {TargetDirectory}/

# Confirm the total file count is unchanged
find {TargetDirectory} -name "*.md" | wc -l
```
