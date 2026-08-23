# UI/UX Design Specification

**Document Version**: {x.y.z}

<!--
TEMPLATE NOTES (delete this comment block when using)
- Level: **Platform-level UI/UX specification**. Place at the `docs/` root; drop the leading underscore from the filename → `uiux_design_specification.md`.
- Distinction from component-level `{component}_uiux.md` (this boundary must be maintained):

  | Dimension | This document (platform-level uiux_design_specification.md) | Component-level {component}_uiux.md |
  |------|-------------------------------------------|---------------------------|
  | What it describes | **Page patterns** (list pages / detail pages / dialogs) and **common controls** | Page-by-page design for **specific pages** (P01, P02, …) |
  | Content | Common rules, interaction conventions, color scheme, tech stack | Page inventory, page flow diagrams, per-page element and behavior tables |
  | Reference direction | Referenced by component docs | Each page states "follows §2.x.x of this document" at the top |
  | On conflict | **This document takes precedence** | If a component needs an exception, it must be explicitly stated and justified on that page |

- Component docs **must not redefine** patterns and controls already defined here — they may only reference the section number.
- If a component distills a new common pattern, follow the `ai_dev_history/01_DocReviewRefine/_uiux_pattern_summary.md`
  process; once confirmed by an engineer, **fold it into this document** and remove the duplicate definition from the component doc.
- It is recommended to add an anchor to each section, e.g. `<a id="211-single-list-page"></a>`, so component docs can reference it precisely.
- This document uses `# level-1 headings` for sections; the first section is the Table of Contents (unnumbered).
-->

---

## Table of Contents

- [1 Document Overview](#1-document-overview)
- [2 Functional Page Design Patterns](#2-functional-page-design-patterns)
  - [2.1 List Pages](#21-list-pages)
    - [2.1.1 Single-List Page](#211-single-list-page)
    - [2.1.2 Dual-List Page](#212-dual-list-page)
    - [2.1.3 Tree Single-List Page](#213-tree-single-list-page)
    - [2.1.4 Tree Dual-List Page](#214-tree-dual-list-page)
  - [2.2 Business Object Detail Page](#22-business-object-detail-page)
    - [2.2.1 Business Object Create Page](#221-business-object-create-page)
    - [2.2.2 Business Object Read-Only Page](#222-business-object-read-only-page)
    - [2.2.3 Business Object Edit Page](#223-business-object-edit-page)
  - [2.3 Dialogs](#23-dialogs)
    - [2.3.1 Quick Info Popover](#231-quick-info-popover)
    - [2.3.2 Blocking Message Dialog](#232-blocking-message-dialog)
- [3 Common UI Controls and Styles](#3-common-ui-controls-and-styles)
  - [3.1 Multi Tags](#31-multi-tags)
    - [3.1.1 Multi Tags Edit Control](#311-multi-tags-edit-control)
    - [3.1.2 Multi Tags Style in Lists](#312-multi-tags-style-in-lists)
    - [3.1.3 Tag Cloud Read-Only Display](#313-tag-cloud-read-only-display)
  - [3.2 Active and IsDeleted](#32-active-and-isdeleted)
    - [3.2.1 List Style for Inactive State](#321-list-style-for-inactive-state)
    - [3.2.2 List Style for Soft-Deleted State](#322-list-style-for-soft-deleted-state)
  - [3.3 Slider + Number Input Linked Control](#33-slider--number-input-linked-control)
  - [3.4 Dynamic Chip Selector](#34-dynamic-chip-selector)
  - [3.5 Tag-style Time Input Control](#35-tag-style-time-input-control)
  - [3.6 Foreign-Key Search Select Control (Autocomplete)](#36-foreign-key-search-select-control-autocomplete)
- [4 User Interaction](#4-user-interaction)
- [5 Frontend Color Scheme](#5-frontend-color-scheme)
- [6 Frontend and API Interaction](#6-frontend-and-api-interaction)
- [7 Tech Stack](#7-tech-stack)
- [8 State Management](#8-state-management)
- [9 Data Formatting Rules](#9-data-formatting-rules)
- [10 Real-Time Refresh Strategy](#10-real-time-refresh-strategy)
- [11 Client-Side Caching Strategy](#11-client-side-caching-strategy)
- [12 Loading State](#12-loading-state)
- [13 Empty Table State](#13-empty-table-state)
- [14 Responsive Design and Browser Support](#14-responsive-design-and-browser-support)
- [15 Performance Optimization](#15-performance-optimization)
- [16 Error Handling and Display](#16-error-handling-and-display)
- [17 Frontend Testing Strategy](#17-frontend-testing-strategy)
- [18 Directory and File Naming Conventions](#18-directory-and-file-naming-conventions)
  - [18.1 Naming Style](#181-naming-style)
  - [18.2 Page ID Numbering Rules (Migration Scenarios)](#182-page-id-numbering-rules-migration-scenarios)
- [Change Log](#change-log)

---

<a id="1-document-overview"></a>

# 1 Document Overview

This document provides the design specification for **page layout, color scheme, and interaction** across all functional pages. All frontend design within the platform must follow this specification.

The UI/UX design documents for individual product components (e.g. `{component_code_name}_uiux.md`) describe the **detailed design of specific pages**;
this document defines the **common rules and patterns that those specific designs must follow**.

**How to use**: At the top of each page section, the component UI/UX document should include a one-line design pattern declaration, for example:

> **Design pattern**: Follows [uiux_design_specification.md](./uiux_design_specification.md) §2.1.2 Dual-List Page.

After that, only describe the parts of the page that **differ from the pattern's defaults** — do not re-copy the pattern itself.

---

<a id="2-functional-page-design-patterns"></a>

# 2 Functional Page Design Patterns

> Each pattern in this chapter is described using the following consistent skeleton: **Description / Applicable Scenarios → Layout → Area Composition (Filter Area,
> Action Button Area, List Area, Row Interaction) → Element Table → Interaction Table → Hard Rules**.
> Every page in a component document must map to one of the pattern numbers in this chapter.

<a id="21-list-pages"></a>

## 2.1 List Pages

List pages are used for **bulk browsing and querying** of a primary business object, while also providing functional entry points for **creating, modifying, and deleting** that object.

| Pattern | Section | Applicable Scenarios |
|------|------|---------|
| Single-List Page | §2.1.1 | {Default list for a single business object; the basis for other variants} |
| Dual-List Page | §2.1.2 | {Linked browsing of a parent object + its child objects} |
| Tree Single-List Page | §2.1.3 | {The business object itself has a hierarchical relationship} |
| Tree Dual-List Page | §2.1.4 | {The parent object is hierarchical + a child object list} |

---

<a id="211-single-list-page"></a>

### 2.1.1 Single-List Page

**Description**: The default list page pattern for a single business object; also the foundational setup for all other list page variants.

**Layout (top to bottom)**: Title Area → Filter Area → Action Button Area → List Area.

#### 1. Filter Area

| # | Rule | Notes |
|---|------|------|
| a | Criteria selection | Provide the **most important attributes** of the object as filter criteria |
| b | Default criteria | Typically provide **id** and a **date range** by default |
| c | Two date-range designs | **Option 1**: a calendar control for selecting start/end dates; **Option 2**: a calendar control + a quick-select dropdown ({Today / Last 3 Days / Last Week / Last Month}). The default value for both options is **{Today}** |
| d | Trigger method | Filtering is triggered automatically every time a control **loses focus** (blur / value change) — **no** search button is needed; only provide a single "Clear Filters" entry point |
| e | Page initialization | On first entry: all criteria other than the date range are empty, the date range is {Today}, and filtering is applied automatically. **If the page has no date criterion**, the first entry displays **{50}** records with no criteria applied |

#### 2. Action Button Area

After the user selects data, these buttons perform standardized operations on it. Default buttons:

| # | Button | Precondition | Behavior |
|---|------|---------|------|
| 1 | **Create** | No selection required | Navigate to a **blank create detail page** |
| 2 | **Delete** | Data must be selected, otherwise show an error | First show a confirmation dialog "Are you sure you want to delete the selected n record(s)?"; choosing "Yes" performs the deletion; choosing "No" closes the dialog and **keeps the current selection** |
| 3 | **View** | Data must be selected, otherwise show an error | Navigate to the **read-only detail page** of the **first** selected object |
| 4 | **Edit** | Data must be selected, otherwise show an error | Navigate to the **edit detail page** of the **first** selected object |

> Components may add or remove buttons, but any addition or removal must be justified in the component document (e.g. "material records are collected automatically by a daemon, so no create/edit is provided").

#### 3. Sorting Rules

| # | Rule | Notes |
|---|------|------|
| a | Default sort | Sorted by **creation time descending**; if there is no creation time, sorted by **id descending** |
| b | Header sort | Clicking a column header switches the sort column; repeatedly clicking the same column toggles between **ascending/descending** |
| c | Direction indicator | The header shows a **small arrow** indicating the current sort column and direction |
| d | Multi-column sort | **Not supported** — single-column sort only |
| e | Special sort control | When there is a special sorting need, provide a sort dropdown control placed at the **far left of the action button row**, separated from the action buttons by a **divider**; selecting a value **immediately re-queries** |
| f | Where sorting executes | All sorting sends a request to the **backend API**; **frontend local sorting is prohibited** (it causes logical inconsistencies when pagination is involved) |
| g | Inheritance rule | All derived list pages inherit this section's sorting rules by default. When a column contains multiple attribute rows, clicking the header sorts by the column's **first attribute row**. The Tree Single-List Page is the exception (§2.1.3 has no sorting) |

#### 4. List Area

| # | Rule | Notes |
|---|------|------|
| a | Column count | No more than **{7}** columns by default; the first column is **id**, the last column is the **date** |
| b | Action button column | **Not provided**; use the Action Button Area or right-click menu instead |
| c | Footer info | **Summary info** uses the uniform format `Selected {int} / Total {int}` (e.g. `Selected 3 / Total 128`) + **pagination controls**; a page-size selector is **not provided** — fixed at **{50}** records per page |
| d | Single select | Click a row with the mouse to select it |
| e | Multi-select | **Ctrl / Shift + click** |
| f | Double-click | Navigate to the record's **read-only detail page** |
| g | Integer ID display | Integer IDs **display only the number itself**, with **no `#` prefix** (`10293`, not `#10293`) |
| h | Compact column format | When multiple fields are compactly displayed in one column, use "prefix marker + slash separator", e.g. `c{v}/p{v}/e{v}` → `c80/p-10/e20`, `{days}d/{count}n` → `120d/35n`. Must be paired with value-based coloring (§5) and a Tooltip |
| i | Tooltip | Hovering over each numeric segment of a compact column shows the field's full name and current value (e.g. `80` → "Content Credibility: 80"). Exact wording is defined in the component document |
| j | Refresh after returning from detail page | Detail page returns an **object ID** (create/edit succeeded) → the list refreshes and **highlights that row**; returns **`null`** (no change/cancelled) → the current state is left unchanged |

#### 5. Right-Click Menu

The right-click menu content is **identical** to the Action Button Area.

#### 6. Scrollbars

Both horizontal and vertical scrollbars apply to the **entire page**, not to sub-areas. The page header and footer **do not use** `sticky` / `fixed`;
the whole page scrolls with the user. This rule also applies to Business Object Detail Pages. **The Dual-List Page is the exception** (see §2.1.2 §3).

---

<a id="212-dual-list-page"></a>

### 2.1.2 Dual-List Page

**Description**: Applies when a parent object list and the full list of child objects belonging to a selected parent object need to be shown together.

**Layout**: Title area at the top; the parent object list area at the bottom left; the child object list area at the bottom right.

#### 1. Parent Object List Area (Simplified Single-List Page)

| # | Rule | Notes |
|---|------|------|
| a | Column count | **1** column by default, **3** columns maximum |
| b | Wrapped display | Important attributes wrap within the same column, up to **3** lines per column; **the first line must be the most important information**, other lines use a smaller or lighter font |
| c | Filter criteria | Typically only **1–3** are provided; **keyword search** is a default required criterion |
| d | Action buttons | Due to limited space, **icon buttons** are used by default instead of text buttons |
| e | Footer info | Total record count and selected count are **not needed**; pagination controls provide only **Previous/Next + current page number** |
| f | Linking rule | When a row is single- or multi-selected, the right-hand area automatically **clears all filter criteria** and refreshes its data based on the **first** selected parent object |
| g | Operates on | The action buttons in this area operate on the **parent object** |

#### 2. Child Object List Area (Standard Single-List Page)

| # | Rule | Notes |
|---|------|------|
| a | Implicit filter criterion | The parent object's id is stored in this area as an **implicit filter criterion**, which every query must include |
| b | Visible filter criteria | Typically **1–3** |
| c | Column count | No more than **5** columns |

#### 3. Scrollbars

The left and right sides **scroll independently of each other**; there is **no main-view scrollbar**, and no scrollbar on the list control itself —
instead, each scrolls as a whole within its own page area.

---

<a id="213-tree-single-list-page"></a>

### 2.1.3 Tree Single-List Page

**Description**: A variant of the Single-List Page, differing in that the business object has a self-referencing hierarchical relationship.

| # | Rule | Notes |
|---|------|------|
| a | First column is a tree | The first column displays the hierarchy as an expandable tree; only the **first level** is shown by default |
| b | Sorting | **No sorting feature**; order is determined by the tree structure |
| c | Configuration-type objects | **No filtering feature** by default; all objects are loaded and displayed directly |
| d | Business-data-type objects | Filtering may be provided normally, but it applies only to the **top-level node objects** |
| e | Hierarchy field | The object typically has a `parent_id` attribute; `parent_id = null` denotes a root node |
| f | Node icons | Provide distinct icons for **root / branch / leaf** to make the hierarchy easy to recognize |

---

<a id="214-tree-dual-list-page"></a>

### 2.1.4 Tree Dual-List Page

**Description**: A variant of the Dual-List Page, differing in that the parent business object has a self-referencing hierarchical relationship.

| # | Rule | Notes |
|---|------|------|
| a | Parent area | The parent list becomes a **Tree Single-List** (see §2.1.3); everything else fully follows §2.1.2 |

---

<a id="22-business-object-detail-page"></a>

## 2.2 Business Object Detail Page

**Description**: A page for a specific business object instance, used to display or edit all of that instance's relevant attributes.

**Common layout (top to bottom)**: Page Title → Object Attribute Area → Action Button Area → Child Object List Area (optional).

| # | Item | Rule |
|---|------|------|
| a | Page title | `{ObjectType} - {id}` |
| b | Object attribute area | Designed as a single- or two-column layout depending on the number of attributes; each cell holds one attribute, showing the attribute name + value control |
| c | Action button area | Provides **Preview, Edit, Save, Delete, Exit** by default; Preview and Edit are **mutually exclusive**, chosen based on the current page's business characteristics |
| d | Child object list area | When a child object list exists, this area contains a standard Single-List Page whose implicit filter criterion is the current object. If there are **multiple different types** of child object lists, provide **tabs** at the top of the area, each tab containing a complete standard Single-List Page (independent filter criteria, action buttons, pagination); tabs are **loaded on demand** when switched |
| e | Presentation form | By default a **standalone page** occupying the platform frame's main view area; when there are very few attributes it may appear as a **dialog** |

**Special notes**:

| # | Scenario | Rule |
|---|------|------|
| 1 | tags attribute | tags are **not** a child object list; they should be represented as a **Multi Tags control** (§3.1) |
| 2 | Foreign-key id editing | By default, display the **object name** corresponding to the foreign key; when editing, use a dropdown selector, or type a keyword to search and show the top **{7}** results (§3.6) |
| 3 | Parent object reference is read-only | If the current object belongs to a parent object, the parent reference field (e.g. `{parent}_id`) is **always read-only** in **all modes** (create/read-only/edit), and shows the parent object's `display_name` rather than its ID. A child object **cannot be moved to a different parent** |
| 4 | Many-to-many relationship management | When a child object list represents a **many-to-many relationship** (rather than ownership), the buttons are "**Add**/**Remove**" instead of "Create/Delete". "Add" opens a simplified selection dialog to choose from existing objects; "Remove" only breaks the relationship — it **does not delete the object itself** |
| 5 | `created_at` / `updated_at` styling | The two fields are shown on the **same row**, in a two-column layout; label text is "Created At" / "Updated At", with **no** "(Read-only)" suffix appended; specific style classes for label and value: {label style} / {value style} |

---

<a id="221-business-object-create-page"></a>

### 2.2.1 Business Object Create Page

**Description**: Used to create a new business object. Layout follows the §2.2 default specification.

| # | Special characteristic | Rule |
|---|---------|------|
| a | Empty and non-editable attributes | id/primary key, creation time, last modified time, created-by user, last-modified-by user |
| b | Child object list area | **Not provided** (the parent object does not yet exist, so its child list cannot be edited) |
| c | Action buttons | Only "**Create**" and "**Cancel**" are provided. Clicking Create → calls the backend to create the object and retrieves the new object id; clicking Cancel → closes or returns to the previous page |
| d | On successful creation | The current page/dialog closes automatically, returns to the previous page, and the new object id is **returned to the previous page** |
| e | On creation failure | **Stays on the current page**, shows an error message, and **retains what the user has already entered** until creation succeeds or the user clicks Cancel |

---

<a id="222-business-object-read-only-page"></a>

### 2.2.2 Business Object Read-Only Page

**Description**: Displays a business object in read-only mode. Layout follows the §2.2 default specification.

| # | Special characteristic | Rule |
|---|---------|------|
| a | Control state | All controls on the page are **read-only** |
| b | Action buttons | Provides "**Edit**", "**Delete**", "**Exit**" by default; the Delete button is shown only if the current user has delete permission |
| c | Button behavior | Clicking Edit → closes the current page and navigates to the **edit page**; clicking Exit → closes or returns to the previous page |
| d | Delete result | **Success**: closes the current page and returns **`null`**. **Failure**: **stays on the current page without exiting**, until deletion succeeds on a retry or the user leaves via another button |
| e | Child object list area | This area may normally provide full query, preview, edit, create functionality targeting the **child objects** (unless permission is lacking) |

---

<a id="223-business-object-edit-page"></a>

### 2.2.3 Business Object Edit Page

**Description**: Edits all editable attributes of an existing business object. Layout follows the §2.2 default specification.

| # | Special characteristic | Rule |
|---|---------|------|
| a | Read-only attributes | id/primary key, creation time, last modified time, created-by user, last-modified-by user |
| b | Remaining attributes | All rendered as corresponding editable attribute controls |
| c | Leave-page prompt | Follows the general "Modification and Save-State Monitoring" rule in §4 item 1 |
| d | Child object list area | May normally provide full functionality targeting child objects (unless permission is lacking) |

**Action buttons**:

| Button | Availability | Behavior |
|------|---------|------|
| **Save** | **Visible but not clickable** when nothing has been modified | Does **not** automatically exit after saving — the user can continue editing |
| **Delete** | Always available | If there are unsaved changes, first prompt "Content has been modified but not saved — delete anyway?"; choosing "Yes" proceeds to the delete confirmation flow, choosing "No" returns to the editing state. If there are no changes, or changes are already saved, go directly to the delete confirmation flow. On successful deletion → closes the page and returns **`null`**; on failure → stays on the current page |
| **Exit** | Always available | See the return-value rules below |
| **Preview** | Clickable only when **unmodified or already saved**; otherwise visible but not clickable | Switches to the read-only page |

**Return value on exit**:

| Situation | Return value |
|------|--------|
| Edited and saved at least once | The business object's **id** |
| Never modified | **`null`** |
| Modified but not saved (after being handled per the §4 rule) | **`null`** |

---

<a id="23-dialogs"></a>

## 2.3 Dialogs

**Description**: A small window that pops up on top of the current page without navigating away from it.

---

<a id="231-quick-info-popover"></a>

### 2.3.1 Quick Info Popover

**Applicable scenario**: Used only to show brief success messages (e.g. "Saved successfully") or short messages of **3 lines or fewer**.

| # | Rule |
|---|------|
| a | Appears in the **exact center of the screen** and disappears automatically after **{3}** seconds |
| b | Has **no** buttons or icons of any kind — only text is shown |
| c | While it is shown, the user **can still interact normally** with the main view and the platform frame — it is **non-blocking** |

---

<a id="232-blocking-message-dialog"></a>

### 2.3.2 Blocking Message Dialog

**Applicable scenario**: A window and interaction form identical to an OS-level MessageDialog; also the foundation for other dialog variants.

| # | Rule |
|---|------|
| a | While shown, both the platform frame and the main view are **blocked** — the user can only interact with this window |
| b | Has optional **icons**, **buttons**, and a **return value** |

---

<a id="3-common-ui-controls-and-styles"></a>

# 3 Common UI Controls and Styles

**Description**: This chapter describes commonly used complex UI controls and their styling specifications. When describing specific pages, component UI/UX documents should **reference this chapter's section numbers directly**,
rather than redefining them.

> Each control is described using a consistent skeleton: **Applicable Scenario → Appearance → Interaction → Data Constraints → Read-Only State**.

---

<a id="31-multi-tags"></a>

## 3.1 Multi Tags

Multi Tags is a control for displaying or editing a set of tag values. It appears as an **editable control** on detail pages,
and as a **read-only compact style** on list pages.

---

<a id="311-multi-tags-edit-control"></a>

### 3.1.1 Multi Tags Edit Control

| Item | Specification |
|------|------|
| **Applicable scenario** | Attributes of type `Tag Input` on detail pages (e.g. `{prog_tags}`, `{aud_tags}`) |
| **Appearance** | {Each tag is a rounded badge containing text + a `×` remove icon; an input box follows at the end} |
| **Adding a tag** | {Type text and press {Enter} or {comma} to submit; duplicate values are ignored automatically} |
| **Removing a tag** | {Click the `×` on the badge; or press Backspace with an empty input box to remove the last tag} |
| **Color** | Bright colors; **the same tag name uses the same color platform-wide** (determined by a hash of the name) |
| **Data constraints** | {case handling, max length, max count, allowed characters} |
| **Read-only state** | {Only badges are shown, with no `×` and no input box} |

---

<a id="312-multi-tags-style-in-lists"></a>

### 3.1.2 Multi Tags Style in Lists

| Item | Specification |
|------|------|
| **Applicable scenario** | A list page column needs to display tags |
| **Appearance** | {Compact small badges, font size {12px}} |
| **Overflow handling** | {Show at most {n}, collapse the rest into `+{m}`, hover Tooltip shows all} |
| **Color** | Uses the same name→color mapping as §3.1.1 |
| **Interaction** | {Tags in the list are read-only and cannot be edited} |

---

<a id="313-tag-cloud-read-only-display"></a>

### 3.1.3 Tag Cloud Read-Only Display

| Item | Specification |
|------|------|
| **Applicable scenario** | {Displaying the overall distribution of a group of tags on a read-only detail page or dashboard} |
| **Appearance** | {A group of badges laid out with automatic wrapping; optionally sized by weight/frequency} |
| **Interaction** | {Read-only; optionally clicking navigates to a list page filtered by that tag} |

---

<a id="32-active-and-isdeleted"></a>

## 3.2 Active and IsDeleted

The platform uniformly uses two orthogonal states, `is_active` (enabled flag) and `deleted_at` (soft-delete timestamp), to describe record availability.
The list styles for the two must be distinguishable at a glance.

| State combination | Meaning | Style |
|---------|------|------|
| `is_active = true`, `deleted_at = null` | Normal | {Default style} |
| `is_active = false`, `deleted_at = null` | Deactivated | See §3.2.1 |
| `deleted_at != null` | Soft-deleted | See §3.2.2 |

---

<a id="321-list-style-for-inactive-state"></a>

### 3.2.1 List Style for Inactive State

| Item | Specification |
|------|------|
| **Applicable scenario** | The list row's record has `is_active = false` |
| **Appearance** | {The entire row's text turns {gray}; optionally append an `Inactive` badge after the name column} |
| **Interaction** | {Can still be selected, viewed, edited; whether specific operations are allowed is defined in the component document} |

---

<a id="322-list-style-for-soft-deleted-state"></a>

### 3.2.2 List Style for Soft-Deleted State

| Item | Specification |
|------|------|
| **Applicable scenario** | The list row's record has `deleted_at != null` |
| **Appearance** | {The entire row is {gray + strikethrough}; a `Deleted` badge is appended after the name column; the `deleted_at` time may be shown} |
| **Shown by default** | {Determined by each list page's `include_deleted` query parameter, default {false}} |
| **Interaction** | {Read-only, editing not allowed; whether restoration is allowed is defined in the component document} |

> **Important**: Soft-deleted records **do not disappear** from the list — they are shown in this style instead. When component documents describe delete behavior,
> they must state clearly that "after calling the DELETE API the record does not disappear; instead `deleted_at` becomes non-null and it is shown in the gray style."

---

<a id="33-slider--number-input-linked-control"></a>

## 3.3 Slider + Number Input Linked Control

| Item | Specification |
|------|------|
| **Applicable scenario** | Numeric attributes with a clearly defined value range (e.g. various scores, weights) |
| **Appearance** | {Slider on the left + number input box on the right} |
| **Interaction** | **Two-way binding**: dragging the slider updates the number in real time; typing a number directly updates the slider position in real time |
| **Data constraints** | {min / max / step; values are clamped to the boundary and a warning is shown when out of range} |
| **Color** | Value coloring follows the §5 value-threshold coloring rules |
| **Read-only state** | **Shows only the number**, without the slider |

---

<a id="34-dynamic-chip-selector"></a>

## 3.4 Dynamic Chip Selector

| Item | Specification |
|------|------|
| **Applicable scenario** | Multi-selecting from a **finite, known** set of candidate options (e.g. days of the week, frequency, category) |
| **Appearance** | {A row of clickable chip buttons; selected and unselected states are distinguished by different colors} |
| **Interaction** | {Clicking toggles selected/unselected; optional "Select All / Clear" shortcut} |
| **Data constraints** | {min/max number of selections; source of candidate options (enum or API)} |
| **Read-only state** | {Only selected chips are shown; unselected items are not shown} |

---

<a id="35-tag-style-time-input-control"></a>

## 3.5 Tag-style Time Input Control

| Item | Specification |
|------|------|
| **Applicable scenario** | Attributes requiring entry of a **set of time points** (e.g. a list of daily scheduled trigger times) |
| **Appearance** | {Entered times are arranged as badges, with a time input box at the end} |
| **Interaction** | {Type `HH:mm` and press {Enter} to add; click a badge's `×` to remove it; duplicates are removed automatically and entries are sorted ascending by time} |
| **Data constraints** | {Format `HH:mm` (24-hour); invalid input is flagged immediately; maximum count {n}} |
| **Read-only state** | {Only badges are shown, with no input box} |

---

<a id="36-foreign-key-search-select-control-autocomplete"></a>

## 3.6 Foreign-Key Search Select Control (Autocomplete)

| Item | Specification |
|------|------|
| **Applicable scenario** | Editing a foreign-key id attribute on a detail page (see §2.2 Special Notes item 2) |
| **Appearance** | {A single-line input box; a candidate list floats below it on focus} |
| **Interaction** | {Type a keyword → calls the backend search → shows the names of the top **{7}** results → the user clicks to select one} |
| **Displayed value vs. stored value** | Displays the foreign-key object's **`display_name`**, stores its **id** |
| **Data constraints** | {whether empty is allowed; minimum character count to search; debounce interval} |
| **Read-only state** | Shows only the foreign-key object's `display_name` (not the id) |

---

<a id="4-user-interaction"></a>

# 4 User Interaction

**Description**: This chapter defines common user interaction design specifications.

| # | Rule | Notes |
|---|------|------|
| 1 | **Modification and save-state monitoring** | Every page with editing functionality has modification/save-state monitoring by default. If the user tries to leave after editing without saving, the page **must immediately prompt**: "This page has unsaved changes — save before exiting?" Choosing "Yes" → **saves first, then exits**; choosing "No" → **discards changes and exits directly**. This is a platform-wide rule; §2.2.3 and other pages all follow this item |
| 2 | **List control behavior** | All list controls use single-select, multi-select (Ctrl / Shift), double-click, and right-click behavior consistent with {Windows / macOS} |
| 3 | **File upload** | Defaults to **drag-and-drop from another application into the page**. After dropping, show the file's **path and size**; whether a file has already been dropped should be indicated with a **different color** |
| 4 | **Feedback for long-running operations** | For operations that may take longer than **{3}** seconds, a **cursor change** indicating processing must be shown; if it still has not finished after **{10}** seconds, switch to a "Quick Info Popover" (§2.3.1) informing the user that processing is taking longer than usual and to check back later for the result |

---

<a id="5-frontend-color-scheme"></a>

# 5 Frontend Color Scheme

**1. Theme**: Defaults to the **{Light}** color style.

**2. CRUD button color rules** (followed uniformly by text buttons and icon buttons on all pages):

| Action type | Color | Applicable buttons |
|---------|------|---------|
| Delete / Cancel | 🔴 {Red} | Delete, Cancel, Remove |
| Save | 🟢 {Green} | Save, Save Changes |
| View / Exit | ⬜ {Gray} | View, Preview, Exit, Close |
| Create / Add / Edit | 🟦 {Blue} | Create, Add, Edit |

**3. tags color**: Uses relatively **bright** colors, and **the same tag name always has the same color** (see §3.1 for details).

**4. Disabled state**: All non-editable or non-clickable controls must be displayed in a **distinct color style** so users can tell at a glance that they are not interactive.

**5. Value-threshold coloring rules** (four-color threshold model):

| Color | Meaning | Applicable scenario |
|------|------|---------|
| 🟢 {Green} | Excellent / Healthy | Value is in the optimal range |
| 🔵 {Blue} | Good / Normal | Value is in an acceptable range |
| 🟠 {Orange} | Warning / Low | Value is approaching a risk zone |
| 🔴 {Red} | Danger / Abnormal | Value is in a poor range |

> **The specific threshold ranges are defined by each component's UI/UX document based on its business needs**; this document only defines the meaning and usage of the four colors.
> Value-based coloring applies to numeric displays in list and detail pages, and is typically used together with the **compact column format** (§2.1.1 §4.h) and **Tooltip** (§2.1.1 §4.i).

---

<a id="6-frontend-and-api-interaction"></a>

# 6 Frontend and API Interaction

| # | Rule |
|---|------|
| 1 | All frontend-backend interaction must go through the **backend API** |
| 2 | The frontend is **strictly prohibited** from directly accessing the backend database or file system |
| 3 | All pages (including list pages) obtain business objects or object lists only through **standard API endpoints**. If an attribute is a foreign-key id, the frontend fetches the foreign-key object(s) by id via the API — **individually or in batch** — and then displays its name |
| 4 | Unless there is a special design note (e.g. a statistics/report page), providing a dedicated API for each individual page is **prohibited**. All create/read/update/delete operations must operate on the API **per business object**, not per page. The more custom APIs there are, the worse the long-term maintainability |

> Shares the same source as [technical_overview.md](./technical_overview.md) §4.1 item 11 — the two must remain consistent.

---

<a id="7-tech-stack"></a>

# 7 Tech Stack

| # | Rule |
|---|------|
| 1 | **Web applications** are developed using **{React}** by default |
| 2 | **Desktop applications** default to the operating system's **native development framework**; cross-platform frontend frameworks should be avoided |

**Fixed frontend tech stack versions** (followed uniformly by all Web applications):

| Dependency | Version | Notes |
|------|------|------|
| {React} | **{19.0.0}** | UI framework |
| {Vite} | **{6.2.0}** | Build tool |
| {TypeScript} | **{5.7.3}** | {strict mode} |
| {React Router} | **{7.x}** (latest) | Routing |
| {UI component library} | **{5.x}** (latest) | UI component library |
| {Zustand} | **{5.x}** (latest) | Lightweight state management |
| {Axios} | **{1.x}** (latest) | HTTP client |
| {pnpm} | **{9.x}** (latest) | Package manager (Monorepo workspace) |

> The above are the project's locked versions: {React, Vite, and TypeScript are locked to the patch level; the rest are locked to the major level,
> using the latest patch under that major}. This table must remain consistent with [technical_overview.md](./technical_overview.md) §5.2.

---

<a id="8-state-management"></a>

# 8 State Management

| # | Rule |
|---|------|
| 1 | All Web applications uniformly use **{Zustand}** to manage frontend state |
| 2 | **Store-splitting strategy**: Split by business entity. Each primary business object corresponds to its own independent Store file |
| 3 | **Store content**: The entity's list data, pagination parameters, filter criteria, current selection, and the corresponding actions (fetch / create / update / delete) |
| 4 | **Global UI Store**: An additional `uiStore` is created to manage global UI state (e.g. sidebar expanded/collapsed, current system language) |
| 5 | **Local state**: Temporary state during form editing uses component-local `useState` and is **not placed** in a global Store |

---

<a id="9-data-formatting-rules"></a>

# 9 Data Formatting Rules

> All data formatting conversion is done on the **frontend**; the backend API returns raw values.

| Data type | Formatting rule |
|---------|-----------|
| **Floating-point number** | Shows **{1}** decimal place by default (e.g. `3.1`, `0.0`) |
| **Date-time** | Uniformly uses the `{yyyy-MM-dd HH:mm:ss}` format (e.g. `2026-03-08 14:30:05`) |
| **Date (date only)** | Uniformly uses the `{yyyy-MM-dd}` format |
| **Enum value** | Displays the enum value's **name** directly (e.g. `NEWS_FEED`); **no** translation or label mapping is applied |
| **Boolean** | Shown as the corresponding UI control state (Switch toggle, Active/Deleted badge); the words true/false are **not shown** |
| **Integer** | Shows the raw number directly, with **no** thousands separator |
| **Empty value** | {Uniformly displayed as a gray `—`} |

---

<a id="10-real-time-refresh-strategy"></a>

# 10 Real-Time Refresh Strategy

| # | Rule |
|---|------|
| 1 | {Automatic real-time refresh is **not provided**} |
| 2 | {All data updates on list and detail pages rely entirely on manual user action: clicking a refresh button, re-entering the page, or the current page auto-reloading after a form submission} |
| 3 | {WebSocket, polling, and Server-Sent Events are **not used**} |

---

<a id="11-client-side-caching-strategy"></a>

# 11 Client-Side Caching Strategy

| # | Rule |
|---|------|
| 1 | {The client does **not** perform any API response caching} |
| 2 | {Every page load, page switch, and list refresh fully requests the latest data from the backend} |
| 3 | {Navigation between pages (list page → detail page → back) does not cache the previous list data; the data is re-requested on return} |

---

<a id="12-loading-state"></a>

# 12 Loading State

| # | Rule |
|---|------|
| 1 | {A Loading state is shown only during **list query** operations} |
| 2 | {Implementation: show a simple **Spinner** in the list data area; skeleton screens are not used} |
| 3 | {Other operations (form submission, detail page loading, deletion) are **not given** additional Loading UI} |

---

<a id="13-empty-table-state"></a>

# 13 Empty Table State

| # | Rule |
|---|------|
| 1 | When the list has no data, the **header** and an empty row area are still shown normally |
| 2 | The default height of an empty table is **{10}** rows |
| 3 | When the data exceeds the default height, the table's height grows with the actual row count (**no** maximum height limit) |
| 4 | An internal vertical scrollbar for the table is **not provided**; the table always displays all rows in full, and the page scrolls as a whole |
| 5 | {No "No data" icon, message text, or "Create" shortcut button is **shown**} |

---

<a id="14-responsive-design-and-browser-support"></a>

# 14 Responsive Design and Browser Support

| # | Rule |
|---|------|
| 1 | {This system is a Web application that supports only **desktop browsers**; mobile layout and interaction are out of scope} |
| 2 | {Only **{Chrome}** (latest stable version) is supported; compatibility with other browsers is not guaranteed} |
| 3 | {Accessibility (WCAG) compliance is **not required**; ARIA labels, keyboard navigation, and screen-reader support are not mandated} |
| 4 | {Responsive breakpoints are **not used** in the frontend code; all pages are designed only for desktop width} |

---

<a id="15-performance-optimization"></a>

# 15 Performance Optimization

| # | Rule |
|---|------|
| 1 | {The current version **does not require** any performance optimization measures — implement it as simply as possible} |
| 2 | {Route-level lazy loading is **not used**} |
| 3 | {memo-style optimizations are **not used**, unless an obvious performance problem is encountered} |
| 4 | {Code splitting or bundle-size optimization is **not done**} |

> This chapter is an **explicit statement of what is deliberately left out**. Stating clearly what is **not** done is just as important as stating what **is** done —
> otherwise an AI developer will introduce lazy loading, memoization, and code splitting on their own initiative.

---

<a id="16-error-handling-and-display"></a>

# 16 Error Handling and Display

| # | Rule |
|---|------|
| 1 | All errors returned by the backend API are uniformly shown through a **Blocking Message Dialog** (§2.3.2) |
| 2 | Usage: an error dialog pops up in the exact center of the platform frame, blocking all views behind it, and closes only after the user clicks "Confirm" |
| 3 | The dialog content displays the backend's returned `message` field; the title is "{System Error}" |
| 4 | {Displaying different UI styles by error code is **not needed** — all errors use the same Blocking Message Dialog} |

---

<a id="17-frontend-testing-strategy"></a>

# 17 Frontend Testing Strategy

| # | Rule |
|---|------|
| 1 | {The frontend **does not require** automated testing (no unit tests, no integration tests, no E2E tests)} |
| 2 | {All frontend functionality is tested by **engineers manually clicking through it**} |
| 3 | {A testing framework is **not introduced**} |

---

<a id="18-directory-and-file-naming-conventions"></a>

# 18 Directory and File Naming Conventions

<a id="181-naming-style"></a>

## 18.1 Naming Style

| Scope | Naming style | Example |
|------|---------|------|
| All `docs/` directories and files | **snake_case** | `{component_code_name}/`, `{component_code_name}_uiux.md` |
| All backend code (`src/backend/`) | **snake_case** | `{component_code_name}/`, `source_media_handler.go` |
| All frontend code (`src/frontend/`) | **kebab-case** | `{component-code-name}/`, `source-media-list.tsx` |
| `ui_page_design/` subdirectory | Free format | `P{nn}  {PageName}/` (generated by a third-party tool, snake_case not enforced) |

> **Exception**: common filenames such as `VERSION`, `README.md`, `.gitkeep` remain unchanged.

<a id="182-page-id-numbering-rules-migration-scenarios"></a>

## 18.2 Page ID Numbering Rules (Migration Scenarios)

When ownership of a UI page migrates from one module to another, follow these numbering principles:

| # | Rule |
|---|------|
| 1 | **Source module**: The original page ID (e.g. P01, P02) **remains unchanged** — it is not renumbered, and subsequent pages are **never shifted down to fill the gap**. The page's content section is replaced with a reference to the migration note pointing to the target module, but **the page title and link are retained** to keep the document readable |
| 2 | **Target module**: The migrated page is assigned a **new page ID**, following immediately after the target module's current highest page number |
| 3 | **Global consistency**: The same page uses different IDs in the two modules, each document being self-consistent; a cross-module reference must **cite both** the source-module ID and the target-module ID |

> **Example**: If P01/P02 of `{ModuleA}` migrate to `{ModuleB}` (which already has P01–P05), they are numbered P06/P07 in `{ModuleB}`;
> in `{ModuleA}`, the content of §3.1/§3.2 is replaced with a migration note, the title and section index are retained, and P03–P08 are not shifted down to fill the gap.

---

<a id="change-log"></a>

## Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {Description of change} |
