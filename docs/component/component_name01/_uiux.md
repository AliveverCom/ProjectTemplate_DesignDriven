<!--
TEMPLATE NOTES (delete this comment block when using)

[Document Level] Component-level UI/UX design document.

[Storage Location] docs/components/{component_code_name}/{component_code_name}_uiux.md
  - The filename replaces the leading `_` with the component code name, e.g. `material_collector_uiux.md`.
  - Companion directory: docs/components/{component_code_name}/ui_page_design/
      ├── {component_code_name}_page_flow.mmd     # Page flow diagram source file
      ├── {component_code_name}_page_flow.png     # Page flow diagram rendered output
      ├── P01  {PageName}/
      │   ├── code.html                            # UI design draft (Stitch / hand-written HTML)
      │   └── screen.png                           # Design draft screenshot
      └── P02  {PageName}/ ...

[Boundary with Platform-Level Documents — very important]
  - The platform-level docs/uiux_design_specification.md defines **design patterns and global UI conventions**:
    page design patterns (Single-List Page / Dual-List Page / Tree Single-List Page / Business Object Detail Page / popup dialogs…),
    common UI controls (Multi Tags, Slider+Number Input, Autocomplete, Tag-style Time Input…),
    color scheme, state management, data formatting, loading/empty states, real-time refresh, error handling, naming rules, etc.
  - This component-level document **only describes this component's own pages**, and references the platform spec via `§x.x.x` section numbers.
    It **must not redefine** any common control spec, color spec, or general interaction rule.
    If a pattern turns out to have cross-component reusability, go through the ai_dev/01_DocReviewRefine
    _uiux_pattern_summary (UI/UX common design pattern extraction) process; after discussion, promote it to the platform spec, leaving only a reference here.
  - The semantics of business object fields follow {component_code_name}_business_desc.md;
    API endpoints and request/response structures follow {component_code_name}_tech_design.md §5.
    This document only states "which fields a page uses, which endpoints it calls" — it does not redefine field types or response bodies.

[Document Conventions]
  - The Page Flow Diagram is placed **before** the TOC (readers see the overall page relationships first, then the table of contents).
  - All APIs under Dependent APIs / Page Behaviors must be written as **complete REST endpoint paths**;
    using an `Object.Method`-style logical name is forbidden (a legacy convention, already removed uniformly in docs_review R10).
  - Page IDs use the two-digit `P{nn}` numbering scheme; once assigned, an ID is never reused. When a page migrates
    to another component, see the numbering rules in uiux_design_specification.md §18.2.
-->

# {ComponentName} ({component_code_name}) UI/UX Design

**Document Version**: {x.y.z}

---

## Page Flow Diagram

![Page Flow Diagram](./ui_page_design/{component_code_name}_page_flow.png)

> Source: [{component_code_name}_page_flow.mmd](./ui_page_design/{component_code_name}_page_flow.mmd)
>
> Drawing conventions: see [templates/mermaid/mmd_style_guide.md](../../../templates/mermaid/mmd_style_guide.md):
> white canvas, light-colored boxes color-coded by page type, all text in black.

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Page Overview](#2-page-overview)
  - [2.1 Page List](#21-page-list)
  - [2.2 Navigation Menu](#22-navigation-menu)
  - [2.3 Page-Object-API Mapping](#23-page-object-api-mapping)
- [3 Page Details](#3-page-details)
  - [3.1 P01 {PageName1}](#31-p01-pagename1)
    - [3.1.1 UI Design](#311-ui-design)
    - [3.1.2 Page Business Functions](#312-page-business-functions)
    - [3.1.3 Page Layout and Elements](#313-page-layout-and-elements)
    - [3.1.4 Page Behaviors](#314-page-behaviors)
  - [3.2 P02 {PageName2}](#32-p02-pagename2)
    - [3.2.1 UI Design](#321-ui-design)
    - [3.2.2 Page Business Functions](#322-page-business-functions)
    - [3.2.3 Page Layout and Elements](#323-page-layout-and-elements)
    - [3.2.4 Page Behaviors](#324-page-behaviors)
- [4 Route Definitions](#4-route-definitions)
- [5 Frontend Source Code Directory](#5-frontend-source-code-directory)
- [Change Log](#change-log)

---

## 2 Page Overview

### 2.1 Page List

| Page ID | Page Name | Layout Mode | Description |
|---------|-----------|-------------|-------------|
| P01 | {PageName1} | Dual-List Page | {One-sentence description of what this page lets the user accomplish} |
| P02 | {PageName2} | Read-Only Detail Page | {One-sentence description} |
| P03 | {PageName3} | Single-List Page | {One-sentence description} |
| P04 | {PageName4} | Business Object Detail Page | {One-sentence description} (⚠️ Not yet implemented) |
| P05 | {PageName5} | Dashboard | {One-sentence description} |
| P06 | {PageName6} | Tree Dual-List Page | {One-sentence description} |
| P07 | API Debug Page | Two-Column Layout | Provides an interactive console for developers to quickly verify all APIs of this module, for development and debugging |

> **Layout Mode values** must be taken from the design pattern names already defined in
> [uiux_design_specification.md](../../uiux_design_specification.md); inventing new names is not allowed:
>
> | Layout Mode | Spec Section |
> |-------------|---------|
> | Single-List Page | §2.1.1 |
> | Dual-List Page | §2.1.2 |
> | Tree Single-List Page | §2.1.3 |
> | Tree Dual-List Page | §2.1.4 |
> | Business Object Detail Page (Create/Edit) | §2.2 / §2.2.1 / §2.2.3 |
> | Read-Only Detail Page (business object preview or read-only page) | §2.2.2 |
> | Dashboard | {spec section, or "component-specific, no general spec"} |
> | Two-Column Layout | {spec section, or "component-specific, no general spec"} |
>
> **Page Origin Annotation**: if a page was migrated from another component, note the origin and the review item ID
> in the Description column, e.g. "(migrated from channel_management P01, R4-01)".

### 2.2 Navigation Menu

{component_code_name}'s pages are presented as a menu group in the unified_portal sidebar, with the following menu structure:

```
{ComponentName}
├── {MenuItem1}       → P05
├── {MenuItem2}       → P01
├── {MenuItem3}       → P03
├── {MenuItem4}       → P06
└── API Debug          → P07
```

By default, lands on the "{DefaultMenuItem}" (P{nn}) page.

> - Only pages reachable directly from the menu appear in the menu tree; detail pages (such as P02/P04/P07) are reached by navigating from a list page and do not appear in the menu.
> - The menu item text must match the values returned by unified_portal's navigation menu API; see
>   the navigation menu section of [unified_portal_tech_design.md](../unified_portal/unified_portal_tech_design.md).

### 2.3 Page-Object-API Mapping

| Page ID | Page Name | Primary Business Objects | Dependent APIs |
|---------|-----------|------------------------|----------------|
| P01 | {PageName1} | {CObject1}, {CObject2} | GET /api/v1/{resource-a}, DELETE /api/v1/{resource-a}/{id}, POST /api/v1/{resource-a}/batch-delete, GET /api/v1/{resource-b} |
| P02 | {PageName2} | {CObject1} | GET /api/v1/{resource-a}/{id}, DELETE /api/v1/{resource-a}/{id} |
| P03 | {PageName3} | {CObject3} | GET /api/v1/{resource-c}, POST /api/v1/{resource-c}/{id}/{action} |
| P04 | {PageName4} (⚠️ Not yet implemented) | {CObject3} | GET /api/v1/{resource-c}/{id} |
| P05 | {PageName5} | — | GET /api/v1/{resource-d}/status, GET /api/v1/stats/summary |
| P06 | {PageName6} | {CObject2} | GET /api/v1/{resource-b}, POST /api/v1/{resource-b}, PUT /api/v1/{resource-b}/{id}, DELETE /api/v1/{resource-b}/{id} |
| P07 | API Debug Page | — | All API endpoints of this module |

> **API Notes**: the Dependent APIs for every page must use **complete REST endpoint paths**, matching the
> full definitions in §5 of [{component_code_name}_tech_design.md](./{component_code_name}_tech_design.md).
> Using an `Object.Method`-style logical name in this table or in §3.x.4 Page Behaviors is forbidden.
>
> **Cross-Module Call Notes ({Rxx-yy})**: {if this module's API is called by another module, or a page in
> this module calls another module's API, it must be stated explicitly here. For example: "This module's
> `GET /api/v1/{resource-b}` is also called cross-module by `{other_component}`:
> {other_component}'s P0x page calls this endpoint when displaying the {xxx} list tab. Per
> [technical_overview.md §4.3](../../technical_overview.md) item {n}, these endpoints are shown grouped as
> cross-module calls on {other_component}'s API debug page." If there is no cross-module call, write "This
> module's API is not called by other modules, and this module's pages do not call other modules' APIs."}

---

## 3 Page Details

---

### 3.1 P01 {PageName1}

> **Design Pattern**: follows [uiux_design_specification.md](../../uiux_design_specification.md) §2.1.2 Dual-List Page.
>
> **⚠️ To Be Completed ({Rxx-yy})**: the full UI/UX design spec for this page (Page Layout and Elements) will be further completed by the engineer before development of the corresponding version.
>
> {Usage of the two blockquote notes above:
>  - The first, **Design Pattern**, is required; it anchors the page to a specific section of the platform spec, avoiding repeated description of common interactions.
>  - The second, **⚠️ To Be Completed**, is optional; keep it only while the page spec is not yet finalized, filling in the corresponding review item ID in parentheses,
>    and remove it together with the note once finalized.
>  - If the page has a version scope restriction, add one more line: `> **Version Scope**: this page belongs to v0.x, not implemented before v0.y.`}

#### 3.1.1 UI Design

![P01 {PageName1} UI](./ui_page_design/P01  {PageName1}/screen.png)

> Source: [ui_page_design/P01  {PageName1}/code.html](./ui_page_design/P01  {PageName1}/code.html)

> {If no design draft exists yet, replace the two lines above with:}
> UI mockup will be provided in a future version.

#### 3.1.2 Page Business Functions

This page allows {TargetRole} to {AccomplishWhatBusinessGoal}.

| # | Function | Description |
|---|----------|-------------|
| 1 | {Function1} | {Select a {ParentObject} on the left; the right side shows the list of {ChildObject} under that {ParentObject}} |
| 2 | {Function2} | {Supports keyword search and date range filtering} |
| 3 | {Function3} | {Sorted by {Field} descending by default; supports sorting by clicking column headers and a special sort dropdown} |
| 4 | {Function4} | {Double-click or click "View" to enter the {ChildObject} Read-Only Detail Page (P02)} |
| 5 | {Function5} | {Select one or more records and click "Delete" to perform a batch delete} |

#### 3.1.3 Page Layout and Elements

Overall page layout: **Title Area** → **Left/Right Dual-Panel Area**. The left and right panels scroll independently; there is no scrollbar on the main view.

> Other common ways to write the layout overview:
> - Single-List Page: overall page layout: **Title Area** → **Filter Area** → **Action Button Area** → **List Area** → **Footer Statistics and Pagination Area**. The whole page scrolls with the user; header/footer do not use sticky/fixed positioning.
> - Detail Page: overall page layout: **Title Area (with Back/Save/Cancel buttons)** → **Property Group Area (tabs or sections)** → **Footer Action Area**.
> - Dashboard: overall page layout: **Title Area** → **Status Card Row** → **Statistics Card Grid** → **Recent Events List**.

##### A. Left Panel — {ParentObject} List

> Follows the Dual-List Page "parent object list area" spec ([uiux_design_specification.md](../../uiux_design_specification.md) §2.1.2).
> {ParentObject} data comes from this module's `GET /api/v1/{resource-b}` API (passing `{query_param}={value}` to show only {filter condition description}).

**A-1. Filter Area**

| # | Element | Control Type | Default Value | Description |
|---|---------|-------------|---------------|-------------|
| 1 | {KeywordSearch} | Text Input | Empty | Fuzzy search by {Field1} or {Field2} |
| 2 | {TypeFilter} | Dropdown | All | Options: All / {ENUM_A} / {ENUM_B} |

- Filtering triggers automatically on blur or value change for each control (no search button needed).
- On first page load, all conditions are at their default values, i.e. all {ParentObject}s are shown without any filter applied.

**A-2. Action Button Area**

| Position | Element | Type | Precondition | Behavior |
|----------|---------|------|----------|----------|
| Right | Refresh | Icon Button (🔄) | — | Reload the {ParentObject} list from the API |

> {Explain why this panel does not offer certain buttons, e.g.: "P01 focuses on browsing {ChildObject}; create/update/delete for {ParentObject} is handled by this module's P06 and is not duplicated here."}

**A-3. List Area**

The list has **1 column**, displayed with line wrapping:

| Row | Content | Style | Description |
|-----|---------|-------|-------------|
| Row 1 | {Field1} | 14px, bold, black | {Description} |
| Row 2 | {Field2} tag + {Field3} tag | 12px, light-colored text, Tag badge | {Description; conditional display rule} |
| Row 3 | {Field4} + {Field5} | 11px, gray text | Display format: `{fmt}`, e.g. `120 days / 35 daily` |

- Sorting: sorted alphabetically ascending by {Field}, **user sorting is not supported** (configuration-type data, following the Tree Single-List Page spec).
- The footer provides only **Previous** / **Next** and the **current page number**; it does not show total record count or selected record count.
- {50} records per page.
- The **first** record in the list is selected by default, and the right panel loads accordingly.

**A-4. Row Interaction**

| Interaction | Behavior |
|-------------|----------|
| Click | Selects the {ParentObject}; the right panel automatically clears all filter conditions, resets the date range to "{DefaultValue}", and refreshes the data list for the newly selected {ParentObject} |
| Ctrl/Shift + Click | Multi-select; the right panel refreshes based on the **first** selected {ParentObject} |
| Double-click | Enters the Read-Only Detail Page P{nn} for that {ParentObject} |
| Right-click | Shows a context menu, with the same menu items as the Action Button Area |

---

##### B. Right Panel — {ChildObject} List

> Follows the Dual-List Page "child object list area" spec (i.e. a standard Single-List Page, but with ≤ 5 columns).

**Implicit Filter Condition**: the `{key_field}` of the first {ParentObject} currently selected on the left. Every query request carries this condition.

> **Notes on writing the "Implicit Filter Condition"**: the lower list of a Dual-List Page always carries a parent-level
> filter condition that is not shown in the Filter Area but must be included in every request. The field name must be
> stated explicitly here, otherwise the engineer cannot determine the request parameters.

**B-1. Filter Area**

| # | Element | Control Type | Default Value | Description |
|---|---------|-------------|---------------|-------------|
| 1 | Keyword | Text Input | Empty | Full-text search by {Field1} and {Field2} (case-insensitive) |
| 2 | Date Range | Calendar + Dropdown | {Today} | Filters the `{date_field}` field. Uses **Method 2** from uiux_design_specification.md: a calendar control + quick dropdown options (Today / Last 3 Days / Last 1 Week / Last 1 Month) |
| 3 | {StatusFilter} | Dropdown | All | Options: All / {ENUM_X} / {ENUM_Y} |

- Filtering triggers automatically on blur or value change for each control.
- On first page load, the date range is "{Today}" and other conditions are empty/default; a query with the date condition is executed automatically once.

**B-2. Action Button Area**

| Position | Element | Type | Precondition | Behavior |
|----------|---------|------|----------|----------|
| Left (divider between this and the buttons) | Sort By | Dropdown | — | Options: Default sort (`{field} DESC`) / {Sort2}↓ / {Sort3}↓. Issues a new query request to the backend API immediately upon selection |
| Right | View | Text Button | A record must be selected | Enters the Read-Only Detail Page P02 for the first selected record |
| Right | Create | Text Button ({color per uiux_design_specification.md §5.2}) | — | Enters the create page P{nn} |
| Right | Edit | Text Button | **Exactly one** record must be selected | Enters the edit page P{nn} for that record |
| Right | Delete | Text Button (red) | A record must be selected | Shows a confirmation dialog "Are you sure you want to delete the selected n records?"; on confirm, performs the delete; on cancel, closes the dialog and keeps the selection |

> {Explain which buttons this page does not offer and why, e.g.: "This page does not offer 'Create' or 'Edit' buttons. {ChildObject} records are automatically collected and generated by a Daemon process, and manual creation or editing is not supported."}

**B-3. List Area**

The list contains **{5}** columns:

| # | Column Header | Data Source | Width | Style-Format | Sortable |
|---|--------------|------------|-------|----------------|----------|
| 1 | ID | `{id_field}` | 60px | Right-aligned integer, **no `#` prefix** | Yes (click column header) |
| 2 | {Column2} | `{field2}` + `{field2_sub}` | flex | **Row 1**: {field2}, black 14px, truncated at 2 lines max; **Row 2**: {field2_sub}, gray 12px, truncated at 1 line; shows gray italic "{PlaceholderText}" if empty; **Row 3**: {tags} Tag badges, 12px, same name gets the same color (see uiux_design_specification.md §3.1.2) | Yes (sorted by {field2}) |
| 3 | {Column3} | `{field3}` | 80px | Tag badge display (e.g. `{ExampleValue}`), centered | Yes |
| 4 | {Column4} | `{field4}` | 120px | **Processed**: green ✓ icon + date (`yyyy-MM-dd`); **Unprocessed**: gray "—" | Yes |
| 5 | {Column5} | `{field5}` | 160px | `yyyy-MM-dd HH:mm` format (see uiux_design_specification.md §9 data formatting rules) | Yes |

- Default sort: by **`{field5}` descending**.
- Sorting rule: clicking a column header toggles between ascending and descending, with a small arrow indicating direction. Multi-column sorting is not supported. All sorting is implemented via the **backend API** (no frontend local sorting).
- Footer shows: `Selected {n} / Total {m}` | **pagination controls**. {50} records per page; no "records per page" selector is provided.
- {Soft-deleted/inactive row styling: follows uiux_design_specification.md §3.2.1 / §3.2.2.}

**B-4. Row Interaction**

| Interaction | Behavior |
|-------------|----------|
| Click | Selects the record |
| Ctrl/Shift + Click | Multi-selects records |
| Double-click | Enters the Read-Only Detail Page P02 for that record |
| Right-click | Shows a context menu, with the same menu items as the Action Button Area (View, Delete) |

> **Row Selection Feedback**: the right-side list on this page **does not use a checkbox column**; selection state is shown by
> highlighting the whole row on click / Ctrl / Shift click (unlike P{nn}, which uses a checkbox column for selection). Selected
> rows are highlighted with a blue background; when multiple rows are selected, all selected rows are highlighted. The
> "selected record count" updates in real time in the footer statistics area.
>
> **Notes on writing this**: if different pages within the same component use different selection methods (checkbox column vs.
> whole-row highlight), the difference must be explicitly noted in both places, otherwise HTML review will repeatedly flag the
> same inconsistency.

#### 3.1.4 Page Behaviors

| # | Trigger | Action | Result |
|---|---------|--------|--------|
| 1 | Page first loads | Calls `GET /api/v1/{resource-b}?{filter}=false&is_active=true&sort_by={field}&sort_order=asc` | The left panel is populated with the {ParentObject} list; the first one is selected automatically |
| 2 | A {ParentObject} is selected on the left (first time or switching) | Clears the right-side filter conditions; the date range resets to "{Today}"; calls `GET /api/v1/{resource-a}/{key_field}` (with date range query parameters) | The right panel refreshes the {ChildObject} list |
| 3 | Right-side filter condition changes | Calls `GET /api/v1/{resource-a}/{key_field}` (with all current filter conditions) | The right-side list refreshes |
| 4 | Right-side sort option changes | Calls `GET /api/v1/{resource-a}/{key_field}?sort_by={field}&sort_order={asc\|desc}` | The right-side list refreshes with the new sort order |
| 5 | Double-click a record / click "View" | Navigates to the P02 page, passing `{id_field}` and `{key_field}` | Enters the Read-Only Detail Page |
| 6 | Click "Delete" | Shows a blocking confirmation dialog (see uiux_design_specification.md §2.3.2); after user confirmation, calls `POST /api/v1/{resource-a}/{key_field}/batch-delete` (the array contains 1 element when a single record is selected) | On success, refreshes the right-side list; on failure, shows an error message and keeps the selection |
| 7 | Returning from P02 (exit or successful delete) | Refreshes the right-side list, keeping the currently selected {ParentObject} on the left | List updates |
| 8 | Left "Refresh" button clicked | Re-calls `GET /api/v1/{resource-b}?...` | The left-side list refreshes, attempting to keep the current selection |
| 9 | API returns an error | Displayed per uiux_design_specification.md §16 error handling | Shows an error message; the page keeps its previous state |

> **Requirements for the Action column**: always write the **specific REST endpoint + query parameters** (including key
> parameter names and values); do not write a vague description like "calls the query API" — the engineer codes directly
> from this table, and a vague description will cause review rework.

---

### 3.2 P02 {PageName2}

> **Design Pattern**: follows [uiux_design_specification.md](../../uiux_design_specification.md) §2.2.2 Business Object Preview or Read-Only Page.

#### 3.2.1 UI Design

![P02 {PageName2} UI](./ui_page_design/P02  {PageName2}/screen.png)

> Source: [ui_page_design/P02  {PageName2}/code.html](./ui_page_design/P02  {PageName2}/code.html)

#### 3.2.2 Page Business Functions

| # | Function | Description |
|---|----------|-------------|
| 1 | {ViewFullInformation} | {Shows all properties of {CObject}, read-only} |
| 2 | {DeleteRecord} | {Click the "Delete" button to delete the current record and return to the list page} |

#### 3.2.3 Page Layout and Elements

Overall page layout: **Title Area (with "Exit"/"Delete" buttons)** → **Property Group Area** → {**Content Collapse/Expand Area**}.

**A. Title and Action Button Area**

| Position | Element | Type | Precondition | Behavior |
|----------|---------|------|----------|----------|
| Left | Page Title | Text | — | Shows `{ObjectName} — {CurrentRecordIdentifier}` |
| Right | Edit | Text Button | {Condition} | Switches to edit mode / enters P{nn} |
| Right | Delete | Text Button (red) | — | Shows a confirmation dialog; on confirm, calls `DELETE /api/v1/{resource-a}/{id}` |
| Right | Exit | Text Button | — | Returns to the originating list page P01 |

**B. Property Group Area**

| # | Group | Field | Control / Display Form | Description |
|---|------|------|----------------|------|
| 1 | Basic Information | `{field1}`, `{field2}` | Read-only text | {Description} |
| 2 | {Group2} | `{field3}` | Tag Cloud, read-only display (§3.1.3) | {Description} |
| 3 | {Group3} | `{field4}` | Slider + Number Input, read-only (§3.3; read-only mode shows only the number) | {Description} |
| 4 | Time Information | `created_at`, `updated_at`, `deleted_at` | Read-only text, `yyyy-MM-dd HH:mm` | {Description} |

**C. {Long Text/Media Resource Area}**

{Describe the collapse/expand rule, media preview method, and truncation rule for overly long text.}

#### 3.2.4 Page Behaviors

| # | Trigger | Action | Result |
|---|---------|--------|--------|
| 1 | Page loads | Calls `GET /api/v1/{resource-a}/{key_field}/{id}` | Populates all properties |
| 2 | Click "Delete" | Shows a confirmation dialog; on confirm, calls `DELETE /api/v1/{resource-a}/{key_field}/{id}` | On success, returns to P01 and refreshes the list; on failure, shows an error message |
| 3 | Click "Exit" | Returns to P01 | The list keeps its original filter conditions and selection |
| 4 | Record not found (404) | Displayed per uiux_design_specification.md §16 | Shows "Record not found" and provides a button to return to the list |

---

> **Repetition Rule**: repeat the `### 3.x P{nn} {PageName}` section once per page. Each section has four fixed subsections:
>
> - `3.x.1 UI Design` (screen.png + code.html)
> - `3.x.2 Page Business Functions` (numbered function table)
> - `3.x.3 Page Layout and Elements` (broken down by panel/area; list pages have a fixed A-1 through A-4)
> - `3.x.4 Page Behaviors` (numbered Trigger/Action/Result table; Action states the full endpoint)
>
> How the 3.x.3 structure differs by Layout Mode:
>
> | Layout Mode | 3.x.3 Breakdown |
> |-------------|-----------|
> | Single-List Page | A-1 Filter Area / A-2 Action Button Area / A-3 List Area / A-4 Row Interaction |
> | Dual-List Page | A. Left Panel (A-1–A-4) + B. Right Panel (B-1–B-4, including the Implicit Filter Condition) |
> | Tree (Dual-)List Page | A. Tree Panel (node hierarchy, expand rules, node icons) + B. Right List Panel (B-1–B-4) |
> | Business Object Detail Page | A. Title and Action Button Area / B. Property Groups (a table comparing the create/read-only/edit mode differences) / C. Validation Rules |
> | Read-Only Detail Page | A. Title and Action Button Area / B. Property Groups / C. Long Text and Media Area |
> | Dashboard | A. Status Cards / B. Statistics Cards / C. Recent Events List / D. Auto-Refresh Strategy (§10) |
> | Two-Column Layout (API Debug Page) | A. Left Column Endpoint Tree (grouped per tech_design §5, including a cross-module call group) / B. Right Column Request Builder and Response Display |

---

## 4 Route Definitions

| # | Route | Page | Component File | Description |
|---|-------|------|---------|------|
| 1 | `/{component-code-name}/{page-a}` | P01 {PageName1} | `pages/{page-a}/index.tsx` | {Description} |
| 2 | `/{component-code-name}/{page-a}/:{id_field}` | P02 {PageName2} | `pages/{page-a}-detail/index.tsx` | {Description} |
| 3 | `/{component-code-name}/{page-b}` | P03 {PageName3} | `pages/{page-b}/index.tsx` | {Description} |
| 4 | `/{component-code-name}/{page-c}/new` | P{nn} {CreatePage} | `pages/{page-c}-detail/index.tsx` | Create mode |
| 5 | `/{component-code-name}/{page-c}/:{id_field}` | P{nn} {DetailPage} | `pages/{page-c}-detail/index.tsx` | Read-only mode |
| 6 | `/{component-code-name}/{page-c}/:{id_field}/edit` | P{nn} {EditPage} | `pages/{page-c}-detail/index.tsx` | Edit mode |
| 7 | `/{component-code-name}/api-debug` | P07 API Debug Page | `pages/api-debug/index.tsx` | For development and debugging |

> **Version Scope Notes**: {state which routes in this table belong to which version, e.g.: "v0.1 implements the routes for
> P01–P0x (n routes total); the P07 API Debug Page belongs to v0.2; P0y belongs to v0.z." This annotation must be
> consistent with [{component_code_name}_version_plan.md](./{component_code_name}_version_plan.md).}
>
> - Route paths use kebab-case, consistent with the frontend package directory naming rule (see [naming_convention.md](../../naming_convention.md)).
> - The create / read-only / edit modes of a detail page **reuse the same component file**, distinguished by a route suffix
>   (see uiux_design_specification.md §2.2).

---

## 5 Frontend Source Code Directory

```
src/frontend/packages/{component-code-name}/
├── src/
│   ├── pages/                       # Page components, one directory per page
│   │   ├── {page-a}/index.tsx       # P01
│   │   ├── {page-a}-detail/index.tsx# P02
│   │   └── api-debug/index.tsx      # P07
│   ├── components/                  # Reusable components private to this component
│   ├── models/                      # TypeScript type definitions (C/E/I prefixes, see naming_convention.md §3)
│   ├── services/                    # API client wrappers (api.ts); pages must not send requests directly
│   ├── hooks/                       # Data hook layer
│   ├── stores/                      # State management (one-store-per-entity + uiStore, see spec §8)
│   ├── mock/                        # v0.1 hardcoded data; used only for testing from v0.2 onward
│   ├── routes.tsx                   # Route registration, one-to-one with §4
│   └── index.ts                     # Package exports
└── package.json
```

> Directories use **kebab-case** (the `src/frontend/` rule); for file naming rules see
> [uiux_design_specification.md](../../uiux_design_specification.md) §18 and
> [naming_convention.md](../../naming_convention.md).

---

## Change Log

| Version | Date | Changes |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {Description of the change; if it originated from a review, note the item ID, e.g. "Fixed R7-04: added To-Be-Completed annotations for P01–P05"} |
| {x.y.z} | {YYYY-MM-DD} | {Description of the change} |

> The Change Log is listed in **reverse chronological order** (newest at top). Every time a document is modified due to a
> review, a new row must be added noting the review item ID, to help the next round of ai_dev/01_DocReviewRefine review
> verify that it was addressed.
