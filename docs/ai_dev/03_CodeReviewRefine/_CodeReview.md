<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Tier] AI Development Process Documents —— 03_CodeReviewRefine

[What This Directory Holds]
  The review target is **source code** review and refine:
    - Frontend code review (FE_code_review)
    - Backend code review (BE_code_review)
    - Review of deviations between code and the design documents
    - Refactoring recommendations / code refine proposals
  Boundaries:
    - Reviews whose target is **.md / .mmd / .html design mockups** always go in `01_DocReviewRefine/`
    - The investigation and fix process for a single defect goes in `04_DebugFix/`

[Storage Location]
  Platform level: docs/ai_dev_history/03_CodeReviewRefine/
  Component level: docs/components/{component_code_name}/ai_dev_history/03_CodeReviewRefine/

[File Naming] {FE|BE}_code_review_{YYYYMMDD}-r{round}.md
  The round number does not reset across dates (r6 → continues as r7 the next day).
  Examples: FE_code_review_20260314-r1.md
            BE_code_review_20260320-r2.md
  When frontend and backend are reviewed together: code_review_{YYYYMMDD}-r{n}.md

[Review Iteration Protocol] Identical to 01_DocReviewRefine, see templates/ai_dev/readme.md:
  - The "Engineer's Reply" column must always be left empty when the AI generates the file, to be filled in by the engineer by hand
  - Chapter 1 of the round-N report is always fixed as the "Previous-Round Verification" table, not new issues
  - Issues that have been fixed are not listed again, and new/old issues are not distinguished
  - Issue IDs are globally unique and never reused

[Relationship to the Design Documents]
  The baseline for code review is {component}_tech_design.md and {component}_uiux.md.
  When a deviation is found, it must be judged as either **a code defect** or **a document defect**:
    - Code defect → open an issue item in this report, fix the code
    - Document defect → return to 01_DocReviewRefine and go through the document review process to
              correct the design document; do not modify the document directly inside a code review report

[Template File Name] The leading `_` is only a template marker; rename the file per the naming rule above when copying it.
-->

# {ComponentDisplayName} {Frontend / Backend} Code Review Report r{round}

**Review Date**: {YYYY-MM-DD}
**Review Round**: r{N}
**Reviewed**:
- {Frontend}: `src/frontend/packages/{component-kebab}/`
- {Backend}: `src/backend/{component}/`

**Review Baseline**:
| # | Baseline Document | Version | Purpose |
|---|---------|------|------|
| 1 | `{component}_tech_design.md` | v{x.y.z} | {class definitions, database, API specs} |
| 2 | `{component}_uiux.md` | v{x.y.z} | {page element and interaction specs} |
| 3 | `02_DevPlanAndReport/v{version}_{BE\|FE}_DevPlan.md` | — | {task checklist and acceptance criteria for this round of development} |
| 4 | `docs/naming_convention.md` | v{x.y.z} | {naming conventions} |
| 5 | `docs/technical_overview.md` §{n} | v{x.y.z} | {platform-level development conventions} |

**Target Version**: v{target version}
**Review Scope**: {State clearly what is included, e.g.: all output of Phase 1–7 from the §4 development task list}
**Out of Scope**: {State clearly what is excluded, e.g.: does not cover daemon program code; does not cover styling details (covered by the HTML review in 01_DocReviewRefine)}

> **Notes**:
> 1. {This report only lists issues that still need to be addressed this round; items fixed in r{N-1} appear in the previous-round verification table.}
> 2. {🔵 Low-severity issues are ignored.}

---

## Previous-Round (r{N-1}) Verification

| r{N-1} Issue ID | Handling | Verification Result |
|----------------|---------|---------|
| {CR-01} | {Engineer's decision + actual change} | ✅ {Fixed, `{file}:{line}` is now {new implementation}} |
| {CR-02} | {Engineer decided not to fix it, reason: {reason}} | ✅ {Consistent with the decision, code unchanged} |
| {CR-03} | {Change made} | 🟡 {Partially complete: {remaining gap}} |
| {CR-04} | {—} | 🔴 {Not implemented} |

> Items marked 🟡 / 🔴 must be re-listed below in the "Issue List" and keep their original ID.

---

## Issue List

| # | Severity | File:Line | Issue | Suggested Fix | Engineer's Reply |
|---|--------|---------|------|---------|-----------|
| {CR-{n}} | 🔴 High | `{path/to/file.ts}:{123}` | **{Issue title}**<br>{current state}<br>**Impact**: {what consequences it causes} | {an actionable suggestion specific enough to be "change X to Y"} | |
| {CR-{n}} | 🔴 High | `{path/to/file.go}:{45-58}` | **{Issue title}**<br>{current state}<br>**Violates**: {tech_design §x.y / naming_convention §n / platform convention §n} | {suggestion} | |
| {CR-{n}} | 🟠 Medium | `{path}` | **{Issue title}**<br>{current state}<br>**Impact**: {consequences} | {suggestion} | |
| {CR-{n}} | 🟡 Low | `{path}:{line}` | **{Issue title}**<br>{current state} | {suggestion} | |

> The "Engineer's Reply" column is filled in by the engineer by hand; the AI always leaves it empty when generating this report.
> Severity: 🔴 Critical (functional bug/security risk/blocks integration) | 🟠 High (deviates from the design, needs rework) | 🟡 Medium (readability/consistency) | 🔵 Low (wording/formatting)

---

## Deviation From the Design Documents

> When code and the design document are inconsistent, it must be judged whether to "fix the code" or
> "fix the document." Some deviations are reasonable evolution during implementation and should be
> written back into the document; some are implementation errors and should be fixed in the code.
> **An item judged as "fix the document" is not fixed directly in this report** — it must be routed to
> `01_DocReviewRefine/` and go through the document review process, keeping this report's issue ID as
> the source annotation over there.

| # | Design Document Requirement | Actual Code Implementation | Location | Judgment | Engineer's Reply |
|---|-------------|-------------|------|------|-----------|
| {D-01} | {tech_design §x.y: {requirement}} | {actual approach} | `{file}:{line}` | {Fix code / Fix document / Both} | |
| {D-02} | {uiux §3.x.3: {requirement}} | {actual approach} | `{file}:{line}` | {suggestion} | |

> The "Judgment" column gives the AI's recommendation; the "Engineer's Reply" column is where the engineer makes the final call.

---

## Convention Compliance Check

| # | Check Item | Basis | Result | Notes |
|---|--------|------|------|------|
| 1 | {Type naming C/E/I prefixes} | `naming_convention.md` §3 | ✅ | |
| 2 | {File and directory naming (frontend kebab-case / backend snake_case)} | `naming_convention.md` §{n} | ✅ | |
| 3 | {Layer responsibility boundaries (handler→service→repository, no cross-layer calls)} | `technical_overview.md` §{n} | ✅ | |
| 4 | {Unified response structure and error codes} | `technical_overview.md` §{n} | ✅ | |
| 5 | {Input validation done at the handler layer} | `tech_design.md` appendix | ✅ | |
| 6 | {Dynamic table name allowlist validation (injection prevention)} | `v{version}_BE_DevPlan.md` §{n} | ✅ | |
| 7 | {Sort field allowlist mapping, not concatenated directly into ORDER BY} | `v{version}_BE_DevPlan.md` §{n} | ✅ | |
| 8 | {Soft delete uses `deleted_at`, with consistent query filtering rules} | `technical_overview.md` §{n} | ✅ | |
| 9 | {Log levels, required fields, and sensitive-data masking} | `technical_overview.md` §{n} | ✅ | |
| 10 | {Frontend has no direct fetch/axios calls, all go through the services layer} | `v{version}_FE_DevPlan.md` §{n} | ✅ | |
| 11 | {Frontend introduces no dependency outside the design documents; versions match the lock table} | `technical_overview.md` §5 | ✅ | |
| 12 | {Route list exactly matches uiux §4} | `{component}_uiux.md` §4 | ✅ | |
| 13 | {API endpoint list exactly matches tech_design §5} | `{component}_tech_design.md` §5 | ✅ | |
| 14 | {Version boundary: no functionality beyond this version's scope was implemented} | `v{version}_DevPlan.md` §1.2 | ✅ | |

> Result values: ✅ Passed / ❌ Failed (a failed item must have a corresponding ID in "Issue List").

---

## Dev Plan Completion Check

> Cross-check the actual output against the task checklist in `02_DevPlanAndReport/v{version}_{BE|FE}_DevPlan.md` §{n}, phase by phase.
> The conclusions in this section should be consistent with that version's `v{version}_DevReport.md`; if not, this report takes precedence and the DevReport should be updated to match.

| Phase | Planned Task Count | 🟢 Complete | 🟡 Partial | 🔴 Not Done | Notes |
|-------|-----------|--------|--------|--------|------|
| Phase 1 {phase name} | {n} | {n} | {n} | {n} | |
| Phase 2 {phase name} | {n} | {n} | {n} | {n} | |
| **Total** | **{N}** | **{N}** | **{N}** | **{N}** | |

---

## Summary

| Severity | Count |
|--------|------|
| 🔴 Critical | {n} |
| 🟠 High | {n} |
| 🟡 Medium | {n} |
| **Total** | **{N}** |

| Dimension | Conclusion |
|------|------|
| Deviations from the design documents | {n} items (of which {n} judged "fix document", already routed to `01_DocReviewRefine/`) |
| Convention non-compliance items | {n} |
| Unresolved items from the previous round | {n} |
| **Ready for the next version's development?** | **{Yes / No}** |

{If No, list the IDs of the 🔴 items that must be closed first.}

---

## Common Issues

{List issues of the same type that recur repeatedly. This kind of issue usually means the dev plan is
 missing a unified convention, and should be written back into
 `02_DevPlanAndReport/v{version}_{BE|FE}_DevPlan.md` §6 "Development Notes" to prevent it from
 recurring in future versions. Delete this section if there is none.}

| # | Common Issue | Where It Occurred | Suggested Destination |
|---|---------|---------|-----------|
| {C-01} | {issue description} | `{file1}`, `{file2}`, `{file3}` | `v{version}_{BE\|FE}_DevPlan.md` §6.{n} |
