<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Tier] AI Development Process Documents —— `02_DevPlanAndReport`

[What This Directory Holds] Dev plans, dev reports, saved-state snapshots, AI handovers, improve plans,
       naming normalization and directory reorganization plans. In short, the two document types
       "what is planned" and "what was actually delivered."

[Storage Location]
  - Platform level: docs/ai_dev_history/02_DevPlanAndReport/
  - Component level: docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/
  Renaming often spans multiple components across both docs/ and src/, so it is usually placed at the
  **platform level**, planned and executed as a single unified pass rather than each component
  renaming on its own.

[File Naming —— hard rule for this directory] Every document must start with its version number:
       `v{version}_{DocType}[_{YYYYMMDD}].md`
  - v0.3_DevPlan.md / v0.3_BE_DevPlan.md / v0.3_FE_DevPlan.md / v0.3_DevReport.md
  - v0.3_Snapshot.md / v0.3_Handover.md / v0.3_ImprovePlan.md
  - v0.3_RenamePlan.md / v0.3_ReorganizePlan.md
  - When multiple documents of the same type exist within one version, append a date suffix: v0.3_RenamePlan_20260308.md
  - The version number comes from {component}_version_plan.md; a cross-version reorganization plan like
    this one uses the **current version at the time it was initiated** (at the platform level, use the
    most-advanced component version at initiation time, and state the basis for that choice at the top
    of the document)

[This File's Purpose] A **naming normalization execution plan** for directories and files. Once
       naming_convention.md establishes or revises a naming rule, use this template to batch-survey the
       existing non-compliant paths, listing each as "current path → target path" and tracking
       execution status.

[Difference From Other Documents in This Directory]
  - v{x}_DevPlan.md      —— changes **functionality** (adds code, implements endpoints).
  - This file (RenamePlan) —— changes **the path itself**, without touching any functional logic.
  - v{x}_ReorganizePlan.md —— only moves files, without renaming them; this file only renames, without
    changing directory levels. The two are often used together, but should be split into two documents
    and executed in two steps to make rollback easier.

[Execution Notes]
  - Git automatically detects these as renames, preserving full history;
  - After renaming, the entire repository must be scanned to update in-document references (Chapter 4),
    otherwise many links will break;
  - Renaming frontend directories affects the monorepo workspace configuration and import paths.

[Human-Filled Column] The "Execution Status" column is filled in incrementally by whoever executes the
       plan; the AI may leave it empty or mark it 🔴 Not Started when generating the plan.

[Template File Name] The leading `_` is only a template marker; rename the file per the naming rule above when copying it.
-->

# v{version} Directory and File Naming Normalization Plan

> **Document Title**: Directory and File Naming Normalization Execution Plan
> **Version**: v{version}
> **Created**: {YYYY-MM-DD}
> **Rule**: {`docs/` and `src/backend/` use **snake_case**; `src/frontend/` uses **kebab-case**; the `ui_page_design/` subdirectory is free-form}
> **Rule Basis**: [naming_convention.md](../../naming_convention.md) §{n}, [technical_overview.md](../../technical_overview.md) §{n}
> **Status**: {🟡 In Progress / 🟢 Completed}

---

## 1. Frontend Directory Renames ({snake_case → kebab-case})

> Scope: all {directories containing underscores} under `src/frontend/packages/`

| # | Type | Current Path | Target Path | Execution Status |
|---|------|---------|---------|--------|
| FD-01 | Directory | `src/frontend/packages/{old_name}/` | `src/frontend/packages/{new-name}/` | |
| FD-02 | Directory | `src/frontend/packages/{old_name}/` | `src/frontend/packages/{new-name}/` | |
| FD-03 | Directory | `src/frontend/packages/{old_name}/` | `src/frontend/packages/{new-name}/` | |

> Note: {`shared/` and `{single_word}/` are single words, so snake_case and kebab-case are identical and no rename is needed.}
> "Execution Status" values: ✅ Completed / 🟡 In Progress / 🔴 Not Started / ⚪ Abandoned (with reason attached).

---

## 2. Document File Renames ({kebab-case → snake_case})

> Scope: {all files under `docs/` containing a hyphen}

| # | Type | Current Path | Target Path | Execution Status |
|---|------|---------|---------|--------|
| DF-01 | File | `docs/{old-name}.md` | `docs/{new_name}.md` | |
| DF-02 | File | `docs/components/{component}/{old-name}.md` | `docs/components/{component}/{new_name}.md` | |

---

## 3. Document File Renames ({mixed case → snake_case})

> Scope: {all file names under `docs/` containing uppercase letters}

| # | Type | Current Path | Target Path | Execution Status |
|---|------|---------|---------|--------|
| DC-01 | File | `docs/components/{component}/{Component}_UiUx.md` | `docs/components/{component}/{component}_uiux.md` | |
| DC-02 | File | `docs/{OldName}.md` | `docs/{new_name}.md` | |

---

## 4. In-Document Reference Updates

> Renaming breaks Markdown links and path references in document body text. This chapter lists every
> reference point that must be updated in sync.
> **Must be executed immediately after the rename**, otherwise a large number of links will break.

| # | File Containing the Reference | Reference Content | Update To | Execution Status |
|---|-------------|---------|--------|--------|
| RF-01 | `docs/{file}.md` §{n} | `{old path}` | `{new path}` | |
| RF-02 | `docs/components/{component}/{file}.md` §{n} | `{old path}` | `{new path}` | |
| RF-03 | `README.md` | `{old path}` | `{new path}` | |
| RF-04 | `project_directory_plan.md` §{n} | `{old path}` | `{new path}` | |

**Scanning method**:

```bash
# Scan for all references to the old path
grep -rn "{old_name}" --include="*.md" .
grep -rn "{old_name}" --include="*.ts" --include="*.tsx" src/frontend/
grep -rn "{old_name}" --include="*.go" src/backend/
```

---

## 5. Configuration and Code Reference Updates

> Directory renames affect build configuration and code import paths.

| # | File | Update Content | Execution Status |
|---|------|---------|--------|
| CF-01 | `{monorepo workspace configuration}` | {package path changed from {old} to {new}} | |
| CF-02 | `{tsconfig paths / alias configuration}` | {path mapping updates} | |
| CF-03 | `{CI/CD configuration}` | {build path updates} | |
| CF-04 | `{Dockerfile / deployment scripts}` | {path updates} | |
| CF-05 | {source code import statements} | {global replacement of import paths} | |

---

## Summary

| Category | Item Count | ✅ Completed | 🟡 In Progress | 🔴 Not Started |
|------|-------|----------|----------|----------|
| Frontend directory renames (FD) | {n} | {n} | {n} | {n} |
| Document file renames (DF) | {n} | {n} | {n} | {n} |
| Document file renames (DC) | {n} | {n} | {n} | {n} |
| In-document reference updates (RF) | {n} | {n} | {n} | {n} |
| Configuration and code references (CF) | {n} | {n} | {n} | {n} |
| **Total** | **{N}** | **{N}** | **{N}** | **{N}** |

---

## Verification

| # | Verification Item | Method | Result |
|---|--------|------|------|
| 1 | {No remaining old paths} | `grep -rn "{old_name}" .` | {🟢 No matches} |
| 2 | {Frontend build passes} | `{build command}` | {🟢 Passed} |
| 3 | {Backend build passes} | `{build command}` | {🟢 Passed} |
| 4 | {Document links are valid} | {Click through every link in README and the overview one by one} | {🟢 All valid} |
| 5 | {Git recognizes them as renames} | `git status` | {🟢 Shows renamed rather than delete+add} |

---

## Impact Scope and Risks

| # | Risk | Impact | Mitigation |
|---|------|------|---------|
| 1 | {Path references in historical documents become invalid} | {Old report links in ai_dev_history point to paths that no longer exist} | {Archived historical documents are not updated; this plan alone records the mapping for later traceability} |
| 2 | {External bookmarks/links break} | {Impact} | {Measures} |
| 3 | {Conflicts with parallel development branches} | {Other branches' changes will conflict with the rename} | {Merge all in-flight branches before renaming, then rebase them uniformly afterward} |

---

## Old Path → New Path Master Mapping Table

> For tracing old paths in historical documents afterward. Keep this table long-term once the rename is complete.

| Old Path | New Path |
|--------|--------|
| `{old path}` | `{new path}` |
| `{old path}` | `{new path}` |
