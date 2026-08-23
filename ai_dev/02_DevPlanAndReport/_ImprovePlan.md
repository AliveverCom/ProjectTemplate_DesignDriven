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
  - When multiple documents of the same type exist within one version, append a date suffix: v0.3_ImprovePlan_20260320.md
  - The version number comes from {component}_version_plan.md; a cross-version reorganization plan uses the current version at the time it was initiated

[This File's Purpose] A **continued improvement plan** for documents/design. Typical scenario: the first
       version of a component's design documents has just been written, and after self-review the AI
       finds a number of open questions that need the engineer to decide, listed as a checklist awaiting
       item-by-item answers. This is the vehicle for the loop "AI writes the document → AI proactively
       asks questions → engineer answers → AI completes the document."

[Difference From Other Documents in This Directory]
  - v{x}_DevPlan.md —— decisions are **already made**; it lists tasks to execute.
  - This file (ImprovePlan) —— decisions are **not yet made**; it lists multiple-choice questions for
    the engineer to decide. Only once every question in this file has been answered can the
    corresponding version's DevPlan be written.
  - v{x}_DevReport.md —— a post-hoc review of what was delivered; it does not raise pending decisions.

[Difference From Review Reports Under 01_DocReviewRefine / 03_CodeReviewRefine]
  - A review report **finds problems**: the document was written incorrectly, is internally
    inconsistent, or the code does not match the design.
  - This file **raises pending decisions**: the document is not wrong, but there are several
    multiple-choice questions that need the engineer to settle.

[Human-Filled Column] The "Engineer's Response" column must always be left empty when the AI generates
       this file; the AI must not decide on unanswered items on its own.

[Template File Name] The leading `_` is only a template marker; rename the file per the naming rule above when copying it.
-->

# {component} v{version} Continued Improvement Plan

> **Document Title**: {ComponentName} ({component}) {First-Version Document Review and Improvement Plan}
> **Version**: v{version}
> **Created**: {YYYY-MM-DD}
> **Prerequisite**: Based on {component} v{x.y.z} documents ({business_desc, tech_design, uiux})
> **Status**: {🟡 Awaiting Engineer's Response / 🟢 Closed}

---

## Completed

| # | Item | Status |
|---|------|--------|
| 1 | {Wrote business_desc.md v{x.y.z} ({summary of coverage})} | ✅ Completed |
| 2 | {Wrote tech_design.md v{x.y.z} ({API definitions, environment configuration, build commands, database connection, server parameters})} | ✅ Completed |
| 3 | {Wrote uiux.md v{x.y.z} (UI design, route definitions, and directory structure for {n} pages)} | ✅ Completed |
| 4 | {Drew {diagram}.mmd and exported it as png} | ✅ Completed |
| 5 | {Added to version_plan.md v{x.y.z}} | ✅ Completed |

---

## Awaiting the Engineer's Confirmation or Input

| # | Issue | Notes | Engineer's Response |
|---|------|------|-----------|
| {XX}-01 | **{Issue title}** | {Background of the issue, how the current document handles it, what is missing, and why an engineer decision is needed. If there is a preferred option, state it explicitly: "Recommend {option}; does the engineer agree?"} | |
| {XX}-02 | **{Issue title}** | {Notes} | |
| {XX}-03 | **{Issue title}** | {Notes} | |
| {XX}-04 | **{Issue title}** | {Notes} | |
| {XX}-05 | **{Issue title}** | {Notes} | |
| {XX}-06 | **{Issue title}** | {Notes} | |

> The "Engineer's Response" column is filled in by the engineer by hand; the AI always leaves it empty when generating this file.
> The issue ID prefix `{XX}` uses the component's abbreviation (e.g. unified_portal → `UP`), and is globally unique and never reused.
> Question-writing rule: **every question must be answerable as a multiple-choice or yes/no question**, avoiding open-ended, undecidable questions like "should X be improved."

---

## Impact Scope of Pending Items

> Explains which documents need to be updated once each pending decision is settled, so the engineer can judge the cost of deciding.

| Issue ID | Affected Document | Affected Section | Effort Estimate |
|---------|---------|---------|-----------|
| {XX}-01 | `{component}_tech_design.md` | §{n} | {Small/Medium/Large} |
| {XX}-02 | `{component}_tech_design.md`, `{component}_uiux.md` | §{n}, §{n} | {Small/Medium/Large} |
| {XX}-03 | `{component}_uiux.md` | §{n} | {Small/Medium/Large} |

---

## Execution Plan After the Responses

> Once the engineer has answered, the AI executes in this order.

| # | Action | Precondition | Output | Status |
|---|------|---------|------|------|
| 1 | {Update tech_design §{n} per the response to {XX}-01} | {XX}-01 answered | `{component}_tech_design.md` v{x.y.z} | |
| 2 | {Update uiux §{n} per the responses to {XX}-02/{XX}-03} | {XX}-02, {XX}-03 answered | `{component}_uiux.md` v{x.y.z} | |
| 3 | {Re-run the completeness review once all responses are implemented} | All answered | `01_DocReviewRefine/design_completeness_review_{YYYYMMDD}_v{n}.md` | |
| 4 | {Write this version's dev plan once the documents are finalized} | All answered and review passed | `v{version}_DevPlan.md` | |

---

## Notes

{Additional notes: e.g. some questions can be deferred to a later version; some questions depend on a
 platform-level convention being settled first; some questions the engineer has already answered
 verbally elsewhere and this is just a record. Delete this section if there is none.}
