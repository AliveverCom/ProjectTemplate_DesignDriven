<!--
TEMPLATE NOTES (delete this comment block when using)

[DOCUMENT LEVEL] `99_Prompts` — the reusable prompt library.
  It archives the standard prompts that have been proven to work in this project. Every component
  running the same workflow copies them straight from here, which keeps the output format
  consistent across components and across sessions.

[TARGET LOCATION] platform level: docs/ai_dev_history/99_Prompts/prompt_library.md
  Components normally do **not** maintain their own copy — they simply use the platform-level one.
  Only when a component genuinely has a large body of component-specific workflows should an
  **incremental** file be added under
  docs/components/{component}/ai_dev_history/99_Prompts/, opening with the statement
  "this file lists only prompts specific to this component; the general ones live in the
  platform-level prompt_library.md".

[TEMPLATE FILE NAME] The leading `_` is only a template marker; drop it when copying
  -> prompt_library.md.

[WHY THE NUMBER 99] It does **not** belong to any stage of the development lifecycle. It is a
  toolbox that spans the whole of it, so it sorts last, clearly separated from the stage
  directories 01-04.

[DIFFERENCE FROM ai_dev_history/prompt_history.md] Keep these apart; neither replaces the other:

  | Dimension | This file, prompt_library.md | prompt_history.md |
  |-----------|------------------------------|-------------------|
  | Nature | **Reusable template** | **Historical archive** |
  | Content | Abstracted standard prompts with one-off context removed | The real prompts as they were actually sent, verbatim |
  | Time | No timestamps; organised by purpose | Timestamped; accumulated newest-first |
  | Count | One per platform | One per component |
  | Answers | "What prompt should I send next time I do this kind of thing?" | "Why does this document look the way it does?" |
  | Change policy | If the output is poor, **revise** the entry | Append only; existing entries are **never modified** |

  Flow: once a prompt in prompt_history.md has been used repeatedly and proven effective, it should
  be **abstracted and added to this file**. Entries in this file are never written back into
  prompt_history.md.

[MAINTENANCE RULE] If a prompt produces a poor result, **revise this file** rather than improvising
  a new prompt next time. That is the entire point of this file.
-->

# Reusable Prompt Library

**Document Version**: {x.y.z}
**Scope**: all product components of {PlatformName}
**Maintenance rule**: if a prompt produces a poor result, **revise this file** rather than
improvising a new one next time.

---

## Index

Grouped by the stage directories of `ai_dev_history/`:

| # | Purpose | Input | Output | Stage Directory |
|---|---------|-------|--------|-----------------|
| 1 | [Generate the UI/UX requirements document from the business description](#1-generate-the-uiux-requirements-document-from-the-business-description) | `{component}_business_desc.md` | `{component}_uiux.md` | — (component doc root) |
| 2 | [Generate page mockups with Google Stitch](#2-generate-page-mockups-with-google-stitch) | Page requirement description | `ui_page_design/P{nn}  {PageName}/code.html` | — (component doc root) |
| 3 | [Review the UI/UX requirements document](#3-review-the-uiux-requirements-document) | `{component}_uiux.md` | Revised uiux.md | `01_DocReviewRefine/` |
| 4 | [Three ways to act on a review](#4-three-ways-to-act-on-a-review) | Review report + Engineer's Response | Revised document or HTML + a new review round | `01_DocReviewRefine/` |
| 5 | [Generate a dev plan](#5-generate-a-dev-plan) | Design documents + version plan | `v{version}_{BE\|FE}_DevPlan.md` | `02_DevPlanAndReport/` |
| 6 | [Cross-document consistency review](#6-cross-document-consistency-review) | All documents of the component | `docs_review_{YYYYMMDD}-r{n}.md` | `01_DocReviewRefine/` |
| 7 | [Snapshot and AI handover](#7-snapshot-and-ai-handover) | The whole context of the current session | `v{version}_Snapshot.md`, `v{version}_Handover.md` | `02_DevPlanAndReport/` |
| 8 | [Code review](#8-code-review) | Source code + design documents | `{BE\|FE}_code_review_{YYYYMMDD}-r{n}.md` | `03_CodeReviewRefine/` |
| 9 | [Defect fixing and regression](#9-defect-fixing-and-regression) | Review report + Engineer's Response | `fix_report_{YYYYMMDD}-r{n}.md` | `04_DebugFix/` |
| 10 | [Development progress review](#10-development-progress-review) | Code + version plan | `v{version}_DevReport.md` | `02_DevPlanAndReport/` |
| 11 | [Draw Mermaid diagrams](#11-draw-mermaid-diagrams) | Document content | `*.mmd` + `*.png` | — (same directory as the owning document) |

> **Path convention**: `{ai_dev}` below stands for
> `docs/components/{component}/ai_dev_history/` (component level) or
> `docs/ai_dev_history/` (platform level).

---

## 1 Generate the UI/UX Requirements Document from the Business Description

**When to use**: `{component}_business_desc.md` is finished and the component's UI/UX requirements
document needs to be produced.

**Output**: `docs/components/{component}/{component}_uiux.md`

**Prompt**:

```
Generate the UI/UX requirements document for this component from
docs/components/{component}/{component}_business_desc.md, and write it to
docs/components/{component}/{component}_uiux.md.

Requirements:
1. Follow the template structure of templates/component/_uiux.md strictly.
2. Page design patterns must reference the existing sections of
   docs/uiux_design_specification.md (Single-List Page / Dual-List Page / Tree List Page /
   Business Object Create Page / Read-Only Page / Edit Page). At the top of each page
   subsection, add a blockquote stating "Follows uiux_design_specification.md §x.x.x".
   Do not restate general rules that the specification already covers.
3. §2.1 Page List must include a Description column.
4. §2.2 Navigation Menu must draw, in a code block, this component's menu tree inside the
   portal sidebar, and state which page is opened by default.
5. In §2.3 Page-Object-API Mapping, Dependent APIs must always be written as REST endpoint
   paths (e.g. GET /api/v1/xxx), never as logical names (e.g. Xxx.Search).
   The endpoints must match {component}_tech_design.md §5 exactly.
6. In §3, every page must contain four subsections: UI Design / Page Business Functions /
   Page Layout and Elements / Page Behaviors.
7. Group Page Layout and Elements by area (A. Left Panel, B. Right Panel, ...), and split each
   area into: Filter Area, Action Button Area, List Area, Row Interaction.
   Give every control an Element / Control Type / Default Value / Description.
   For the List Area, give every column a Column Header / Data Source / Width / Style / Sortable.
8. Page Behaviors uses a numbered table: Trigger / Action / Result. The Action cell must name the
   concrete REST endpoint being called.
9. End the document with §4 Route Definitions and a Change Log.
10. Chapter 1 is the Table of Contents; all sections use numeric numbering.

Do not generate any page HTML. This step produces the requirements document only.
```

---

## 2 Generate Page Mockups with Google Stitch

**When to use**: the UI/UX requirements document is finished and each page needs an HTML mockup.

**Output**: `docs/components/{component}/ui_page_design/P{nn}  {PageName}/code.html` + `screen.png`

**Stitch prompt (minimal)**:

```
create a new page with light style. Without portal framework and main menu.
```

**Stitch prompt (full)**:

```
Create a new page with light style. Without portal framework and main menu.

Page: {PageName}
Layout: {Single-List Page / Dual-List Page / Detail Page / Dashboard / Two-Column Debug Console}

{paste the full §3.x.3 Page Layout and Elements text for this page from {component}_uiux.md}

Requirements:
- Light theme, white background.
- Desktop only, no responsive breakpoints.
- Use realistic sample data consistent with the field descriptions above.
- Table columns must match the column list exactly, in the same order.
- {any other requirement specific to this page}
```

**Notes**:

| # | Note |
|---|------|
| 1 | Save the HTML Stitch produces as `code.html` and its rendered screenshot as `screen.png`, both inside the same `P{nn}  {PageName}/` directory |
| 2 | Directory name format: `P{two-digit number}{two spaces}{PageName}`, matching the Page ID and Page Name in uiux.md §2.1. **The two spaces are a hard convention**, not a typo |
| 3 | Once generated, every HTML mockup must go through the review flow in [§4](#4-three-ways-to-act-on-a-review) to be reconciled with uiux.md |
| 4 | The sample data inside the HTML is the **source of the mock data** for the later frontend-only version, so it must be realistic and consistent across pages |

---

## 3 Review the UI/UX Requirements Document

**When to use**: after the engineer has given a long UI/UX requirement description, to confirm the
AI wrote every detail into the document.

**Prompt**:

```
Improve docs/components/{component}/{component}_uiux.md.

When you are done, review {component}_uiux.md and confirm that every detail from my prompt above
has been written into the document with nothing lost or simplified. List my requirements one by
one together with the section each of them landed in. If any requirement is not covered, say so
explicitly and add it.

This prompt must be appended to
docs/components/{component}/ai_dev_history/prompt_history.md.
```

**Notes**:

| # | Note |
|---|------|
| 1 | This is a **self-check review**: the AI verifies against its own input whether it dropped any requirement detail |
| 2 | The closing "append to prompt_history.md" is a fixed step, ensuring a long requirement description is archived into the accumulating file at the root of `{ai_dev}` |
| 3 | The output is a **revised uiux.md plus a coverage checklist**; no separate review file is produced |

---

## 4 Three Ways to Act on a Review

**When to use**: an HTML review report exists and the engineer has filled in the Engineer's Response
column; the AI now has to act on it.

### 4.a Treat the HTML as authoritative and change UiUx.md

```
Treat the HTML as authoritative and update
docs/components/{component}/{component}_uiux.md.

Using the entries marked "HTML is authoritative" in
docs/components/{component}/ai_dev_history/01_DocReviewRefine/html_review_{YYYYMMDD}-r{n}.md,
change the corresponding specifications in uiux.md so they match what the HTML actually
implements. Update the Change Log of uiux.md afterwards.
```

### 4.b Treat UiUx.md as authoritative and change the HTML

```
Treat docs/components/{component}/{component}_uiux.md as authoritative and update the HTML.

Using the entries marked "UiUx.md is authoritative" in
01_DocReviewRefine/html_review_{YYYYMMDD}-r{n}.md, go page by page through
ui_page_design/P{nn}  {PageName}/code.html and make each one conform to the uiux.md
specification. Change only the points raised; do not touch anything else along the way.
```

### 4.c Fix page by page per the Engineer's Response, then review again

```
Using the Engineer's Response column in
docs/components/{component}/ai_dev_history/01_DocReviewRefine/html_review_{YYYYMMDD}-r{n}.md,
fix, page by page, either the review baseline (uiux.md) or the review target (code.html) as the
response directs.

1) When the fixes are done, run the review again. The new review report does NOT need to include
   anything already fixed. It only needs to contain the issues that still stand, plus any newly
   discovered issues. There is no need to distinguish between the two, because the engineer does
   not care whether an issue is old or new — either way it must be fully resolved or answered.

2) Review report format:
   Start with the review metadata: "Document Title, Review Date, Review Baseline, Review Target,
   Previous Review File Name". Below that, output page by page. For each page, show the Page ID
   and title first, then the issue list: Issue ID, baseline specification, actual state of the
   review target, severity (as a coloured ball: 🔴 Critical, 🟠 High, 🟡 Medium, 🔵 Low), and
   Engineer's Response (left empty — the engineer fills it in later by hand).
   Finish with a summary table (pages x severity) and a Common Issues section.

3) Save the review to
   docs/components/{component}/ai_dev_history/01_DocReviewRefine/,
   using the file name format html_review_{yyyyMMdd}-r{review round}.md.
```

**Notes**:

| # | Note |
|---|------|
| 1 | 4.c is the **main path**; 4.a and 4.b are for bulk cases where the engineer has already decided one side is authoritative |
| 2 | The round number `r{n}` does not reset across dates |
| 3 | The Engineer's Response column is always left empty in a new report |

---

## 5 Generate a Dev Plan

**When to use**: the design documents have passed review and development of a version is about to
start.

**Output**: `{ai_dev}/02_DevPlanAndReport/v{version}_{BE|FE}_DevPlan.md`
(a master plan covering both sides uses `v{version}_DevPlan.md`)

**Prompt**:

```
Generate the {backend/frontend} dev plan for v{TargetVersion} of the {component} component, and
write it to
docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{TargetVersion}_{BE|FE}_DevPlan.md.

Follow the template structure of templates/ai_dev/02_DevPlanAndReport/_{BE|FE}_DevPlan.md strictly.

Requirements:
1. §0 Referenced Design Documents Index must be precise down to "file path + required sections",
   split into four groups: this component's design documents / platform-level convention
   documents / shared-library reference documents / previous-version code reference.
   The purpose of this chapter is to let an AI in a fresh session know exactly what to read on a
   cold start, so make it complete.
2. §1.2 must state explicitly what this version does NOT include, and in which version each
   excluded item is planned, to prevent implementation beyond scope.
3. §2 gives the target directory tree as it will look once this version is complete, with the
   parts added or reworked in this version shown in bold.
4. §4 groups development tasks into Phases: Phases run in sequence, tasks within a Phase may run
   in parallel. Each task states: task description / output file path / implementation notes /
   status column (left empty).
5. §5 the API endpoint routing table must line up one to one with the endpoints in
   {component}_tech_design.md §5, and each endpoint must cite its tech_design section number.
6. §6 Development Notes must capture the traps hit in the previous version — check
   ai_dev_history/04_DebugFix/ (debug_*.md, fix_report_*.md) and
   03_CodeReviewRefine/ (*_code_review_*.md).
7. Language and dependency versions must match the lock table in docs/technical_overview.md §5.
   Do not upgrade anything on your own initiative.

Baseline document: {component}_version_plan.md §{n} (the scope definition of v{TargetVersion}).
```

---

## 6 Cross-Document Consistency Review

**When to use**: several design documents of a component have been changed and you need to check
they are still consistent with one another.

**Output**: `{ai_dev}/01_DocReviewRefine/docs_review_{YYYYMMDD}-r{n}.md`

**Prompt**:

```
Perform a cross-document consistency review of every md and mmd file under
docs/components/{component}/, cross-checking against docs/technical_overview.md,
docs/naming_convention.md and docs/uiux_design_specification.md.

Write the result to
docs/components/{component}/ai_dev_history/01_DocReviewRefine/docs_review_{YYYYMMDD}-r{n}.md,
following the template structure of templates/ai_dev/01_DocReviewRefine/_docs_review.md strictly.

Requirements:
1. Chapter 1 is the verification table for the previous round (r{n-1}), not new issues.
   Check every issue in the r{n-1} report against the Engineer's Response to see whether it was
   actually carried out. The result can only be
   Done / Partial / Not Addressed.
2. Issues that were fixed and verified must not appear in the new issue list.
3. This report covers only Critical and Medium issues; ignore every Low issue.
4. Issue IDs use R{n}-{two-digit sequence}, globally unique and never reused.
5. Summarise each issue in one row of the Issue Summary table, then describe it in its own
   subsection: severity / affected files / problem description / impact / Option A and Option B
   for the engineer to decide between (mark the recommended one) / Engineer's Response (empty).
6. Finish with a Cross-Document Consistency Verification checklist covering at least:
   endpoint count and path consistency, the API references inside Page Behaviors, field names
   between class_diagram and tech_design, column names between db_schema and the table
   definitions, the mapping between business_desc business objects and class members, a single
   authoritative source per enum, platform field conventions such as soft delete, naming
   conventions, the validity of cross-references between sections, and the absence of leftover
   "to be completed" markers.

Focus especially on: two documents giving mutually exclusive definitions of the same thing. That
is the highest-priority class of issue, because the engineer cannot tell which one to trust.
```

---

## 7 Snapshot and AI Handover

**When to use**: two distinct situations — keep them apart:

| Situation | Use | Recipient | Core question |
|-----------|-----|-----------|---------------|
| The current session is about to run out of context and the same line of work must continue | [7.a Snapshot](#7a-snapshot) | A new session of the **same** AI | "Where did I get to?" |
| A stage is finished and the work is handed to another AI or another person | [7.b Handover](#7b-handover) | **Another** AI, or the engineer | "What do you need to know to take this over?" |

**Output**: `{ai_dev}/02_DevPlanAndReport/v{version}_Snapshot.md`, `v{version}_Handover.md`
(when a version needs several snapshots, add a date-time suffix: `v0.3_Snapshot_20260823-1430.md`)

### 7.a Snapshot

```
We are running out of context. Before we stop, save the current state of the work in full, and
write it to
docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{CurrentVersion}_Snapshot.md.

Follow the template structure of templates/ai_dev/02_DevPlanAndReport/_Snapshot.md strictly.

Write all of the following, **assuming the reader is you after losing your memory**:
1. What the current task is, and which documents are its baseline (precise to file path plus
   section number).
2. What is already done: list each item, mapping it to a concrete file path and the key changes.
   For anything already verified, state how it was verified (which command was run, which output
   was inspected).
3. What is in progress: which step you are stuck on and what the next action is. If a file was
   left half-edited, give its path and say exactly which part is unfinished.
4. What has not been started: the remaining task list from the original plan, keeping the
   original Phase and task numbering.
5. **Implicit knowledge** — this is the most important section. Write down what cannot be seen
   from the code or the documents and exists only inside this conversation:
   - decisions the engineer made during the conversation and the reasoning behind them
     (especially decisions NOT to do something);
   - approaches that were tried and failed, and why they failed (so whoever takes over does not
     repeat them);
   - environment and tooling traps hit along the way (command flags, version issues, path issues);
   - corrections to your own understanding of a piece of code or documentation
     ("I first assumed X; it is actually Y").
6. The first action for restoring the session: which files a new session should read first, and
   which command it should run first.

Do not write pleasantries or summary filler. Write only facts that will actually be useful to
whoever picks this up.
```

### 7.b Handover

```
This stage of the work is complete and must be handed over to another AI to continue.
Produce a handover document and write it to
docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{CurrentVersion}_Handover.md.

Follow the template structure of templates/ai_dev/02_DevPlanAndReport/_Handover.md strictly.

Write all of the following, **assuming the recipient knows nothing about this project**:
1. Handover scope: what is being handed over and what is not (draw the boundary explicitly, so
   the recipient does not overreach).
2. Cold-start reading list: the files the recipient must read before starting, in order, each with
   "file path + required sections + why it matters", marked 🔴 Must Read / 🟠 Important /
   🟡 Reference.
3. Current state: what this stage delivered, item by item mapped to file paths; which items are
   verified and how; which are not verified.
4. **Implicit knowledge the recipient cannot get from the code** — this is the core value of a
   handover document and must be written:
   - why it was designed this way (especially wherever it looks convoluted);
   - deliberate trade-offs ("there is no caching here because ...");
   - places where the implementation diverges from the design document, and why (is the document
     pending a fix, or the implementation?);
   - external prerequisites it depends on (environment variables, third-party accounts, other
     components that must be running first).
5. **Rejected alternatives and why** — as its own section. For each: what the alternative was, why
   it was rejected, and who rejected it (the engineer, or the AI's own judgement).
   The purpose is to stop the recipient from "reinventing" an approach that was already rejected
   and burning another round of discussion on it.
6. Known problems and outstanding items, in three tiers: P1 Blocking / P2 Important /
   P3 Maintenance, each with a recommended action.
7. Recommended next step: what the recipient should do first, and which document it should produce.

Do not write "good luck" or anything like it. Write only facts and the reasoning behind judgements.
```

**Notes**:

| # | Note |
|---|------|
| 1 | A Snapshot is **inward-facing** (for your future self), a Handover is **outward-facing** (for someone else); the level of detail differs accordingly — a Snapshot may omit project background, a Handover must supply it |
| 2 | Both must contain the "implicit knowledge" and "rejected alternatives" sections — that is the only information which cannot be re-derived from the repository; once lost, it is lost for good |
| 3 | The file name starts with the version (`v0.3_Snapshot.md`), so it naturally groups with the DevPlan and DevReport of the same version in the same directory |
| 4 | After a handover, the recipient's first prompt should be "read `v{version}_Handover.md`, confirm your understanding and list your questions" — not to start working straight away |

---

## 8 Code Review

**When to use**: the code for a version is written and the implementation must be reviewed against
the design documents.

**Output**: `{ai_dev}/03_CodeReviewRefine/{BE|FE}_code_review_{YYYYMMDD}-r{n}.md`

**Prompt**:

```
Review the {backend/frontend} source code of {component} against the design documents, and write
the result to
docs/components/{component}/ai_dev_history/03_CodeReviewRefine/{BE|FE}_code_review_{YYYYMMDD}-r{n}.md.

Follow the template structure of templates/ai_dev/03_CodeReviewRefine/_CodeReview.md strictly.

Review baseline (backend):
- docs/components/{component}/{component}_tech_design.md
  §2 Class Definitions / §4 Database Definitions / §5 API Definitions /
  §10 Source Code Directory Layout
- docs/technical_overview.md §4.1 API conventions, §4.2 database conventions,
  §4.4 backend conventions, §4.5 logging conventions
- docs/naming_convention.md in full

Review baseline (frontend):
- docs/components/{component}/{component}_uiux.md §2 Page Overview / §3 per-page specification /
  §4 Route Definitions
- docs/uiux_design_specification.md in full
- docs/naming_convention.md in full

Review target:
- backend: src/backend/{component}/
- frontend: src/frontend/packages/{component-kebab}/

Requirements:
1. Chapter 1 is the verification table for the previous round (r{n-1}); for a first review, write
   "first review, no previous issues". Issues that were fixed and verified must not appear in the
   new issue list.
2. **You must distinguish a code defect from a document defect.** This is the most important
   judgement in the review:
   - **Code defect**: the specification is sound, the implementation fails to meet it -> put it in
     the Issue List of this report, to be fixed by development.
   - **Document defect**: the implementation is sound, the document is wrong or out of date ->
     **do not ask for a code change in this report**. List it in its own section,
     "Items to be routed to 01_DocReviewRefine", explaining how the document should change; it
     then goes through the docs_review flow.
   - **Both wrong, or you cannot tell which is right**: put it under "Needs an engineer decision",
     with Option A / Option B and the recommended one marked.
3. Each issue states: Issue ID (CR-{two-digit sequence}, globally unique, never reused) /
   Severity (🔴 Critical · 🟠 High · 🟡 Medium · 🔵 Low) / file path:line / current state /
   the documented specification / suggested fix / Engineer's Response (empty).
4. Focus areas:
   - do API endpoint paths, methods, request parameters and response shapes match
     tech_design §5 word for word;
   - do database column names, types and indexes match tech_design §4;
   - do page elements, list columns, buttons and interaction behaviours match uiux §3;
   - does the naming follow naming_convention (C/E/I prefixes, snake_case columns,
     kebab-case frontend directories);
   - is anything implemented beyond the scope of this version's version_plan
     (implementing ahead of schedule is equally a defect);
   - has the layering been broken (a handler touching the database directly, a service assembling
     SQL, and so on).
5. Report only Critical and High issues; collect Medium ones into a single list section; ignore Low.
6. Finish with a "Deviation from the Design Documents" summary table and a statistics table.

Do not modify any code. This step produces the review report only.
```

**Notes**:

| # | Note |
|---|------|
| 1 | Separating code defects from document defects is the core of this prompt. Mixed together, the engineer cannot respond item by item, and document defects get "fixed" as if they were code defects, which drifts further off spec with every round |
| 2 | Items routed to `01_DocReviewRefine` keep their original `CR-{n}` ID so both sides can be reconciled |
| 3 | The round number `r{n}` does not reset across dates; frontend and backend are numbered independently |
| 4 | The fixing stage uses [§9](#9-defect-fixing-and-regression); its output lands in `04_DebugFix/` |

---

## 9 Defect Fixing and Regression

**When to use**: the engineer has filled in the Engineer's Response column of a code review report
or debug record, and the AI has to carry the fixes out item by item.

**Output**: `{ai_dev}/04_DebugFix/fix_report_{YYYYMMDD}-r{n}.md`

**Prompt**:

```
Using the Engineer's Response column in
docs/components/{component}/ai_dev_history/03_CodeReviewRefine/{BE|FE}_code_review_{YYYYMMDD}-r{n}.md,
carry out the fixes item by item and write a fix report to
docs/components/{component}/ai_dev_history/04_DebugFix/fix_report_{YYYYMMDD}-r{n}.md.

Requirements:
1. **One to one**: the entry numbers in the fix report must correspond exactly to the issue IDs
   (CR-xx) of the source report — nothing merged, nothing split, nothing skipped. Each entry gives:
   Issue ID / Engineer's Response / what actually changed (file path + key changes) / outcome.
   The outcome can only be: ✅ Fixed / 🟡 Partially fixed (say what remains) /
   ⏭️ Not changed, per the response (say why) / 🔴 Cannot fix (say what is blocking it).
2. **Follow the Engineer's Response exactly**; do not widen the scope on your own.
   If the response is "do as you suggested", do what you suggested in the source report — do not
   switch to a different approach at the last minute.
   If, while fixing, you find the response itself is problematic, **stop**. Add a section
   "Needs a second confirmation from the engineer" to the report rather than improvising.
3. **You must list "existing functionality this fix might have affected"** as its own section.
   For each item write:
   - which function in which file, or which area of which page, was changed;
   - which existing functionality passes through that code (callers, other pages reusing the
     component, other APIs depending on the field);
   - the impact judgement: 🔴 very likely affected / 🟡 possibly affected /
     🟢 theoretically unaffected.
   State the evidence for the judgement ("grep found 3 call sites" is far more useful than
   "should be fine").
4. **Run a regression check on every item marked 🔴 or 🟡 above**, in a dedicated
   "Regression Verification" section:
   - how it was verified (which command was run / which page was opened / which endpoint was
     called);
   - the actual result (paste the key output; do not just write "works");
   - conclusion: ✅ Passed / 🔴 New issue found (number the new issue FIX-{n} and add it to the
     outstanding items).
   For anything that cannot be verified automatically, write "needs manual verification" explicitly
   and give the manual verification steps.
5. Finish with: a fix statistics table (counted by outcome), the outstanding items list, and a
   recommendation on whether a new round of code review should be triggered.

Do not make any change unrelated to this fix round — that includes reformatting, renaming and
"while I'm here" optimisations.
```

**Notes**:

| # | Note |
|---|------|
| 1 | Points 3 and 4 are the core of this prompt: **impact surface plus regression check**. Without them a fix report only answers "what I changed", never "what I broke" |
| 2 | An impact judgement must state its evidence; conclusions such as "should be fine" with no basis are not acceptable |
| 3 | New issues found during regression get their own `FIX-{n}` numbers rather than reusing `CR-{n}`, so the numbering never crosses with the source report |
| 4 | The fix report shares the `{YYYYMMDD}-r{n}` suffix with its source review report, so the pair is easy to look up together |
| 5 | Investigative debugging (symptom unclear, root cause must be located first) uses the `04_DebugFix/_DebugRecord.md` template, not this prompt |

---

## 10 Development Progress Review

**When to use**: when you suspect the implementation has drifted away from the version plan and a
full stocktake is needed.

**Output**: `{ai_dev}/02_DevPlanAndReport/v{version}_DevReport.md`

**Prompt**:

```
Using docs/components/{component}/{component}_version_plan.md as the baseline, review the actual
development progress of {component} and write the result to
docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{CurrentVersion}_DevReport.md.

Follow the template structure of templates/ai_dev/02_DevPlanAndReport/_DevReport.md strictly.

Code paths:
- frontend: src/frontend/packages/{component-kebab}/
- backend: src/backend/{component}/

Requirements:
1. **Look only at what is actually in the code**, not at what the documents claim. Every
   conclusion must map to a concrete file path.
2. Use the four markers: 🟢 Done / 🟡 Partial / 🔴 Not Implemented / ⚪ Out of Scope.
3. Organise by LAYER rather than by version: frontend pages / API endpoints / backend layers
   (model, enum, service, repository, handler, pkg, config, cmd) / database / daemons /
   data migration. Gaps usually appear between layers, not within them.
4. After the overall version progress summary, add a "Key Finding" paragraph naming the single most
   important gap.
5. Finish with the outstanding issues list in three tiers: P1 Blocking / P2 Important /
   P3 Maintenance, each with a recommended action and an Engineer's Response column (left empty).
6. End with a recommended next action, naming which DevPlan should be produced next.
```

---

## 11 Draw Mermaid Diagrams

**When to use**: a document needs its companion business process diagram, class diagram, ER
diagram, page flow diagram or API relationship diagram.

**Output**: `{prefix}_{diagram_type}.mmd` plus a `.png` of the same name, in the same directory as
the owning document.

**Prompt**:

```
Generate the {business process diagram / class diagram / database ER diagram / page flow diagram /
API relationship diagram} for {component}, and write it to
{path}/{component}_{business_process|class_diagram|db_schema|page_flow|api}.mmd.

Follow the style conventions of the corresponding template under templates/mermaid/ strictly:
1. The canvas background must be white (background: "#FFFFFF" in themeVariables).
2. All text must be black (textColor / primaryTextColor / classText all "#000000").
3. Every box must be classified by TYPE, with a different LIGHT background colour per type, a
   darker border in the same hue, and text colour #000000 throughout.
   - flowchart / graph: define categories with classDef and attach them to nodes with :::className.
   - classDiagram: **classDef / cssClass / ::: do not work at all** (no error is raised, they are
     silently ignored). You must write one `style {ClassName} fill:...,stroke:...,color:#000000`
     per class.
4. The diagram must contain a legend subgraph explaining what each colour means; a classDiagram
   does not support subgraph, so use the colour comment block at the top of the file instead.
5. The content must match {source_doc}.md §{n} exactly. Do not add or remove nodes on your own.
6. Do not leave any empty `%%` comment line (it breaks parsing); if the YAML front-matter title
   contains curly braces it must be quoted.

After generating it, export a PNG to the same directory with the same name:
mmdc -i {file}.mmd -o {file}.png -b white -s 3
```

**Notes**:

| # | Note |
|---|------|
| 1 | The exact init configuration, the classDef colour table and the pre-publish checklist are in `templates/mermaid/mmd_style_guide.md` |
| 2 | The `.mmd` is the source and the `.png` is what the document embeds; the two must always be updated together |
| 3 | Reference format inside a document: `![xxx](./xxx.png)` followed by a blockquote `> Source: [xxx.mmd](./xxx.mmd)` |
| 4 | Always render with `-s 3`: at the default resolution a long identifier in a narrow box gets clipped (`CANCELED` renders as `CANCELE`) |

---

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Change description} |
