# ProjectTemplate_DesignDriven

**A complete documentation template library for design-driven development**

**Templates Version**: see [git tags](https://github.com/AliveverCom/ProjectTemplate_DesignDriven/tags) · **License**: [MIT-0](./LICENSE)

---

## 0 About This Repository

This repository is a **documentation template library built for AI-assisted development**: you write
the business, technical and UI/UX design as structured documents first, then let an AI produce the
code from those documents. The documents are the single source of truth; the code is their
implementation.

None of these templates were designed in the abstract. They were **reverse-engineered from a real,
running multi-component platform**: tens of thousands of lines of real design documents were written
against early drafts of these templates, the template structure was refined over many rounds of
writing and review, and those refinements were folded back into the templates. Every section
heading, table column and hard rule you find here corresponds to a problem actually hit in practice
(each template's header comment block records the reasoning).

### 0.1 How to Use It

Drop the contents of this repository into your project's `templates/` directory:

```bash
git clone https://github.com/AliveverCom/ProjectTemplate_DesignDriven.git templates
rm -rf templates/.git
```

Then follow [§7 Workflow](#7-workflow) to copy templates into `docs/` and
`docs/components/{component}/`, rename them and fill them in.
**The root of this repository == the `templates/` directory of your project**, so every
`templates/xxx` path mentioned below refers to `xxx` in this repository.

### 0.2 What Is Inside

| Directory | Count | Content |
|-----------|-------|---------|
| `platform/` | 7 | Platform-level document templates → land in the `docs/` root |
| `component/` | 6 | Component-level document templates → land in `docs/components/{component}/` |
| `mermaid/` | 7 | Drawing conventions + 6 `.mmd` diagram templates (white canvas, category-coloured light fills, all-black text) |
| `ai_dev/` | 19 | AI development-process document templates → land in `ai_dev_history/`; includes `99_Prompts/_TaskBook.md`, the subagent task-book standard |
| `CLAUDE.md` + `.claude/agents/` | 1 + 5 | Project-root AI team rules (roles, concurrency, quota discipline, document authority chain) and the five subagent role definitions, each with a **resident knowledge pack** skeleton (build/test commands, toolchain, coding-standard digest, file map, known pitfalls) so subagents do not cold-start → land in the project root |

---

## 1 Table of Contents

- [0 About This Repository](#0-about-this-repository)
- [1 Table of Contents](#1-table-of-contents)
- [2 The Four Template Categories](#2-the-four-template-categories)
- [3 Platform-Level vs Component-Level: The Easiest Thing to Get Wrong](#3-platform-level-vs-component-level-the-easiest-thing-to-get-wrong)
- [4 Directory Structure](#4-directory-structure)
- [5 Naming Conventions](#5-naming-conventions)
- [6 General Writing Conventions](#6-general-writing-conventions)
- [7 Workflow](#7-workflow)
- [8 Where Each Template Came From](#8-where-each-template-came-from)
- [9 AI Team Rules, Task Books and Design Volumes](#9-ai-team-rules-task-books-and-design-volumes)
- [10 License](#10-license)

---

## 2 The Four Template Categories

| # | Category | Directory | Lands In | Purpose |
|---|----------|-----------|----------|---------|
| 1 | **Platform-level** product / business / technical docs | `platform/` | `docs/` root, project root | Describe the **whole platform**: business landscape, technical architecture and **global conventions** |
| 2 | **Component-level** product / business / technical docs | `component/` | `docs/components/{component}/` | Describe the **inside of a single component**: business objects, classes, tables, APIs, pages |
| 3 | **Mermaid diagram** templates | `mermaid/` | Alongside the document that references them | Uniform white-canvas, black-text, category-coloured drawing conventions and templates |
| 4 | **AI development-process** docs | `ai_dev/` | `docs/ai_dev_history/`, `docs/components/{component}/ai_dev_history/` | Review reports, dev plans, dev reports, improve plans, prompt history |

---

## 3 Platform-Level vs Component-Level: The Easiest Thing to Get Wrong

Platform-level and component-level documents have very similar names, but they are **two different
layers of product documentation**. The boundary must be held strictly:

| Topic | Platform-level (`docs/` root) | Component-level (`docs/components/{component}/`) |
|-------|------------------------------|--------------------------------------------------|
| **Business** | `business_overview.md`<br>Platform positioning, component landscape, user roles, end-to-end business process | `{component}_business_desc.md`<br>This component's business objects and attributes, and its internal business process |
| **Technical** | `technical_overview.md`<br>Layered architecture, **global conventions** (API / DB / frontend / backend / logging / security / versioning), dependency version locks, deployment | `{component}_tech_design.md`<br>This component's classes, enums, tables, API endpoints, source layout |
| **UI/UX** | `uiux_design_specification.md`<br>**Design patterns** (single/dual list pages, detail pages, dialogs), shared controls, colour, state management, formatting rules | `{component}_uiux.md`<br>This component's page list, menu, per-page layout, elements, behaviours and routes |
| **Versioning** | `version_plan.md`<br>General version numbering convention (five-segment `vA.B.C.D.E` semantics, milestone ladder, `VERSION` file strategy) | `{component}_version_plan.md`<br>What this component ships in each version from v0.1 to v1.0 |
| **Naming / layout** | `naming_convention.md`, `project_directory_plan.md` | None — components do not redefine these, they simply comply |

### Three Hard Rules

1. **A convention is defined exactly once, at platform level.** When a component-level document
   touches a global convention it **only references the section number**
   (e.g. "this component's APIs follow `technical_overview.md` §4.1"). It **must not redefine it**.
2. **On conflict, platform level wins.** If a component document contradicts a platform document,
   the component document is the defect. Raise it in a `01_DocReviewRefine` review and fix it there.
3. **Promotion path.** A pattern discovered inside one component starts as a proposal in
   `ai_dev/01_DocReviewRefine/_uiux_pattern_summary.md`. Once the engineer confirms it, it is
   written into the platform-level `uiux_design_specification.md`, and the component document is
   changed to a reference.

---

## 4 Directory Structure

```
templates/
├── readme.md                       # this file
│
├── platform/                       # (1) platform-level templates -> docs/ root
│   ├── _business_overview.md           # platform business and product overview
│   ├── _technical_overview.md          # platform technical overview + global conventions
│   ├── _uiux_design_specification.md   # platform UI/UX design specification
│   ├── _naming_convention.md           # platform naming convention
│   ├── _project_directory_plan.md      # project directory plan (lands in the project root)
│   ├── _version_plan.md                # general version convention -> docs/version_plan.md
│   └── _api_endpoint_list.md           # platform-wide API endpoint index -> docs/APIs/
│
├── component/                      # (2) component-level templates -> docs/components/{component}/
│   ├── _business_desc.md               # component business description
│   ├── _tech_design.md                 # component technical design
│   ├── _uiux.md                        # component UI/UX design
│   ├── _version_plan.md                # component version plan
│   ├── _sub_module_design.md           # sub-module / daemon design -> sub_{module}/
│   └── _migration_plan.md              # legacy data migration -> old_projects_migration/
│
├── mermaid/                        # (3) Mermaid templates -> next to the owning document
│   ├── mmd_style_guide.md              # drawing conventions (white canvas / light fills / black text)
│   ├── _business_process.mmd           # business process diagram
│   ├── _technical_architecture.mmd     # technical architecture diagram
│   ├── _class_diagram.mmd              # class diagram
│   ├── _db_schema.mmd                  # database schema diagram
│   ├── _api_diagram.mmd                # API call graph
│   └── _page_flow.mmd                  # page flow diagram -> ui_page_design/
│
└── ai_dev/                         # (4) AI development-process templates -> ai_dev_history/
    ├── readme.md                       # ai_dev overview + Review Iteration Protocol
    ├── _prompt_history.md              # prompt archive -> root of ai_dev_history/
    ├── 01_DocReviewRefine/             # document-level review and refine
    ├── 02_DevPlanAndReport/            # dev plan / report / snapshot / handover / improve plan
    ├── 03_CodeReviewRefine/            # code-level review and refine
    ├── 04_DebugFix/                    # debugging and defect fixing
    └── 99_Prompts/                     # reusable prompt library
```

The numbered directories under `ai_dev/` represent **stages of the AI development lifecycle**. When
they land in a project the **numbered directory names are kept verbatim**, under
`docs/ai_dev_history/` (platform level) or
`docs/components/{component}/ai_dev_history/` (component level).

Two special rules:

- Every document under `02_DevPlanAndReport/` is **named starting with the version it belongs to**:
  `v0.3_DevPlan.md`, `v0.3_BE_DevPlan.md`, `v0.3_DevReport.md`, `v0.3_Snapshot.md`,
  `v0.3_Handover.md`, `v0.3_ImprovePlan.md`.
- `prompt_history.md` lives in the **root** of `ai_dev_history/`, not in any numbered subdirectory —
  it is a single accumulating file that spans the whole development cycle, one per component,
  appended to and never recreated.

See [ai_dev/readme.md](./ai_dev/readme.md) for details.

---

## 5 Naming Conventions

### 5.1 Template File Names

- A **leading `_`** marks a "filename suffix": replace the `_` with the component code name when
  you use it.
  - `_business_desc.md` → `material_collector_business_desc.md`
  - `_tech_design.md` → `material_collector_tech_design.md`
  - `_class_diagram.mmd` → `material_collector_class_diagram.mmd`
- **Platform-level templates** also carry the `_`, but you simply **drop it** when they land
  (no prefix is added).
  - `_business_overview.md` → `business_overview.md`
- **Files without a `_`** (`readme.md`, `mmd_style_guide.md`) document the template
  library itself and are not copied into your project.

### 5.2 Target File Naming Rules

| Scope | Rule | Example |
|-------|------|---------|
| `docs/` | snake_case | `material_collector_tech_design.md` |
| `src/backend/` | snake_case | `material_collector/` |
| `src/frontend/` | kebab-case | `material-collector/` |
| `ui_page_design/` subdirectories | `P{nn}  {PageName}` (two spaces) | `P01  Source Media List` |
| `.mmd` / `.png` | Same prefix as the owning document | `material_collector_page_flow.mmd` |
| `02_DevPlanAndReport/` documents | `v{version}_{DocType}[_{YYYYMMDD}].md` | `v0.3_BE_DevPlan.md` |
| Other ai_dev documents | `{type}_{YYYYMMDD}[-r{round}].md` | `docs_review_20260314-r10.md` |

---

## 6 General Writing Conventions

| # | Convention | Detail |
|---|-----------|--------|
| 1 | **Chapter 1 is the table of contents** | Every document opens with a Table of Contents / Index |
| 2 | **Numeric section numbers** | All sections use `1` / `1.1` / `1.1.1`; anchor links must match the headings |
| 3 | **Placeholders** | Anything inside `{curly braces}` is to be replaced; none may survive into a published document |
| 4 | **Tables over prose** | If it can be a table, it is a table — attributes, parameters and behaviours always are |
| 5 | **Diagrams live in their own files** | A `.mmd` source plus a rendered `.png`; Markdown embeds the png and links the mmd source |
| 6 | **Cross references** | Relative path plus section number, e.g. `[technical_overview.md](../../technical_overview.md) §4.3` |
| 7 | **Optional sections** | Sections marked `> **OPTIONAL SECTION**: ...` are kept or deleted whole; renumber after deleting |
| 8 | **Version numbers** | `**Document Version**: {x.y.z}` in the header, `## Change Log` at the end |
| 9 | **Severity markers** | 🔴 Critical / High · 🟠 High / Medium · 🟡 Medium · 🔵 Low |
| 10 | **Progress markers** | 🟢 Done · 🟡 Partial · 🔴 Not Implemented · ⚪ Out of Scope |
| 11 | **To-do markers** | `> **⚠️ To Be Completed ({IssueID})**: {description}` |
| 12 | **Human-filled columns** | The last column of a review table is always "Engineer's Response"; an AI leaves it empty |

---

## 7 Workflow

### 7.1 Starting the Documents for a New Component

```
1. mkdir -p docs/components/{component}/{ai_dev_history,ui_page_design}
2. cp templates/component/_business_desc.md  docs/components/{component}/{component}_business_desc.md
   cp templates/component/_tech_design.md    docs/components/{component}/{component}_tech_design.md
   cp templates/component/_uiux.md           docs/components/{component}/{component}_uiux.md
   cp templates/component/_version_plan.md   docs/components/{component}/{component}_version_plan.md
3. echo 0.1.0 > docs/components/{component}/VERSION
4. Delete the TEMPLATE NOTES comment block at the top of each file, and delete
   any optional sections you do not need
5. Copy the .mmd templates you need from templates/mermaid/, rename them, render the png
6. Replace the {placeholders} section by section
```

### 7.2 Starting the Platform-Level Documents

```
cp templates/platform/_business_overview.md  docs/business_overview.md
cp templates/platform/_technical_overview.md docs/technical_overview.md
(drop the leading underscore; add no prefix)
```

### 7.3 During AI Development

Take the matching template from `templates/ai_dev/{numbered directory}/` according to the current
lifecycle stage, and produce the report following the naming rules and the Review Iteration Protocol
in [ai_dev/readme.md](./ai_dev/readme.md).

---

## 8 Where Each Template Came From

Every template was reverse-engineered from a real document in the source project. The table below
records that **origin** — these paths refer to files in the source project (a multi-component content
production platform), not to files in this repository. When you are unsure how much detail a section
expects, this table tells you which real document to think of.

| Template | Origin (the most complete instance in the source project) |
|----------|---------------------|
| `platform/_business_overview.md` | `docs/business_overview.md` |
| `platform/_technical_overview.md` | `docs/technical_overview.md` |
| `platform/_uiux_design_specification.md` | `docs/uiux_design_specification.md` |
| `platform/_naming_convention.md` | `docs/naming_convention.md` |
| `platform/_project_directory_plan.md` | `project_directory_plan.md` |
| `platform/_version_plan.md` | `docs/version_plan.md` |
| `platform/_api_endpoint_list.md` | `docs/APIs/api_endpoint_list.md` |
| `component/_business_desc.md` | `docs/components/{component}/{component}_business_desc.md` |
| `component/_tech_design.md` | `docs/components/{component}/{component}_tech_design.md` |
| `component/_uiux.md` | `docs/components/{component}/{component}_uiux.md` |
| `component/_version_plan.md` | `docs/components/{component}/{component}_version_plan.md` |
| `component/_sub_module_design.md` | `docs/components/{component}/sub_{program}/{program}_design.md` |
| `component/_migration_plan.md` | `docs/components/{component}/old_projects_migration/data_migration_plan.md` |
| `mermaid/_business_process.mmd` | `docs/business_process.mmd`, `{component}_business_process.mmd` |
| `mermaid/_technical_architecture.mmd` | `docs/technical_architecture.mmd` |
| `mermaid/_class_diagram.mmd` | `{component}_class_diagram.mmd` |
| `mermaid/_db_schema.mmd` | `{component}_db_schema.mmd` |
| `mermaid/_api_diagram.mmd` | `{component}_api.mmd` |
| `mermaid/_page_flow.mmd` | `ui_page_design/{component}_page_flow.mmd` |
| `ai_dev/01_DocReviewRefine/` | `ai_dev_history/review/docs_review_*.md`, `tech_design_review_*.md`, `FE_BE_review_*.md`, `html_review_*.md`, `uiux_design_summary_*.md` |
| `ai_dev/02_DevPlanAndReport/` | `ai_dev_history/dev_plan/BE_dev_plan_*.md`, `FE_dev_plan_*.md`, `*_implementation_*.md`, `dev_progress_review_*.md`, `*_improvement_plan_*.md`, `naming_convention_rename_plan_*.md`, `docs_reorganize_plan_*.md` |
| `ai_dev/03_CodeReviewRefine/` | `ai_dev_history/review/FE_review_*.md` |
| `ai_dev/04_DebugFix/` | `ai_dev_history/review/debug_*.md` |
| `ai_dev/99_Prompts/` | `docs/templates_prompt/uiux_prompts.md` |
| `ai_dev/_prompt_history.md` | `{component}/ai_dev_history/{component}_raw_prompts.md` |

---

## 9 AI Team Rules, Task Books and Design Volumes

Added in 1.3.0 after measuring a real project's spend: about 75% went to Opus subagents re-reading a
560 KB design document and third-party engine sources on every review round. Three template groups
address this:

| Template | Lands in | What it fixes |
|---|---|---|
| `CLAUDE.md` + `.claude/agents/*.md` | project root | Team roles and models, concurrency set by the engineer, 10/20-minute task granularity, **quota discipline** (reviews re-use the R1 instance and read only the diff from R2 on; engine facts verified once and cited by `EF-nn`; convergence rule; Sonnet closure check for code review r2; task books point at volumes), document authority chain |
| `ai_dev/99_Prompts/_TaskBook.md` | `docs/ai_dev/99_Prompts/` | The mandatory task-book skeleton (volume-level read list, do-not-read list, tool-call budget, report format) and seven templates (review R1 / R2+, drafter revision, dev-plan review, implementation, code review r1/r2, acceptance run) |
| `component/_tech_design_engine_facts.md` + `_tech_design.md` 1.0 + `_project_directory_plan.md` 1.1 / 3.1 rule 7 | component docs | **Design volumes**: split a large design into a main document plus `{component}_tech_design_{volume}.md` files with globally unique chapter numbers, and register third-party engine facts once so nobody re-reads the engine source |

---

## 10 License

Released under **[MIT No Attribution (MIT-0)](./LICENSE)**.

You may use, copy, modify and redistribute these templates for any purpose, commercial or not, with
**no obligation whatsoever** — you do not need to keep a copyright notice, ship the license text, or
credit this project anywhere.

If you would like to, a line such as the following in your own `README.md` is appreciated, but it is
entirely optional and nothing is expected in return:

```markdown
Documentation structure based on [ProjectTemplate_DesignDriven](https://github.com/AliveverCom/ProjectTemplate_DesignDriven).
```

---

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.4.0 | 2026-09-06 | Resident knowledge packs in the five `.claude/agents/` role files (build/test commands, toolchain, coding-standard digest, file map, known pitfalls, review conventions); `_TaskBook.md` 1.1.0 adds the "Shared knowledge pack" section, implementation tool-call cap 35 and tool-call count in reports |
| 1.3.0 | 2026-09-06 | AI team rules and quota discipline: `CLAUDE.md` template, `.claude/agents/` five role definitions, `ai_dev/99_Prompts/_TaskBook.md`, `component/_tech_design_engine_facts.md`, design-volume rules (`_project_directory_plan.md` 1.1 / 3.1 rule 7, `_tech_design.md` 1.0), `general_doc_template.md` header `Version:` line rule, mermaid render fix (`-w 2600 -c mmdc_config.json`) |
| 1.0.0 | 2026-08-23 | First release: platform / component / mermaid / ai_dev template categories, reverse-engineered from the real design documents of a multi-component platform |
