<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] AI development process document. `01_DocReviewRefine` = **document-level**
       review and refine, distinct from the **code-level** review in `03_CodeReviewRefine`.

[Purpose] Cross-document consistency review report. Reviews whether mutually exclusive
       definitions, field-name disagreements, or broken section references exist among
       **all** the design documents (business_desc / tech_design / uiux / version_plan / *.mmd)
       under one component (or the whole platform), and between those documents and the
       platform-level specs (technical_overview / naming_convention / uiux_design_specification).

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
    docs_review_{YYYYMMDD}-r{n}.md                — cross-document consistency review  ← **this file**
    tech_design_review_{YYYYMMDD}_r{n}.md         — single-document deep review
    design_completeness_review_{YYYYMMDD}-r{n}.md — frontend/backend design completeness review
    html_review_{YYYYMMDD}-r{n}.md                — HTML mockup vs. uiux document review
    uiux_pattern_summary_{YYYYMMDD}-v{n}.md       — common UI/UX pattern extraction proposal
  For platform-level database/architecture-specific reviews, use: db_review_report_{YYYYMMDD}-r{round}.md

[Template file name] The leading `_` is only a template marker. When copying into ai_dev_history/,
       rename it per the rules above and drop the underscore.

[Review Iteration Protocol] See templates/ai_dev/readme.md:
  - The "Engineer's Response" column is always left empty when AI generates the report
  - Round N's report opens with the previous-round remediation confirmation table, not new issues
  - Fixed issues are not restated, and no distinction is drawn between old and new issues
  - Issue IDs are globally unique and never reused
  - Architectural disagreements must present Option A/B with the recommended one marked
-->

# {ComponentName} Document Review Report R{Round}

**Date**: {YYYY-MM-DD}
**Scope**: {Review scope, e.g.: all md and mmd files under the material_collector directory, cross-checked against technical_overview.md}
**Based on**: {Baseline state, e.g.: the latest document state after all {n} issues from the R{N-1} report were fixed}

> **Notes**:
> 1. {The resolution of previous-round issues has been verified; see the verification table below.}
> 2. {This report only reports 🔴 High and 🟡 Medium severity issues, ignoring all 🔵 Low severity issues.}
> 3. {This report only lists **newly found** issues not covered by R{N-2}/R{N-1}; fixed items are not repeated.}
> 4. {Additional unified improvements completed this round are covered in the "Additional Unified Improvements" section.}

---

## R{N-1} Change Verification Results

| R{N-1} Item | Handling | Verification Result |
|-------------|---------|---------|
| R{N-1}-01 | {Engineer's decision + actual AI change} | ✅ {Correctly modified, consistent with the definition in {document} §{section}} |
| R{N-1}-02 | {Engineer decided not to fix, reason: {reason}} | ✅ {Consistent with engineer's decision, document not modified} |
| R{N-1}-03 | {Change made} | 🟡 {Partially complete: {remaining gap}} |
| R{N-1}-04 | {Change made} | 🔴 {Not addressed: {reason}} |

> Verification results only take three values: ✅ Done / 🟡 Partial / 🔴 Not Addressed.
> 🟡 and 🔴 items must be re-listed as pending issues in the "Issue Summary" section below.

---

## Additional Unified Improvements

> This section is kept only when the engineer requested a module-wide unified change (e.g. "rename all API logic names to REST endpoints" across the board); otherwise delete the whole section.

The engineer requested "{verbatim request}", so the following changes were made on top of the R{N-1} fixes:

### A. {Improvement Item A Title}

| Section | Before | After |
|------|--------|--------|
| §{x.y} {location} | {original content} | {new content} |
| §{x.y} {location} | {original content} | {new content} |

### B. {Improvement Item B Title}

| Group | Change |
|------|------|
| {group} | {change description} |

### C. {Improvement Item C Title}

{Description}

### D. Change Log Update

{Add v{x.y.z} to the Change Log of {document}, recording the R{N-1} fixes and this round's unified improvements.}

---

## Issue Summary

| # | Severity | Affected Files | Issue Summary | Engineer's Response |
|---|---------|----------|----------|-------------|
| R{N}-01 | 🔴 High | {fileA.md §x.y vs fileB.mmd} | {One-sentence description of the conflict} | |
| R{N}-02 | 🟡 Medium | {file.md §x.y} | {One-sentence description} | |
| R{N}-03 | 🟡 Medium | {fileA.md §x vs fileB.md §y} | {One-sentence description} | |

> The "Engineer's Response" column is filled in by the engineer manually. AI must always leave it empty, never guess, and never write a placeholder like "TBD".

---

## R{N}-01 {Issue Title}

**Severity**: 🔴 High

**Affected Files**:
- `{file path A}` ({the file's role, e.g. "authoritative definition"/"reference display"})
- `{file path B}` ({role})
- `{file path C}` §{section}

**Problem Description**:

{Detailed description of the problem. Explain what the two definitions are, why they are mutually exclusive, and why the engineer will run into errors regardless of which one is implemented.}

{If comparing across multiple documents, list each document's current value in a table:}

| Document | {Field/Definition Name} |
|------|--------------|
| {fileA} (authoritative) | `{value}` |
| {fileB} (referencing) | `{value}` |
| {fileC} DDL | `{value}` |

**Impact**: {Explain the consequences — compilation failure / data inconsistency / frontend-backend field mismatch / security risk.}

**Engineer Decision Needed**:

**Option A** (recommended): {approach}.
Cost: {which files need to be changed accordingly}.

**Option B**: {approach}.
Cost: {cost}.

**Engineer's Response**:

---

## R{N}-02 {Issue Title}

**Severity**: 🟡 Medium

**Affected Files**:
- `{file path}` §{section}

**Problem Description**:

{Description}

**Suggested Fix**:

{Actionable suggestion specific to "change X to Y", avoiding empty phrases like "suggest improving".}

**Engineer's Response**:

---

## R{N}-03 {Issue Title}

**Severity**: 🟡 Medium

**Affected Files**:
- `{file path}` §{section}

**Problem Description**:

{Description}

**Suggested Fix**:

{Suggestion}

**Engineer's Response**:

> Repeat the `## R{N}-{number}` subsection above for as many issues as there are. One section per issue, in the same order as the "Issue Summary" table.

---

## Cross-Document Consistency Final Verification

| # | Check Item | Result |
|---|--------|------|
| 1 | {Total endpoint count consistency (tech_design §5 = uiux P{nn} §x.y = api.mmd = {n} endpoints)} | ✅ |
| 2 | {Endpoint path consistency (tech_design ↔ uiux ↔ api.mmd all endpoint paths fully consistent)} | ✅ |
| 3 | {API references in Page Behaviors all use REST endpoint paths, no leftover logic names} | ✅ |
| 4 | {Every API in uiux §2.3 Dependent APIs has a corresponding definition in tech_design §5} | ✅ |
| 5 | {class_diagram.mmd field names consistent with tech_design §2 class definitions} | ✅ |
| 6 | {db_schema.mmd column names consistent with tech_design §4 table definitions} | ✅ |
| 7 | {business_desc business object attributes map one-to-one to tech_design class members} | ✅ |
| 8 | {Enum definitions have a single authoritative source, all references point to the same place} | ✅ |
| 9 | {Platform-level field conventions such as soft delete / is_active are consistent across the whole module} | ✅ |
| 10 | {Naming conforms to naming_convention.md §2/§3/§4} | ✅ |
| 11 | {All §-section cross-references within the document are valid, none point to deleted sections} | ✅ |
| 12 | {No leftover *(to be filled in)* / TODO markers (except in Change Log descriptions)} | ✅ |
| 13 | {common_lib shared type field names consistent with references in this module} | ✅ |

> Result values: ✅ Pass / ❌ Fail (a failing item must have a corresponding issue ID in "Issue Summary").

---

## This Round's Conclusion

| Dimension | Conclusion |
|------|------|
| Newly found 🔴 issues | {n} |
| Newly found 🟡 issues | {n} |
| Unresolved from previous round | {n} |
| Ready to enter development | {Yes / No, explain blocking items} |

{If no serious new issues were found this round, state: "After a comprehensive review, **no serious new issues were found this round**." and list the conclusions of the key check points.}
