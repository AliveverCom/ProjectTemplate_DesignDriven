<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Tier] AI Development Process Documents —— 04_DebugFix

[What This Directory Holds]
  - The **investigation and fix process record** for a single defect / integration failure (this file)
  - A **fix summary report** for a batch of defects (_FixReport.md)
  Boundaries:
    - Systematic, proactively-initiated code review goes in `03_CodeReviewRefine/`
    - Reviews of documents / design mockups go in `01_DocReviewRefine/`

[This File's Purpose]
  Records the complete process of a specific failure from symptom to closure, with emphasis on the
  **investigation path** (which hypotheses were tried, how they were disproved) rather than just the
  conclusion — so that a similar symptom next time can directly reuse the investigation path.
  One file per defect; if a defect requires multiple rounds of investigation, append -r{n}.

[Typical Scenarios]
  Frontend/backend integration issues such as the frontend not getting data, mismatched fields, CORS,
  pagination parameters not taking effect, enum value mismatches, timezone misalignment, a dynamic
  table not existing, daemon crashes, etc.

[Storage Location]
  Platform level: docs/ai_dev_history/04_DebugFix/
  Component level: docs/components/{component_code_name}/ai_dev_history/04_DebugFix/

[File Naming] debug_{scope}_{YYYYMMDD}[-r{round}].md
  {scope} uses a short identifier: FE_API / BE_DB / daemon / migration, etc.
  Examples: debug_FE_API_20260316-r1.md
            debug_daemon_crash_20260402.md

[Template File Name] The leading `_` is only a template marker; rename the file per the naming rule above when copying it.
-->

# {ComponentDisplayName} {Investigation Scope} Debug Record r{sequence}

**Date**: {YYYY-MM-DD}
**Component**: {component}
**Investigation Scope**: {e.g.: frontend → backend API integration}
**Related Version**: {frontend v{n} / backend v{n}}
**Investigated By**: AI ({model name})
**Related Documents**: {02_DevPlanAndReport/v{version}_FE_DevPlan.md / v{version}_BE_DevPlan.md}
**Status**: {🟢 Closed / 🟡 Partially Resolved / 🔴 Unresolved}

---

## 1 Symptom

{Describe using observable facts, without speculation. Make clear: what action → what was expected → what actually happened.}

| Item | Content |
|------|------|
| **Triggering Action** | {e.g.: opened the P01 page, selected the first source on the left} |
| **Expected Result** | {e.g.: the right-hand list shows the material records for that source} |
| **Actual Result** | {e.g.: the right-hand list is blank, console shows {error message}} |
| **Impact Scope** | {e.g.: both P01/P03 pages fail to load data} |
| **First Occurred** | {e.g.: after the frontend integrated the API (v{version}_FE_DevPlan Phase 4)} |
| **Always Reproducible** | {Yes / No (occurred {n} out of {n} times)} |

**Key Error / Log Excerpt**:

```
{raw error message or log, keep the full stack trace or response body, do not summarize}
```

---

## 2 Reproduction Steps

| # | Step | Expected | Actual |
|---|------|------|------|
| 1 | {Start the backend: `{command}`} | {service listens on :{port}} | {✅ Normal} |
| 2 | {Start the frontend: `{command}`} | {page is accessible} | {✅ Normal} |
| 3 | {Visit `{path}`} | {page renders} | {✅ Normal} |
| 4 | {Perform {action}} | {{expectation}} | {❌ {actual}} |

**Environment Info**:

| Item | Value |
|------|-----|
| Operating System | {value} |
| {Backend language} version | {value} |
| {Frontend runtime} version | {value} |
| Database version | {value} |
| Backend service address | {http://localhost:{port}} |
| Frontend dev server | {http://localhost:{port}} |

---

## 3 Investigation

> Record hypotheses and the disproving process one by one. **Disproved hypotheses must also be kept** — that is this document's greatest value.

| # | Hypothesis | How It Was Tested | Conclusion |
|---|------|---------|------|
| 1 | {backend service is not running} | {`curl {endpoint}`} | ❌ Disproved: {backend returns 200 normally} |
| 2 | {endpoint path mismatch} | {compared `router.go` with `services/{resource}Api.ts`} | ❌ Disproved: {paths match} |
| 3 | {CORS not allowed} | {checked the preflight response in the browser Network panel} | ❌ Disproved: {`Access-Control-Allow-Origin` is already returned} |
| 4 | {response structure unwrapping error} | {compared the backend `pkg/response.go` with the frontend interceptor's unwrapping logic} | ✅ **Confirmed**: {the frontend interceptor takes `res.data`, but the backend wraps it in two layers, `res.data.data`} |
| 5 | {field name casing mismatch} | {compared tech_design §2 with the actual JSON} | ❌ Disproved: {they match} |

**Key Investigation Commands / Techniques**:

```bash
# {explain what this command verifies}
{command}

# {explanation}
{command}
```

---

## 4 Root Cause

**Root Cause**: {a one-sentence statement of the true cause.}

**Detailed Analysis**:

{expand: why this happened, which piece of code made a wrong assumption, why it was not exposed earlier.}

**Root Cause Location**:

| # | File | Line | Problematic Code |
|---|------|----|---------|
| 1 | `{path/to/file}` | {123} | {problematic code snippet or description} |

**Why It Was Not Caught Earlier**:

{e.g.: during v0.1 the frontend used mock data, so the API was never actually called and the unwrapping logic was never exercised.}

---

## 5 Fix and Changed Files

**Approach**: {the chosen fix. If there were multiple options, explain why this one was chosen.}

| # | File | Change | Type |
|---|------|---------|------|
| 1 | `{path/to/file}` | {specific change} | {Fix} |
| 2 | `{path/to/file}` | {specific change} | {Fix} |
| 3 | `{path/to/file}` | {added defensive handling / added logging} | {Hardening} |

**Key Code Change**:

```{language}
// Before
{code}

// After
{code}
```

**Does the Design Document Need to Be Updated Too**: {Yes / No}.
{If yes, state which document and section, and route it to `01_DocReviewRefine/` to go through the document review process — do not modify the design document directly in this record.}

---

## 6 Verification Result

| # | Verification Item | Method | Result |
|---|--------|------|------|
| 1 | {original symptom is gone} | {repeat the §2 reproduction steps} | {🟢 Passed} |
| 2 | {related pages regression} | {visit each affected page {P01/P03}} | {🟢 Passed} |
| 3 | {full endpoint regression} | {click through each endpoint on the P{nn} API debug page} | {🟢 {n}/{n} passed} |
| 4 | {boundary scenarios} | {{empty data / large data volume / network error}} | {🟢 Passed} |
| 5 | {compile and build} | {`{build command}`} | {🟢 Passed} |

---

## 7 Outstanding Items

| # | Outstanding Item | Severity | Notes | Planned Handling | Engineer's Reply |
|---|---------|--------|------|---------|-----------|
| 1 | {outstanding issue} | 🟠 | {notes} | {v{n} / route to `03_CodeReviewRefine/`} | |

> The "Engineer's Reply" column is filled in by the engineer by hand; the AI always leaves it empty.

---

## 8 Lessons Learned

> Lessons from this investigation that should prevent recurrence.
> These should be written back into `02_DevPlanAndReport/v{version}_{BE|FE}_DevPlan.md` §6 "Development Notes."

| # | Lesson | Suggested Destination |
|---|------|-----------|
| 1 | {e.g.: the number of unwrapping layers in the frontend interceptor must strictly match the number of wrapping layers in the backend's response.go — this must be verified as soon as the first endpoint is integrated} | `v{version}_FE_DevPlan.md` §{n} |
| 2 | {e.g.: the first step when integrating a new component with the API should be to get the API debug page working, before adapting the business page} | `v{version}_FE_DevPlan.md` §{n} |
