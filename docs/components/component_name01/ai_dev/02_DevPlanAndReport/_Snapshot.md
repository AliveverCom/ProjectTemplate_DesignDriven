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
  - When multiple documents of the same type exist within one version, append a date suffix: v0.3_Snapshot_20260320.md
    (saved-state snapshots often happen more than once within a version, so a date suffix is required, and a time
     suffix when needed: v0.3_Snapshot_20260320_1430.md)
  - The version number is taken from {component}_version_plan.md

[Positioning of this file] A **saved-state snapshot** document. When an AI dev session is interrupted (context
       exhausted / user paused / environment failure / deliberate segmentation), it records "exactly how far things
       got" in full, so that any new session can resume without loss and without having to re-explore.

[Distinction from other documents in this directory —— especially Snapshot vs. Handover]
  | Dimension | v{x}_Snapshot.md (this file) | v{x}_Handover.md |
  |------|---------------------------|------------------|
  | Trigger | **Passive interruption**: context exhausted, crash, told to stop | **Active handover**: model change, splitting parallel work, role change |
  | Voice | First person — "here is how far I got" | Second person — "here is what you take over" |
  | Recipient | Possibly the next session of the same "me" | Explicitly another AI |
  | Focus | Environment state, half-edited file positions, traps already hit | Scope boundary, acceptance criteria, hard constraints |
  | Completeness bias | Lossless first — redundancy is fine | Clarity first — only what is needed |
  If one interruption also hands work to another AI, **write both**: the Snapshot records the site, the Handover assigns the task.

  - v{x}_DevPlan.md   —— the full plan written before development; this file only records **which step of the plan execution has reached**.
  - v{x}_DevReport.md —— the delivery acceptance written at version close-out; this file is a temporary state **mid-process**,
    which can be archived once the version is complete and is not used as a delivery basis.

[Writing Requirements]
  - **Write only facts, not speculation**. Anything unverified belongs in §7 Known Problems, not written up as already done.
  - Write every file path in full (from the repo root), and write every command in a form that can be copied and run directly.
  - §3 "Work In Progress" must be precise to file and location — this is the information most easily lost, and the most valuable.

[Human-filled columns] The "Confirmed by Engineer" column is always left empty by the AI.

[Template File Name] The leading `_` is only a template marker; rename per the convention above when copying.
-->

# {ComponentDisplayName} v{version} Snapshot

**Document**: v{version}_Snapshot[_{YYYYMMDD}].md
**Version**: v{version}
**Component**: {component_code_name}
**Saved At**: {YYYY-MM-DD HH:MM}
**Trigger Reason**: {Context exhausted / User paused / Environment failure / Deliberate segmentation / Other: {explanation}}
**Corresponding Dev Plan**: `v{version}_DevPlan.md`{, `v{version}_BE_DevPlan.md`}
**Executed By**: {AI model name and role}
**Code Branch / Commit**: `{branch}` @ `{commit hash}`{(if uncommitted, write "working tree uncommitted, see §5.4")}

---

## 1 Current Task and Objective

| Item | Content |
|------|------|
| **This Session's Goal** | {One sentence: which segment of the DevPlan this session is meant to complete} |
| **Currently Executing** | `v{version}_DevPlan.md` §{n} **Phase {n}: {Phase Name}**, step {n} |
| **Completed Through** | Phase {n}, step {n} ({step name}) |
| **Interrupted At** | Phase {n}, step {n} ({step name}), mid-execution |
| **Overall Completion** | About {n}% ({n}/{n} Phases) |

**Objective Recap**: {In 3–5 sentences, restate what this version is meant to achieve, so the recipient does not
have to re-read the whole DevPlan to understand the meaning of the current action.}

---

## 2 Completed Work

> List only **verified** output. Anything unverified belongs in §3 or §7.

| # | Work Item | Corresponding DevPlan | Output File | Verification Status |
|---|--------|-------------|---------|---------|
| 1 | {Phase {n} — {work item}} | §{n} | `{full file path}` | {🟢 Verified: {verification method and result}} |
| 2 | {Phase {n} — {work item}} | §{n} | `{full file path}` | {🟢 Verified: {verification method}} |
| 3 | {Phase {n} — {work item}} | §{n} | `{full file path}` | {🟡 Written but not verified} |

**Output File List** (new):

```
{full path}
{full path}
```

**Output File List** (modified):

| File | What Changed |
|------|---------|
| `{full path}` | {Summary of change} |
| `{full path}` | {Summary of change} |

---

## 3 Work In Progress

> **This is the most critical section.** It is what lets the recipient avoid duplicate work and overwriting a half-finished state.
> Every item must state clearly: which file was changed, up to which line/function, and how it was originally meant to be finished.

### 3.1 Files Currently Being Modified

| # | File | Where It Was Left Off | Expected Approach | Compiles/Runs? |
|---|------|---------|---------|---------------|
| 1 | `{full file path}` | {e.g.: `{FuncName}()` has its signature and first half written, missing the {xxx} branch handling, starting at line {n}} | {Expected implementation approach, including which existing functions to call, what to return} | {🔴 No, syntax incomplete / 🟢 Yes} |
| 2 | `{full file path}` | {Location description} | {Expected approach} | {Status} |

### 3.2 Half-Finished Work Notes

{For each half-finished piece, explain: why it was changed this way, what part is already settled, and what is
not yet figured out. If the code has `TODO` / `FIXME` markers, list them here with what each marker means.}

| Marker | Location | Meaning |
|------|------|------|
| `TODO({tag})` | `{file}:{line}` | {What needs to be done} |
| `FIXME({tag})` | `{file}:{line}` | {What is broken} |

### 3.3 Changes That Need Immediate Revert or Cleanup

> If any code was temporarily added for debugging, logic was commented out, or a timeout value was temporarily
> shortened, it must be listed here — otherwise it risks being mistaken for the real implementation and left in the code.

| # | File | Temporary Change | How to Handle |
|---|------|---------|---------|
| 1 | `{file}:{line}` | {e.g.: polling interval temporarily changed from 300s to 5s for debugging} | {Restore to 300s} |

---

## 4 Remaining Tasks Not Yet Started

| # | Task | Corresponding DevPlan | Dependency | Estimated Effort |
|---|------|-------------|------|-----------|
| 1 | {Phase {n} — {task}} | §{n} | {Depends on §3.1 item 1 being completed} | {Small/Medium/Large} |
| 2 | {Phase {n} — {task}} | §{n} | {Depends on task 1} | {Small/Medium/Large} |
| 3 | {Phase {n} — {task}} | §{n} | None | {Small/Medium/Large} |

**Dependency Graph**:

```
{task 1} → {task 2} → {task 4}
              ↘ {task 3}
```

{State which tasks can run in parallel and which must be sequential.}

---

## 5 Environment State

### 5.1 Running Services

| # | Service | Startup Command | Port | Status | Notes |
|---|------|---------|------|------|------|
| 1 | {backend service} | `{command}` | {8081} | {🟢 Running / 🔴 Stopped} | {Notes} |
| 2 | {frontend dev server} | `{command}` | {5173} | {Status} | {Notes} |
| 3 | {database} | `{command}` | {5432} | {Status} | {Notes} |

> If the recipient needs to restart, follow the recovery steps in §8 — do not start things by guesswork.

### 5.2 Database State

| Item | Content |
|------|------|
| Database Name | `{db_name}` |
| Migrations Applied | {Through `{migration file name}`} |
| Sample Data | {Imported / Not imported; source `{sql file}`} |
| Manual Changes | {Whether the table structure or data was changed by hand; if so, list each one — this kind of change is not in the migration scripts and is very easily lost} |

| # | Manual Change | SQL | Written Into a Migration Script? |
|---|---------|-----|------------------|
| 1 | {Change} | `{SQL}` | {🔴 Not yet written, needs to be added} |

### 5.3 Dependencies and Toolchain

| # | Dependency/Tool | Version | Newly Installed This Session | Notes |
|---|----------|------|---------|------|
| 1 | {dependency name} | {version} | {Yes/No} | {Where it was installed, how} |

### 5.4 Uncommitted Working Tree Changes

```bash
# The recipient should run this first to confirm the working tree state
git status
git diff --stat
```

| Item | Content |
|------|------|
| Committed | {Yes (hash `{hash}`) / No} |
| Stashed | {Yes (`{stash name}`) / No} |
| Untracked Files | {List them, or write "None"} |

### 5.5 Temporary Files and Artifacts

| # | Path | Purpose | Deletable? |
|---|------|------|---------|
| 1 | `{path}` | {Purpose} | {Deletable / Not deletable: {reason}} |

---

## 6 Key Decision Record

> Decisions made during this session that will affect subsequent implementation.
> The recipient **must not** overturn these decisions without being aware of them.

| # | Decision Point | Choice | Rationale | Confirmed by Engineer |
|---|--------|------|------|---------------|
| 1 | {Decision point description} | {Which approach was chosen} | {Why this was chosen, alternatives rejected and why} | |
| 2 | {Decision point description} | {Choice} | {Rationale} | |
| 3 | {Decision point description} | {Choice} | {Rationale} | |

> The "Confirmed by Engineer" column is filled in manually by the engineer; always left empty when AI-generated.
> For any unconfirmed decision with significant cost, the recipient should verify with the engineer before continuing.

---

## 7 Known Problems and Traps

> Traps hit during this session, so the next session doesn't hit them again. This is the most time-saving section of this document.

| # | Symptom | Root Cause | Workaround | Resolved? |
|---|------|------|---------|-----------|
| 1 | {Symptom description} | {Root cause} | {How to work around it / correct approach} | {🟢 Resolved / 🔴 Unresolved} |
| 2 | {Symptom description} | {Root cause} | {Workaround} | {Status} |

**Detailed Notes on Unresolved Problems**:

{For each 🔴 unresolved problem, explain: what has been tried, what possibilities have been ruled out, and the
suggested next step for investigation. If the problem is significant, start a separate `04_DebugFix/debug_{YYYYMMDD}.md` and link it here.}

---

## 8 Recovery Steps

> The recipient can return to the pre-interruption state by executing these in order. Every step must be a
> directly copy-pasteable command or an unambiguous action — do not write vague descriptions like "set up the environment."

| # | Action | Command / Description | Expected Result |
|---|------|------------|---------|
| 1 | {Confirm code state} | `git status && git log --oneline -5` | {Working tree matches the description in §5.4} |
| 2 | {Restore stash (if any)} | `git stash list && git stash pop` | {Changes back in the working tree} |
| 3 | {Start database} | `{command}` | {Port {n} reachable} |
| 4 | {Confirm migration state} | `{command}` | {Version is {xxx}} |
| 5 | {Start backend} | `{command}` | {Log shows `{key line}`} |
| 6 | {Start frontend} | `{command}` | {{url} accessible} |
| 7 | {Smoke test} | `{curl / page action}` | {Returns {expected}} |
| 8 | {Return to the interruption point} | {Open `{file}` at line {n}, continue per §3.1} | — |

**Required Reading** (read in order before taking over):

| # | File | Required Section | Priority |
|---|------|---------|--------|
| 1 | This file | Entire document | 🔴 Required |
| 2 | `v{version}_DevPlan.md` | §{n} starting at Phase {n} | 🔴 Required |
| 3 | `{component}_tech_design.md` | §{n} | 🟠 Important |
| 4 | `{component}_version_plan.md` | §{n} (v{version} scope and "not included") | 🟠 Important |

---

## 9 Notes

{Any information that doesn't fit the categories above but that the recipient should know. Delete this section if there is none.}
