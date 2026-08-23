<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] AI development process document. `01_DocReviewRefine` = **document-level**
       review and refine, distinct from the **code-level** review in `03_CodeReviewRefine`.

[Purpose] Single-document deep review report. Focuses on the internal quality of **one**
       design document (usually tech_design.md): whether the class definitions, database
       definitions, API definitions, and backend implementation logic are complete,
       self-consistent, and ready to code directly from.
       Difference from _docs_review.md: that one checks **cross-document consistency**,
       this one checks **single-document depth and completeness**.

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
    tech_design_review_{YYYYMMDD}_r{n}.md         — single-document deep review  ← **this file**
    design_completeness_review_{YYYYMMDD}-r{n}.md — frontend/backend design completeness review
    html_review_{YYYYMMDD}-r{n}.md                — HTML mockup vs. uiux document review
    uiux_pattern_summary_{YYYYMMDD}-v{n}.md       — common UI/UX pattern extraction proposal
  For reviewing business_desc, use: business_desc_review_{YYYYMMDD}_r{round}.md
  For reviewing uiux, use: uiux_review_{YYYYMMDD}_r{round}.md

[Issue IDs] Use continuous TD-{number} numbering, **incrementing across rounds without reset**:
  if r2 ends at TD-18, r3 starts at TD-19. IDs are never reused.

[Template file name] The leading `_` is only a template marker. When copying into ai_dev_history/,
       rename it per the rules above and drop the underscore.

[Review Iteration Protocol] See templates/ai_dev/readme.md:
  - The "Engineer's Response" column is always left empty when AI generates the report
  - Round N's report opens with the previous-round remediation confirmation table, not new issues
  - Fixed issues are not restated, and no distinction is drawn between old and new issues
  - Issue IDs are globally unique and never reused
  - Architectural disagreements must present Option A/B with the recommended one marked
-->

# {component}_tech_design.md — Review Report

**Review Date**: {YYYY-MM-DD}
**Document Reviewed**: `{component}_tech_design.md` v{x.y.z} (after r{N-1} revision)
**Review Round**: r{N}
**Review Objective**: {This round's review objective, e.g.: focused on the review of class definitions, database definitions, Service APIs, and backend implementation logic.}
**Scope**: {State clearly what is included, e.g.: §2 Class Definitions, §4 Database Definitions, §5 API Definitions}
**Out of Scope**: {State clearly what is explicitly excluded, e.g.: does not cover any daemon program logic (split out into independent design files under sub_* subdirectories); does not cover frontend page specs.}

> **Notes**:
> 1. {This report only lists newly found issues from this round; see the "r{N-1} Remediation Confirmation" table for items already fixed in r{N-1}.}
> 2. {🔵 Low severity issues are ignored.}

---

## r{N-1} Remediation Confirmation

| r{N-1} Issue | Remediation Result |
|-------------|---------|
| TD-{nn} {short issue name} | ✅ {Specific location and content of the fix, e.g.: added constraint description to §3 Introduction} |
| TD-{nn} {short issue name} | ✅ {§5.4.1 added a known-limitations note, clarifying the {n}-minute fixed threshold} |
| TD-{nn} {short issue name} | ✅ {Both §4.1 and §4.2 `updated_at` are now annotated with "auto-update via DB trigger"} |
| TD-{nn} {short issue name} | 🟡 {Partially complete: {remaining gap}} |
| TD-{nn} {short issue name} | 🔴 {Not addressed: {reason}} |

> 🟡 / 🔴 items must be re-listed in the "New Issues List" below, keeping their original ID.

---

## New Issues List

| # | Severity | Issue Description | Suggested Fix | Engineer's Response |
|---|--------|----------|----------|-----------|
| TD-{n} | 🔴 High | **{Issue title, bold}**<br>{Current state: how document location A reads, how location B reads}<br>**Conflict points**: (1) {conflict point 1}; (2) {conflict point 2}; (3) {conflict point 3}<br>**Impact**: {The engineer cannot determine which document to follow when implementing / will lead to {consequence}} | **Option A** (recommended): {approach}.<br>**Option B**: {approach}. | |
| TD-{n} | 🔴 High | **{Issue title}**<br>{Description}<br>**Impact**: {Query performance degrades linearly with data volume / SQL injection risk / frontend-backend field mismatch} | {Actionable suggestion specific to "add a {index type} index on the {field} column in §x.y"} | |
| TD-{n} | 🟠 Medium | **{Issue title}**<br>{Description}<br>**Missed scenario**: {undefined boundary condition} | {In §x.y, add: "{suggested wording to add}"} | |
| TD-{n} | 🟠 Medium | **{Issue title}**<br>{§x.y line {n}: the Description of `{field}` reads "{incorrect content}", should be "{correct content}". Compared with §{other location}, the two descriptions are inconsistent.} | {Correct the Description of the `{field}` row in §x.y from "{incorrect}" to "{correct}".} | |
| TD-{n} | 🟡 Low | **{Issue title}**<br>{Description, e.g.: the allowed values of `sort_by` in §x.y use "etc.", not fully enumerated, so the engineer cannot determine the complete list of valid fields.} | {Remove "etc." and fully enumerate all allowed values: `{a}`, `{b}`, `{c}`.} | |

> The "Engineer's Response" column is filled in by the engineer manually. AI must always leave it empty, never guess, and never write a placeholder like "TBD".
> Use `<br>` to break lines within the Issue Description column, preserving the layering inside each table cell: current state → conflict points → impact.

---

## Review Coverage Checklist

| Section | Review Item | Conclusion |
|------|--------|------|
| §2 Class Definitions | {Every class is annotated with the business object it maps to} | ✅ |
| §2 Class Definitions | {Every member has a type, UI/DB display or storage requirement, and description} | ✅ |
| §2 Class Definitions | {Enums have a single authoritative definition location; referenced types are annotated with their source module} | ✅ |
| §{n} Program Descriptions | {Every daemon/program describes its trigger method, inputs, outputs, and failure/retry behavior} | ✅ |
| §4 Database | {Every table has a primary key, index annotations, soft-delete and timestamp fields} | ✅ |
| §4 Database | {All fields appearing in WHERE / ORDER BY have indexes} | ✅ |
| §4 Database | {The creation timing and naming rules for dynamic tables/sharded tables are clear} | ✅ |
| §5 API | {Every endpoint has Endpoint / Description / Authentication} | ✅ |
| §5 API | {Request parameter tables are complete, including Location / Type / Required} | ✅ |
| §5 API | {Request and response example fields are consistent with the parameter tables} | ✅ |
| §5 API | {Error responses and error codes are defined, covering 404 / 400 / 500 scenarios} | ✅ |
| §5 API | {Pagination and sorting parameter values are fully enumerated, no "etc."} | ✅ |
| §5 API | {Boundary scenarios such as cross-table aggregation and non-existent tables have defined handling} | ✅ |
| §{n} Development Technology | {Language, framework, and dependency versions are locked} | ✅ |
| §{n} Source Directory | {Directory tree matches the actual plan, including version-range notes} | ✅ |
| Appendix | {Input validation rules, logging conventions, and test strategy are defined} | ✅ |

---

## This Round's Conclusion

| Dimension | Count |
|------|------|
| 🔴 High | {n} |
| 🟠 Medium | {n} |
| 🟡 Low | {n} |
| **Total** | **{N}** |

**Can independent development proceed based on this**: {Yes / No}. {If no, list the blocking item IDs.}
