<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] AI development process document. `01_DocReviewRefine` = **document-level**
       review and refine, distinct from the **code-level** review in `03_CodeReviewRefine`.
       This file belongs to the "refine" side of that: not about finding problems, but about
       distilling component-level design into platform-level specification.

[Purpose] Extract reusable common design patterns from a **single** component's UI/UX design,
       for the engineer to decide whether to promote them to a **platform-level** spec written
       into `docs/uiux_design_specification.md`. This is the only official channel from
       component-level design to platform-level specification.

[Why it's needed] The first component to complete UI/UX design naturally produces a large
       number of design decisions (control styles, list interactions, color rules, state
       presentation). If these decisions are not distilled, later components will each write
       their own, and the spec loses effect.

[What this directory holds] Design document consistency review, technical design review,
       frontend/backend design completeness review, UI/UX HTML mockup review, UI/UX common
       pattern extraction.
       Rule of thumb: if the review target is a **.md / .mmd / .html design draft** → put it here;
                       if the review target is **source code** → put it in `03_CodeReviewRefine/`.

[Output directory]
  - Platform level: docs/ai_dev_history/01_DocReviewRefine/
  - Component level: docs/components/{component_code_name}/ai_dev_history/01_DocReviewRefine/
       (the document lives under the component directory, but its conclusions land in the
       platform-level docs/uiux_design_specification.md)

[File naming] `{type}_{YYYYMMDD}[-r{round}].md`, the round number does **not** reset across dates
       (e.g. docs_review_20260310-r6.md → docs_review_20260314-r7.md).
  Naming per type in this directory:
    docs_review_{YYYYMMDD}-r{n}.md                — cross-document consistency review
    tech_design_review_{YYYYMMDD}_r{n}.md         — single-document deep review
    design_completeness_review_{YYYYMMDD}-r{n}.md — frontend/backend design completeness review
    html_review_{YYYYMMDD}-r{n}.md                — HTML mockup vs. uiux document review
    uiux_pattern_summary_{YYYYMMDD}-v{n}.md       — common UI/UX pattern extraction proposal  ← **this file**

[Template file name] The leading `_` is only a template marker. When copying into ai_dev_history/,
       rename it per the rules above and drop the underscore.

[Review Iteration Protocol] See templates/ai_dev/readme.md:
  - The "Engineer's Response" column is always left empty when AI generates the report;
    the engineer decides item by item "adopt / don't adopt / discuss further"
  - Round N's report opens with the previous-round remediation confirmation table, not new issues
  - Fixed issues are not restated, and no distinction is drawn between old and new issues
  - Issue IDs are globally unique and never reused
  - Architectural disagreements must present Option A/B with the recommended one marked
-->

# {ComponentDisplayName} UI/UX Common Design Pattern Summary

**Date**: {YYYY-MM-DD}
**Source**: `{component}_uiux.md` v{x.y.z}
**Purpose**: Extract UI/UX design patterns from the `{component}` module that may be applicable to other modules, for discussion and a decision on whether to fold them into `uiux_design_specification.md`.
**Current Platform Spec Version**: `uiux_design_specification.md` v{x.y.z}

---

## Design Patterns Already Merged into uiux_design_specification.md

The following design patterns have already been written into the global design spec (v{x.y.z}) as part of this update, and are no longer listed as under discussion:

1. **{PatternName1}** → §{n.n.n}
2. **{PatternName2}** → §{n.n.n}
3. **{PatternName3}** → §{n.n.n}
4. **{PatternName4}** → §{n.n.n}
5. **{PatternName5}** → §{n.n.n}

> For patterns already merged, the component-level uiux.md should be changed to reference the platform spec's section number, and the duplicated description removed.

---

## Patterns Under Discussion

The following design patterns are already in use in the `{component}` module and have some degree of generality; discussion is suggested on whether to fold them into the global spec.

| # | Pattern Name | Source Page | Description | Generality Assessment | Engineer's Response |
|---|---------|------|------|-----------|------------|
| 1 | **{PatternName}** | {The {FieldName} field on P02/P04/P06} | {Full description of the pattern: what the control looks like, how the interaction is triggered, how the read-only state appears, what types of fields it applies to} | {High — usable for any numeric field with a range constraint} | |
| 2 | **{PatternName}** | {Global rule on P01/P03/P05} | {Description} | {High — applicable to all list pages} | |
| 3 | **{PatternName}** | {Global rule} | {Description} | {Medium — only applicable to list-type pages} | |
| 4 | **{PatternName}** | {P07} | {Description} | {Low — tightly bound to this module's business, recommend keeping it at the component level} | |

> The "Engineer's Response" column is filled in by the engineer manually. AI must always leave it empty, never guess, and never write a placeholder like "TBD".
> Example response values: "Fold into uiux_design_specification.md" / "Keep at component level, don't promote" / "Discuss further".
> Generality Assessment values: **High** (applicable to most modules) / **Medium** (applicable to similar-type pages) / **Low** (specific to this module).

---

## Suggested Target Sections

For patterns assessed as "High", pre-plan their placement in `uiux_design_specification.md` so the engineer can judge whether the spec structure is reasonable:

| Pattern | Suggested Location | Parent Chapter |
|------|---------|---------|
| {PatternName1} | §{n.n} {Section Title} | {§2 Functional Page Design Patterns / §3 Common UI Controls and Styles / §5 Frontend Color Scheme} |
| {PatternName2} | §{n.n} {Section Title} | {Parent chapter} |

---

## Notes

1. This document only does **extraction and proposal**; it does not directly modify `uiux_design_specification.md`. After the engineer responds, AI writes the changes in per the response and bumps the Change Log version of the platform spec.
2. Once a pattern is merged into the platform spec, the source component's `{component}_uiux.md` must be updated to reference the spec's section number (in the form "follows [uiux_design_specification.md](../../uiux_design_specification.md) §{n.n.n}"), removing the local duplicate description to avoid the two definitions drifting apart.
3. Patterns assessed as "Low" remain in the component-level document, but should still be recorded here for later components to search and reuse when a similar need arises.
4. This document is produced once per component after its UI/UX design is complete; when producing it for a later component, first search the existing pattern summaries to avoid re-extracting the same pattern.
