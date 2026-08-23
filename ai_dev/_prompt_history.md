<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] AI development process documentation. **Sits in the root of
`ai_dev_history/`, not inside any numbered subdirectory.**

[Target Location]
  - Platform-level: docs/ai_dev_history/prompt_history.md
  - Component-level: docs/components/{component_code_name}/ai_dev_history/prompt_history.md
  - Common library: docs/common_lib/ai_dev_history/prompt_history.md

[Template File Name] The leading `_` is only a template marker; **remove it** when copying.
  On disk it is simply called `prompt_history.md`, **with no component-name prefix**
  (it already lives under the component's own ai_dev_history/, so adding a prefix would be
  redundant).

[Why It's in the Root Directory, Not Inside 01_/02_/03_/04_]
  The numbered subdirectories correspond to the **stages** of the development lifecycle, and
  each stage produces multiple documents with dates or version numbers. This file does not
  belong to any single stage — it is a **single accumulating file that spans the entire
  development lifecycle**: each component (or the platform) has **one, appended to only, never
  recreated, never split by stage**. Putting it in the root directory lets an AI at any stage
  find at a glance "what did the engineer actually say".

[Difference from 99_Prompts/_PromptLibrary.md] Be sure to distinguish these — they are not
interchangeable:

  | Dimension | This file, prompt_history.md | 99_Prompts/prompt_library.md |
  |-----------|-------------------------------|-------------------------------|
  | Nature | **Historical archive** | **Reusable template** |
  | Content | Raw prompts that actually occurred, verbatim | Abstracted standard prompts, with one-off context removed |
  | Time | Timestamped, accumulated in reverse chronological order | No timestamps, categorized by purpose |
  | Quantity | One per component | One for the whole platform |
  | Purpose | Answers "why does this document look the way it does" | Answers "what prompt should I send next time for a similar task" |
  | Changes | Append-only; historical entries are **never modified** | Revised whenever the output isn't ideal |

  Flow: once a historical prompt has been used repeatedly and proven effective, it should be
  **abstracted and added to the PromptLibrary**; entries in the PromptLibrary are not written
  back into this file.

[What to Record — Not Every Prompt Needs Recording] Only the following three categories are
collected:
  1. The user provided a **substantial, substantive description** for a given document (a
     multi-line-or-more specification);
  2. The user **explicitly requested** that the prompt be archived;
  3. A prompt that **changed the design direction or overturned an existing decision**.
  Routine exchanges like "change this bit here", "continue", "changed per your suggestion" are
  **not collected**.

[Content Type Values] See the values table in the body; only values from that table may be used.

[Ordering] **Reverse chronological** (newest at the top), since the most recent intent is looked
up most often.
-->

# {ComponentName} Prompt History

<!-- When used at the platform level, write the title as: # Prompt History -->

**Document Version**: {x.y.z}

> This file preserves raw prompts provided by developers during the design and development
> process, making the development process traceable. It does NOT need to contain every prompt —
> only those where a user explicitly provides a substantial description for a specific document,
> or when the user explicitly requests it, or when the prompt changed a design decision.
>
> This file preserves the original text of the **raw prompts** provided by the engineer during
> the development process, so that "why does the document look this way" can be traced.
> The document body is the result of AI processing; the original intent is preserved only here.
> Not every prompt needs to be collected — only prompts where the engineer gave a substantial
> descriptive requirement for a specific document, prompts the engineer explicitly asked to be
> recorded, or prompts that changed the design direction.

---

## Prompt Index

| # | DateTime | User | Content Type | Title | Related Document |
|---|----------|------|---------------|-------|---------------------|
| {3} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title} | {file path(s) produced or modified by this prompt} |
| {2} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title} | {file path(s) produced or modified by this prompt} |
| {1} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title} | {file path(s) produced or modified by this prompt} |

> The **Related Document** column lists the file(s) directly **produced or modified** by this
> prompt (can be multiple, separated by `, `); either a path relative to this file or relative
> to the repo root is fine, but stay consistent within the same file.
> Example: `../{component}_uiux.md`, `02_DevPlanAndReport/v0.3_BE_DevPlan.md`.
> If the prompt did not directly produce a file (e.g. it only confirmed a decision), fill in `—`
> and explain in the Title.

**Content Type values** (only values from the table below may be used):

| Value | Applicable Scenario | Typical Related Document |
|-------|------------------------|------------------------------|
| `business_desc` | Requirement descriptions related to the business description document | `{component}_business_desc.md` |
| `tech_design` | Requirement descriptions related to the technical design document | `{component}_tech_design.md` |
| `uiux` | Requirement descriptions related to the UI/UX requirements document | `{component}_uiux.md` |
| `ui_page_design` | Descriptions related to page mockups (HTML / Stitch) | `ui_page_design/P{nn}  {PageName}/code.html` |
| `version_plan` | Descriptions related to version planning and version scope division | `{component}_version_plan.md` |
| `dev_plan` | Descriptions related to the dev plan and implementation approach | `02_DevPlanAndReport/v{version}_{BE\|FE}_DevPlan.md` |
| `dev_report` | Descriptions related to the dev report and progress review | `02_DevPlanAndReport/v{version}_DevReport.md` |
| `doc_review` | Document review requirements, review report format, responses to document review conclusions | `01_DocReviewRefine/docs_review_{YYYYMMDD}-r{n}.md` |
| `code_review` | Code review requirements, responses to code review conclusions | `03_CodeReviewRefine/{BE\|FE}_code_review_{YYYYMMDD}-r{n}.md` |
| `debug` | Descriptions related to defect investigation, integration debugging, and fixes | `04_DebugFix/debug_{topic}_{YYYYMMDD}-r{n}.md` |
| `handover` | Instructions related to snapshots and AI-to-AI handovers | `02_DevPlanAndReport/v{version}_Handover.md`, `v{version}_Snapshot.md` |
| `other` | Anything not covered above (e.g. naming convention decisions, diagramming requirements, directory reorganization) | {fill in as appropriate} |

---

## Prompts

### # {3} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title}

**Related Document**: {file path(s) produced or modified by this prompt}

{Paste the original raw prompt content here. Paste it exactly as-is, with no rewriting,
condensing, or reformatting.}

---

### # {2} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title}

**Related Document**: {file path(s) produced or modified by this prompt}

{Paste the original raw prompt content here.}

---

### # {1} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title}

**Related Document**: {file path(s) produced or modified by this prompt}

{Paste the original raw prompt content here.}

---

> Prompts are listed in **reverse chronological order** (newest first).
> Prompts are arranged in **reverse order** (the newest one is at the top).
>
> To add a new prompt / Steps to append a new prompt:
>
> 1. **Insert a new row at the top of the Prompt Index table**, with `#` set to the current
>    maximum + 1, and fill in the "Related Document" column.
>    Insert a new row at the **top** of the Prompt Index table; `#` = current max + 1.
>    Numbers **only ever increase, never decrease**: even if a given entry is later judged
>    unnecessary to keep, its number is not reclaimed and existing numbers are not renumbered.
> 2. **Insert the new Prompt section directly below the `## Prompts` heading** (i.e. the newest
>    entry goes at the very top).
>    Add a new section at the **top** of the Prompts area, directly below the `## Prompts`
>    heading.
> 3. Each Prompt section follows this format / Each prompt section follows this format:
>    - **First line**: `### # {n} | {DateTime} | {User} | {Content Type} | {Short title}` —
>      exactly matching the index table row.
>      **First line**: matching the index row exactly.
>    - **Second line**: `**Related Document**: {path}`.
>    - **Body**: the raw prompt content, **verbatim, word for word**. Do not summarize, do not
>      reformat, do not fix typos, do not translate.
>      **Body**: the raw prompt content, **verbatim**. Do not summarize, reformat, or fix typos.
>    - **Ending**: a horizontal rule `---` followed by a blank line, then the next prompt.
>      **End**: a horizontal rule (`---`) followed by a blank line, then the next prompt.
> 4. **Existing entries are never modified, under any circumstance.** If a prompt's decision is
>    later overturned, do not change the original entry — instead, append the new prompt that
>    overturns it as a new entry at the top, and note in the new entry's Title that it
>    "overturns #{n}". The value of history lies in it being history.
> 5. After appending, update the `**Document Version**` at the top of the file.
>    Update `**Document Version**` at the top when adding prompts.
