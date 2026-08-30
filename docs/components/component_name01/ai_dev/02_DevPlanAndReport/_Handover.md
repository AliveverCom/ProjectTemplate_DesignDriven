<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Tier] AI Development Process Docs —— `02_DevPlanAndReport`

[What this directory holds] Dev plans, dev reports, saved-state snapshots, AI handovers, improvement plans,
       naming normalization and directory reorganization plans. In short: "what is planned" and "what was actually built."

[Landing Directory]
  - Platform level: docs/ai_dev_history/02_DevPlanAndReport/
  - Component level: docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/

[File Naming —— hard rule for this directory] All documents must start with **the version number they belong to**:
       `v{version}_{DocType}[_{YYYYMMDD}].md`
  - v0.3_DevPlan.md / v0.3_BE_DevPlan.md / v0.3_FE_DevPlan.md / v0.3_DevReport.md
  - v0.3_Snapshot.md / v0.3_Handover.md / v0.3_ImprovePlan.md
  - v0.3_RenamePlan.md / v0.3_ReorganizePlan.md
  - When multiple documents of the same type exist within one version, append a date suffix: v0.3_Handover_20260320.md
    when splitting work across multiple AIs in parallel, append a recipient identifier: v0.3_Handover_BE.md / v0.3_Handover_FE.md
  - The version number is taken from {component}_version_plan.md

[Positioning of this file] The **handover document between AIs**. Written when one AI actively hands work off to
       another AI (model change, role change, splitting a large task into parallel sub-tasks) — a task brief.

[Distinction from other documents in this directory —— especially Handover vs. Snapshot]
  | Dimension | v{x}_Handover.md (this file) | v{x}_Snapshot.md |
  |------|--------------------------|------------------|
  | Trigger | **Active handover**: model change, splitting parallel work, role change | **Passive interruption**: context exhausted, crash, told to stop |
  | Voice | Second person — "here is what you take over" | First person — "here is how far I got" |
  | Recipient | Explicitly another AI | Possibly the next session of the same "me" |
  | Focus | Scope boundary, acceptance criteria, hard constraints, implicit knowledge | Environment state, half-edited file positions, traps already hit |
  | Completeness bias | Clarity first — only what is needed | Lossless first — redundancy is fine |
  If the interrupted work also needs to preserve the current state, **write both**: the Snapshot records the site,
  the Handover assigns the task, and this document's §2 should list the Snapshot as required reading.

  - v{x}_DevPlan.md —— the complete plan for **the whole version**; this file only carves out the slice handed to one party.
  - v{x}_DevReport.md —— delivery acceptance at version close-out; this file is a division-of-labor document mid-process.

[Writing Requirements]
  - **Boundary before content**: §1 must state both "what is part of this handover" and "what is explicitly not,"
    which is the single most effective column for keeping the recipient from overstepping.
  - §5 Context Notes is the core value of this template — write what the recipient **cannot tell from reading the
    code and docs alone** (why it was designed this way, which options were already rejected, what the engineer settled verbally).
  - Every outstanding task must carry a **decidable acceptance criterion** — not just "implement feature XX."

[Human-filled columns] §7's handover confirmation table is filled in by both the handing-over party and the
       taking-over party; left empty when AI-generated.

[Template File Name] The leading `_` is only a template marker; rename per the convention above when copying.
-->

# {ComponentDisplayName} v{version} Dev Handover

**Document**: v{version}_Handover[_{YYYYMMDD}][_{recipient identifier}].md
**Version**: v{version}
**Component**: {component_code_name}
**Handing Over**: {AI model name and role, e.g.: Claude Opus 5 / Backend Lead}
**Taking Over**: {AI model name and role, e.g.: Claude Sonnet 5 / Frontend Integration}
**Handover Time**: {YYYY-MM-DD HH:MM}
**Handover Scope**: {One sentence, e.g.: v0.2 frontend API integration layer (services/ + hooks/ + page changes)}
**Corresponding Dev Plan**: `v{version}_DevPlan.md`{, `v{version}_FE_DevPlan.md`}
**Accompanying Snapshot Document**: {`v{version}_Snapshot_{YYYYMMDD}.md` / None}

---

## 1 Handover Scope and Boundary

> This section is the recipient's action boundary. **The "explicitly NOT part of this handover" column is equally
> important** — without it, the recipient will end up touching things they shouldn't, causing conflicts with other parallel work.

| # | Handover Item | Part of This Handover | Explicitly **NOT** Part of This Handover |
|---|--------|-------------|---------------------|
| 1 | {Module/Directory} | {Specifically what to do} | {The part of the same directory not to be touched, and why} |
| 2 | {Module/Directory} | {Specifically what to do} | {What's not included} |
| 3 | {Module/Directory} | {Specifically what to do} | {What's not included} |

**Scope Summary in One Sentence**: {The recipient only does {X}, not {Y}. {Y} is owned by {whom} / already done / belongs to the next version.}

### 1.1 Boundary With Other Parallel Tasks

> If this handover is one branch of splitting a large task into multiple parallel tracks, the boundary of each
> track must be spelled out to avoid different tracks editing the same files.

| Recipient | Scope Owned | Exclusive Files/Directories | Shared Files (need coordination) |
|--------|---------|--------------|------------------|
| {Recipient A} | {Scope} | `{path}` | `{path}` ({coordination method}) |
| {Recipient B} | {Scope} | `{path}` | Same as above |

---

## 2 Required Reading for the Recipient

> Read in priority order. Do not start work before finishing everything marked 🔴 Required.

| # | File Path | Required Section | Priority | Why It Matters |
|---|---------|---------|--------|-----------|
| 1 | `{component}_version_plan.md` | §{n} (v{version} feature scope + **what's not included**) | 🔴 Required | {Pins down version boundaries, prevents overstepping into the next version} |
| 2 | `v{version}_DevPlan.md` | §{n} Phase {n}–{n} | 🔴 Required | {Full context for the handed-over task} |
| 3 | `{component}_tech_design.md` | §{n} ({class definitions}), §{n} ({API definitions}) | 🔴 Required | {Implementation basis} |
| 4 | `{component}_uiux.md` | §{n} ({page}) | 🔴 Required | {Frontend implementation basis} |
| 5 | `technical_overview.md` | §{n} ({global conventions}) | 🟠 Important | {Platform-level conventions, must not be violated} |
| 6 | `uiux_design_specification.md` | §{n} | 🟠 Important | {UI patterns and control conventions} |
| 7 | `naming_convention.md` | §{n} | 🟡 Reference | {Naming conventions} |
| 8 | `v{version}_Snapshot_{YYYYMMDD}.md` | Entire document | {🔴/🟡} | {If there is an accompanying Snapshot document} |

### 2.1 Reference Code (model your code on these)

| # | File Path | What to Reference |
|---|---------|---------|
| 1 | `{path to a comparable existing component file}` | {e.g.: API client wrapper pattern, error handling approach} |
| 2 | `{path}` | {Reference point} |

---

## 3 Already Completed

> Existing results the recipient can rely on directly. **Do not rewrite these.**

| # | Completed Item | Output File | Verification Status | How the Recipient Should Use It |
|---|-----------|---------|---------|---------------|
| 1 | {Content} | `{full path}` | {🟢 Verified: {method}} | {Call `{function/interface}` directly} |
| 2 | {Content} | `{full path}` | {🟢 Verified} | {Usage} |
| 3 | {Content} | `{full path}` | {🟡 Written but not verified} | {Verify {what} yourself before use} |

### 3.1 Interfaces / Contracts Ready to Use Directly

| # | Name | Location | Signature / Shape | Notes |
|---|------|------|------------|------|
| 1 | `{interface name}` | `{file}` | `{signature}` | {Semantics, error cases} |
| 2 | `{unified response structure}` | `{file}` | `{structure}` | {Notes} |

### 3.2 Integration Contract (frontend-backend agreed sample-data cross-reference keys)

| Object | Key Field | Sample Value | Source |
|------|---------|--------|------|
| {Object} | `{field}` | `{value}` | `{sql / mock file}` |

---

## 4 Outstanding Tasks

> Every item must have a **decidable** acceptance criterion. Avoid non-verifiable descriptions like "implement feature XX."

| # | Task | Acceptance Criteria | Dependency | Estimate |
|---|------|---------|------|------|
| 1 | {Task description} | {e.g.: `GET /api/v1/{resource}` is called when page P01 loads, the list renders {n} rows, and the Network panel shows no 4xx/5xx} | None | {Small/Medium/Large} |
| 2 | {Task description} | {Decidable acceptance criteria} | Task 1 | {Estimate} |
| 3 | {Task description} | {Decidable acceptance criteria} | Task 1 | {Estimate} |
| 4 | {Task description} | {Decidable acceptance criteria} | Tasks 2, 3 | {Estimate} |

**Suggested Execution Order**: {Task 1 → Task 2/3 (can be parallel) → Task 4}

### 4.1 Overall Completion Criteria

| # | Criterion | Verification Method |
|---|--------|---------|
| 1 | {All pages load correctly} | {Open each page in turn, no console errors} |
| 2 | {All endpoints integrated} | {Check off each item against the endpoint list in tech_design §5} |
| 3 | {Build passes} | `{build command}` |
| 4 | {DevReport produced} | {Write `v{version}_DevReport.md` after completion} |

---

## 5 Context Notes

> **This section is the core value of this document.** Write the implicit knowledge the recipient cannot get
> from reading the code and docs alone. Without this section, the recipient will re-walk paths already walked,
> or overturn decisions that have already been settled.

### 5.1 Why It Was Designed This Way

| # | Design Point | Why It Was Done This Way |
|---|--------|-------------|
| 1 | {Design point} | {Background and rationale} |
| 2 | {Design point} | {Background and rationale} |

### 5.2 Rejected Alternatives (do not propose them again)

| # | Alternative Considered | Reason for Rejection | Rejected By |
|---|-------------|---------|--------|
| 1 | {Alternative} | {Reason} | {Engineer / Handing-over AI} |
| 2 | {Alternative} | {Reason} | {Who rejected it} |

### 5.3 Conventions the Engineer Settled Verbally, Not Yet in Any Document

> These are the conventions most easily lost. If one is important and long-lived, it should also be flagged for writing back into a formal design document.

| # | Convention | Source | Needs to Be Written Back to a Document |
|---|------|------|---------------|
| 1 | {Convention content} | {`prompt_history.md` #{n} / engineer's reply during some review} | {Yes: should be written into `{document}` §{n} / No} |
| 2 | {Convention content} | {Source} | {Yes/No} |

### 5.4 Easy Traps

| # | Trap | Symptom | Correct Approach |
|---|----|------|---------|
| 1 | {Trap} | {What you'll see} | {Correct approach} |
| 2 | {Trap} | {Symptom} | {Correct approach} |

---

## 6 Constraints and Hard Limits

> Changes that violate these constraints will be rejected outright in review.

### 6.1 What Not to Change

| # | Do Not Change | Reason | If a Change Is Truly Needed |
|---|--------|------|----------------|
| 1 | `{file/directory}` | {Owned by {whom} / is the authoritative definition} | {Confirm with the handing-over party or the engineer first} |
| 2 | {An interface signature} | {Already called in {n} places} | {File an ImprovePlan and go through the decision process} |
| 3 | {Database table structure} | {Migration scripts already applied} | {Add a new migration, do not edit historical migrations} |

### 6.2 What Not to Expand Into

| # | Do Not Do | Reason |
|---|--------|------|
| 1 | {Implement v{next version}'s features} | {Beyond this version's boundary, see version_plan §{n} "Not Included"} |
| 2 | {Take the opportunity to refactor {module}} | {Conflicts with parallel tasks / not in this scope} |
| 3 | {Introduce a new dependency} | {Dependency versions are locked in technical_overview §5; additions require revising that table first} |

### 6.3 Conventions That Must Be Followed

| # | Convention | Source |
|---|------|------|
| 1 | {Unified response format} | `technical_overview.md` §4.1 |
| 2 | {Naming convention} | `naming_convention.md` §{n} |
| 3 | {UI design pattern} | `uiux_design_specification.md` §{n} |
| 4 | {Logging and data masking} | `technical_overview.md` §4.5 |

---

## 7 Handover Confirmation

> Filled in item by item by both the handing-over party and the taking-over party. Always left empty when AI-generated.
> The recipient should complete this table before starting work; unconfirmed items should be raised as questions, not guessed at.

| # | Confirmation Item | Handing Over | Taking Over |
|---|--------|--------|--------|
| 1 | Handover scope and boundary (§1) understood, no ambiguity | | |
| 2 | Required reading (§2) fully read | | |
| 3 | Completed work (§3) verified usable, will not be rewritten | | |
| 4 | Acceptance criteria for outstanding tasks (§4) are decidable | | |
| 5 | Context notes (§5) acknowledged, will not overturn rejected alternatives | | |
| 6 | Constraints and hard limits (§6) acknowledged | | |
| 7 | Environment starts correctly, current state reproducible | | |
| 8 | Outstanding questions raised and answered | | |

### 7.1 Recipient's Questions

> Filled in by the recipient; answered item by item by the handing-over party or the engineer before work begins.

| # | Question | Answer |
|---|------|------|
| 1 | | |
| 2 | | |

---

## 8 Notes

{Any information that doesn't fit the categories above but that the recipient should know. Delete this section if there is none.}
