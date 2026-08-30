<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] AI development process document. `01_DocReviewRefine` = **document-level**
       review and refine, distinct from the **code-level** review in `03_CodeReviewRefine`.
       Note: HTML mockups are **design documents** (not source code), so their review reports
       belong in this directory.

[Purpose] Bidirectional alignment review between HTML UI mockups and the UI/UX requirements
       document. Compares `ui_page_design/P{nn}  {PageName}/code.html` page by page against
       the spec differences in `{component}_uiux.md`, producing a page-organized issue list
       for the engineer to decide "which one is authoritative" item by item.

[What this directory holds] Design document consistency review, technical design review,
       frontend/backend design completeness review, UI/UX HTML mockup review, UI/UX common
       pattern extraction.
       Rule of thumb: if the review target is a **.md / .mmd / .html design draft** → put it here;
                       if the review target is **source code** → put it in `03_CodeReviewRefine/`.

[Output directory]
  - Platform level: docs/ai_dev_history/01_DocReviewRefine/
  - Component level: docs/components/{component_code_name}/ai_dev_history/01_DocReviewRefine/

[File naming] `{type}_{YYYYMMDD}[-r{round}].md`, the round number does **not** reset across dates
       (e.g. docs_review_20260310-r6.md → docs_review_20260314-r7.md).
  Naming per type in this directory:
    docs_review_{YYYYMMDD}-r{n}.md                — cross-document consistency review
    tech_design_review_{YYYYMMDD}_r{n}.md         — single-document deep review
    design_completeness_review_{YYYYMMDD}-r{n}.md — frontend/backend design completeness review
    html_review_{YYYYMMDD}-r{n}.md                — HTML mockup vs. uiux document review  ← **this file**
    uiux_pattern_summary_{YYYYMMDD}-v{n}.md       — common UI/UX pattern extraction proposal

[Three ways the engineer can handle this] (see templates/ai_dev/99_Prompts/_PromptLibrary.md §4)
  a. Treat HTML as authoritative, modify uiux.md
  b. Treat uiux.md as authoritative, modify HTML
  c. Per the "Engineer's Response" in this report, fix the review baseline or review target page
     by page as indicated, then re-review

[Template file name] The leading `_` is only a template marker. When copying into ai_dev_history/,
       rename it per the rules above and drop the underscore.

[Review Iteration Protocol] See templates/ai_dev/readme.md:
  - The "Engineer's Response" column is always left empty when AI generates the report
  - Round N's report opens with the previous-round remediation confirmation table, not new issues
  - Fixed issues are not restated, and there is **no need** to distinguish whether an issue is
    old or new — the engineer only cares about what remains to be handled
  - Issue IDs are globally unique and never reused
  - Architectural disagreements must present Option A/B with the recommended one marked
-->

# {ComponentDisplayName} HTML Review Report

> **Document Title**: {ComponentName} ({component}) HTML Review Report
> **Review Date**: {YYYY-MM-DD}
> **Review Baseline**: `{component}_uiux.md` v{X.Y.Z}
> **Review Target**: `code.html` files under `ui_page_design/P01–P{nn}`
> **Previous Review File Name**: {Previous review file name; write "None" for the first review}

---

Note: In the Engineer's Response, "UiUx.md" refers to the review baseline `{component}_uiux.md`, and "HTML" refers to the review target.

---

## Review Scope

| Page ID | Page Name | HTML Path | Reviewed This Round |
|---------|-----------|----------|-------------|
| {P01} | {PageName} | `ui_page_design/P01  {PageName}/code.html` | ✅ |
| {P02} | {PageName} | `ui_page_design/P02  {PageName}/code.html` | ✅ |
| {P03} | {PageName} | — | ⚪ {HTML mockup not yet produced} |

---

## {P01} {PageName}

| # | Issue | UiUx.md Specification | HTML Actual | Severity | Engineer's Response |
|---|------|-------------|----------|-------|------------|
| 1 | **{IssueSummary}** | {Spec description from UiUx.md §3.x.3} | {Actual implementation in HTML} | 🔴 | |
| 2 | **{IssueSummary}** | {Spec description} | {Actual situation} | 🟠 | |
| 3 | **{IssueSummary}** | {Spec description} | {Actual situation} | 🟡 | |
| 4 | **{IssueSummary}** | {Spec description} | {Actual situation} | 🔵 | |

> Severity legend: 🔴 Critical | 🟠 High | 🟡 Medium | 🔵 Low
> The "Engineer's Response" column is filled in by the engineer manually. AI must always leave it empty, never guess, and never write a placeholder like "TBD".

---

## {P02} {PageName}

| # | Issue | UiUx.md Specification | HTML Actual | Severity | Engineer's Response |
|---|------|-------------|----------|-------|------------|
| 1 | **{IssueSummary}** | {Spec description} | {Actual situation} | 🔴 | |
| 2 | **{IssueSummary}** | {Spec description} | {Actual situation} | 🟡 | |

---

{Repeat the "## {PageId} {PageName}" + table structure above, per page}

---

## Summary

| Page | 🔴 Critical | 🟠 High | 🟡 Medium | 🔵 Low | Total |
|------|--------|-------|-------|-------|------|
| {P01} | {n} | {n} | {n} | {n} | {n} |
| {P02} | {n} | {n} | {n} | {n} | {n} |
| {P03} | {n} | {n} | {n} | {n} | {n} |
| **Total** | **{N}** | **{N}** | **{N}** | **{N}** | **{N}** |

### Common Issues

{List issues that recur across multiple pages — these usually mean uiux.md is missing a global
 rule, or the HTML generation lacked a unified design constraint. Note which pages each one
 involves. Delete this section if there are none.}

| # | Common Issue | Pages Involved | Suggested Handling | Engineer's Response |
|---|---------|---------|---------|-------------|
| C-01 | {Issue description} | {P01, P03, P05} | {Add a global rule section to uiux.md / add a platform-level spec to uiux_design_specification.md} | |
| C-02 | {Issue description} | {P02, P04} | {Suggested handling} | |

### Patterns Suggested for Promotion to Platform-Level Spec

{If a common issue reflects a reusable general design pattern, record it here and route it into
 templates/ai_dev/01_DocReviewRefine/_uiux_pattern_summary.md for the formal pattern-extraction
 process. Delete this section if there are none.}

| # | Pattern Name | Source Page(s) | Suggested Target Section |
|---|---------|---------|-------------|
| 1 | {PatternName} | {P01/P03} | `uiux_design_specification.md` §{n} |
