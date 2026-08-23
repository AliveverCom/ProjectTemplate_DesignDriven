<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Tier] AI Development Process Documents —— 04_DebugFix

[Purpose] The fix summary report delivered after a round of **concentrated defect fixing**.
  Typical triggers:
    - Closing a batch of issues from a review report (01_DocReviewRefine / 03_CodeReviewRefine)
    - A concentrated fix of a batch of bugs accumulated during integration testing
    - A defect cleanup pass before a version release

[Difference From _DebugRecord.md]
  _DebugRecord.md  = the investigation process for a **single defect** (emphasis on the investigation path, hypotheses, and disproving them)
  _FixReport.md    = the delivered fix results for a **batch of defects** (emphasis on the closure status of each item and regression verification)
  A FixReport typically references multiple DebugRecords and one review report.

[Storage Location]
  Platform level: docs/ai_dev_history/04_DebugFix/
  Component level: docs/components/{component_code_name}/ai_dev_history/04_DebugFix/

[File Naming] fix_report_{YYYYMMDD}-r{n}.md
  The round number does not reset across dates.
  Examples: fix_report_20260318-r1.md, fix_report_20260325-r2.md

[Issue ID Rule]
  This report **does not mint new issue IDs** — it always reuses the original ID from the source report
  (CR-xx / R{n}-xx / TD-xx / D-xx), so the engineer can cross-check item by item against the source
  report. IDs are globally unique and never reused.

[Template File Name] The leading `_` is only a template marker; rename the file per the naming rule above when copying it.
-->

# {ComponentDisplayName} Fix Report r{n}

**Document**: fix_report_{YYYYMMDD}-r{n}.md
**Component**: {component_code_name}
**Target Version**: v{target version}
**Fix Date**: {YYYY-MM-DD}
**Fix Round**: r{n}
**Fixed By**: AI ({model name})
**Defect Source**: {list the source documents covered by this round of fixes, e.g.: `03_CodeReviewRefine/BE_code_review_20260317-r2.md`, `04_DebugFix/debug_FE_API_20260316-r1.md`}
**Status**: {🟢 All Closed / 🟡 Partially Closed / 🔴 Blocked Items Remain}

> **Notes**:
> 1. This report only summarizes **fix results**; it does not repeat issue details — see the corresponding ID in the source report for details.
> 2. The "Confirmed by Engineer" column is filled in by the engineer by hand; the AI always leaves it empty when generating this report.

---

## 1 Fix Scope

| # | Defect Source Document | Issue IDs Covered | Count |
|---|-------------|-----------|------|
| 1 | `03_CodeReviewRefine/{BE\|FE}_code_review_{YYYYMMDD}-r{n}.md` | {CR-01 ~ CR-08} | {8} |
| 2 | `01_DocReviewRefine/docs_review_{YYYYMMDD}-r{n}.md` | {R{n}-03, R{n}-05} | {2} |
| 3 | `04_DebugFix/debug_{scope}_{YYYYMMDD}-r{n}.md` | {Outstanding item 1} | {1} |
| | **Total** | | **{N}** |

**Sources Not Covered by This Round**: {state which source reports' issues are out of scope for this round, and why. Fill in "None" if there are none.}

---

## 2 Fix Inventory

| # | Issue ID | Severity | Issue Summary | Root Cause | How It Was Fixed | Changed Files | Status | Confirmed by Engineer |
|---|--------|--------|---------|------|---------|---------|------|-----------|
| 1 | {CR-01} | 🔴 | {issue in one sentence} | {the actual root cause} | {what change was made} | `{path}:{line}` | 🟢 Fixed | |
| 2 | {CR-02} | 🔴 | {issue in one sentence} | {cause} | {change} | `{path1}`, `{path2}` | 🟢 Fixed | |
| 3 | {CR-05} | 🟠 | {issue in one sentence} | {cause} | {change} | `{path}` | 🟡 Partially Fixed | |
| 4 | {R{n}-03} | 🟠 | {issue in one sentence} | {cause} | {change} | `{path}` | 🟢 Fixed | |
| 5 | {CR-07} | 🟡 | {issue in one sentence} | {cause} | {change} | `{path}` | 🔴 Not Fixed | |

> Status: 🟢 Fixed (the symptom is gone and has been regression-verified) | 🟡 Partially Fixed (the main symptom is resolved but a residual remains, must be explained in §3) | 🔴 Not Fixed (must be explained in §3)
> Severity is carried over from the source report's rating; do not re-rate it in this report.
> The "Confirmed by Engineer" column is filled in by the engineer by hand; the AI always leaves it empty when generating this report.

---

## 3 Not Fixed and Why

> This section covers every 🟡 Partially Fixed and 🔴 Not Fixed item from §2. Delete this section if everything is 🟢.

| # | Issue ID | Status | Reason Not Fixed / Not Closed | Disposition | Confirmed by Engineer |
|---|--------|------|---------------------|---------|-----------|
| 1 | {CR-05} | 🟡 | {e.g.: a full fix requires refactoring {module}, out of scope for this version} | {deferred to v{n}} | |
| 2 | {CR-07} | 🔴 | {e.g.: needs the engineer to first decide between {option A / option B}} | {needs engineer decision} | |
| 3 | {CR-09} | 🔴 | {e.g.: constrained by {external dependency}, currently cannot be worked around} | {converted to a known limitation, written into `{component}_tech_design.md` §{n}} | |

> "Disposition" values: deferred to v{n} / converted to a known limitation / needs an engineer decision / route to `03_CodeReviewRefine` for re-review.
> Any item marked "converted to a known limitation" must also be written into the corresponding design document's "Known Limitations" section, and this column must note the section it was written into.

---

## 4 Changed File Summary

### 4.1 New Files

| # | File Path | Reason | Related Issue ID |
|---|---------|------|-----------|
| 1 | `{path/to/new_file}` | {why it was added} | {CR-02} |

### 4.2 Modified Files

| # | File Path | What Changed | Related Issue ID |
|---|---------|---------|-----------|
| 1 | `{path/to/file}` | {what was changed} | {CR-01} |
| 2 | `{path/to/file}` | {what was changed} | {CR-02, CR-05} |

### 4.3 Deleted Files

| # | File Path | Reason for Deletion | Related Issue ID |
|---|---------|---------|-----------|
| 1 | `{path/to/removed_file}` | {why it was deleted} | {CR-04} |

> A subsection with no entries is deleted entirely.
> If this round of fixes also changed a design document, it must be listed here, noting that it has already gone through the document review process in `01_DocReviewRefine/`.

---

## 5 Regression Verification

### 5.1 Existing Functionality This Fix Might Have Affected

> List the **impact surface** first, then design the verification items based on it. This step cannot be skipped — the classic accident in a concentrated fix is repairing A and breaking B.

| # | Affected Existing Functionality | Reason for the Impact | Already Verified |
|---|----------------|---------|-----------|
| 1 | {e.g.: pagination on the P03 collection-task browsing page} | {shares the same pagination wrapper that CR-02 changed} | {Yes} |
| 2 | {e.g.: sorting on all list pages} | {CR-05 changed the sort field allowlist mapping} | {Yes} |

### 5.2 Verification Items

| # | Verification Item | Method | Result | Notes |
|---|--------|---------|------|------|
| 1 | {the original symptom of every 🟢 item is gone} | {verify each one against the reproduction steps in the source report} | ✅ | {{n}/{n} passed} |
| 2 | {regression of affected existing functionality} | {execute each item listed in §5.1} | ✅ | |
| 3 | {full endpoint regression} | {P{nn} API debug page, each endpoint} | ✅ | {{n}/{n} passed} |
| 4 | {compile / build} | {`{build command}`} | ✅ | |
| 5 | {unit tests} | {`{test command}`} | ✅ | {{n} passed} |
| 6 | {boundary scenarios} | {empty data / large data volume / network error} | ✅ | |

> Result values: ✅ Passed / ❌ Not Passed. A ❌ item must be explained in §6 Residual Risk.

---

## 6 Residual Risk

| # | Risk | Trigger Condition | Impact | Mitigation | Confirmed by Engineer |
|---|------|---------|------|---------|-----------|
| 1 | {risk description} | {under what conditions it would surface} | {consequence} | {current workaround / monitoring measure} | |

> The "Confirmed by Engineer" column is filled in by the engineer by hand; the AI always leaves it empty when generating this report.
> When there is no residual risk, keep this section and write "This round of fixes has no residual risk."

---

## 7 Summary

| Dimension | Value |
|------|------|
| Total issues covered | {N} |
| 🟢 Fixed | {n} |
| 🟡 Partially Fixed | {n} |
| 🔴 Not Fixed | {n} |
| Changed files (new / modified / deleted) | {n} / {n} / {n} |
| Regression verification pass rate | {n}/{n} |
| **Can the source report be closed?** | **{Yes / No}** |

{If No, list the IDs of the issues blocking closure, and each one's next responsible party (AI continues fixing / awaiting engineer decision).}
