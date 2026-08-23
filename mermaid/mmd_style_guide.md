# Mermaid Style Guide (mmd_style_guide.md)

This file documents the `templates/mermaid/` directory. It defines the uniform styling, colour
palette, naming and file-placement rules for **every** `.mmd` diagram in the project. The `.mmd`
files in this directory whose names start with `_` are copyable diagram templates.

---

## 1 Why the Styling Must Be Uniform

1. **Diagrams are rendered to PNG and embedded in Markdown documents.** Those documents end up
   printed, exported to PDF and pasted into review material. Mermaid's default themes
   (`default` / `dark`) produce grey fills and light-grey text that are nearly unreadable on a
   white page once printed.
2. **Both humans and AI classify nodes by colour.** When roles, pages, endpoints, tables and
   external systems share one diagram, only a stable colour convention lets a reader tell node
   types apart at a glance.
3. **Diagrams from different modules get read side by side.** Once every module's class diagram,
   API diagram and page flow diagram use the same palette, cross-module comparison never trips over
   "the same kind of thing in two different colours".

Hence three hard rules apply to every `.mmd` in this project:

> **(1) White canvas　(2) Every box classified by type, one light fill per type　(3) All text black**

---

## 2 Standard init Block (copy verbatim)

The first meaningful line of every `.mmd` file must be a `%%{init: ...}%%` directive block.

### 2.1 Flowcharts / Architecture / Page Flow / API Diagrams (`graph` / `flowchart`)

```
%%{init: {
  "theme": "base",
  "themeVariables": {
    "background": "#FFFFFF",
    "primaryColor": "#E3F2FD",
    "primaryTextColor": "#000000",
    "primaryBorderColor": "#1565C0",
    "secondaryColor": "#E8F5E9",
    "tertiaryColor": "#FFF9C4",
    "lineColor": "#555555",
    "textColor": "#000000",
    "titleColor": "#000000",
    "clusterBkg": "#FAFAFA",
    "clusterBorder": "#CCCCCC",
    "edgeLabelBackground": "#FFFFFF",
    "fontFamily": "Arial, sans-serif",
    "fontSize": "14px"
  }
}}%%
```

### 2.2 Class Diagrams / Database Schema Diagrams (`classDiagram`)

```
%%{init: {
  "theme": "base",
  "themeVariables": {
    "background": "#FFFFFF",
    "primaryColor": "#E3F2FD",
    "primaryTextColor": "#000000",
    "primaryBorderColor": "#1565C0",
    "lineColor": "#333333",
    "textColor": "#000000",
    "titleColor": "#000000",
    "classText": "#000000",
    "clusterBkg": "#FAFAFA",
    "clusterBorder": "#CCCCCC",
    "edgeLabelBackground": "#FFFFFF",
    "fontFamily": "Arial, sans-serif",
    "fontSize": "13px"
  }
}}%%
```

> `classText` only takes effect in a `classDiagram`, where it forces class member text to black.
> `titleColor` applies to the `--- title: ... ---` front-matter heading.
> If your diagrams contain non-Latin text, extend `fontFamily` with a font that covers it, for
> example `"Arial, \"Noto Sans\", sans-serif"`.

### 2.3 The Colouring Mechanism Differs by Diagram Type (a real trap — verified by testing)

**The same palette requires two completely different syntaxes depending on the diagram type.**

| Diagram type | Colouring syntax that works | Syntax that is silently ignored |
|--------------|----------------------------|---------------------------------|
| `graph` / `flowchart` | `classDef xxxCls fill:...` plus the `:::xxxCls` suffix on each node | — |
| `classDiagram` | **One `style ClassName fill:...,stroke:...,color:#000000` per class** | `classDef`, `cssClass "A,B" cls`, `class A:::cls` |

Verified on mermaid v11.16: writing `classDef` / `cssClass` / `:::` inside a `classDiagram`
**raises no error at all**, but has no effect whatsoever — every class box comes out in the same
theme colour. This is the classic "looks like it was classified but wasn't" trap.

A `classDiagram` needs one `style` statement per class. Example (this also works for classes
declared inside a `namespace` — verified):

```
classDiagram
    direction TB

    namespace main_db {
        class main_object {
            <<Table>>
            +INT_PK object_id
        }
    }
    class ref_table {
        +INT_PK ref_id
    }

    %% ----- tableCls ordinary business table: light blue -----
    style main_object fill:#E3F2FD,stroke:#1565C0,color:#000000
    %% ----- refTableCls cross-database reference table: light grey -----
    style ref_table   fill:#F5F5F5,stroke:#616161,color:#000000
```

> It is verbose, but the verbosity buys something: every class *must* be classified explicitly.
> Miss one and it renders in the default blue — immediately visible, and therefore much easier to
> catch in review.

### 2.4 Two Syntax Traps That Break Rendering Outright (verified by testing)

| # | Trap | Symptom | Correct form |
|---|------|---------|--------------|
| 1 | A YAML front-matter `title` that starts with `{` | `YAMLException`, rendering fails (`{` starts a flow mapping in YAML) | Quote the title: `title: "{ComponentName} — Business Process"` |
| 2 | An **empty** `%%` comment line | `graph` / `flowchart` reports `Parse error ... %%%%flowchart TD` (mermaid's comment-stripping regex requires at least one character after `%%`; an empty `%%` line survives and fuses with the next line) | Write the blank line as `%% -`, or delete it |

> The front matter (`--- title: ... ---`) must be at the **very beginning** of the file — no comment
> and no blank line may precede it.

---

## 3 Standard Colour Tables

### 3.1 Global Palette (9 light colours)

Every diagram draws from these 9 light colours only; inventing new ones is not allowed. `fill` is
the box background, `stroke` is the border in the same hue family.

| Code | Name | fill | stroke |
|------|------|---------|---------|
| `BLUE` | light blue | `#E3F2FD` | `#1565C0` |
| `CYAN` | light cyan | `#E0F7FA` | `#00838F` |
| `GREEN` | light green | `#E8F5E9` | `#2E7D32` |
| `YELLOW` | light yellow | `#FFF9C4` | `#F9A825` |
| `ORANGE` | light orange | `#FFF3E0` | `#EF6C00` |
| `CORAL` | light coral | `#FBE9E7` | `#D84315` |
| `PURPLE` | light purple | `#F3E5F5` | `#6A1B9A` |
| `PINK` | light pink | `#FADBD8` | `#C0392B` |
| `GREY` | light grey | `#F5F5F5` | `#616161` |

### 3.2 Category → Colour Mapping (authoritative)

| Category (classDef name) | Applies to | Palette | fill | stroke | Text |
|--------------------------|-----------|---------|---------|---------|------|
| `actorCls` | People / roles / actors (business process diagrams) | BLUE | `#E3F2FD` | `#1565C0` | `#000000` |
| `pageCls` | Frontend pages / UI views (API diagrams) | CYAN | `#E0F7FA` | `#00838F` | `#000000` |
| `apiCls` | REST endpoints / interfaces | GREEN | `#E8F5E9` | `#2E7D32` | `#000000` |
| `decisionCls` | Decision / branch nodes (diamonds) | YELLOW | `#FFF9C4` | `#F9A825` | `#000000` |
| `menuCls` | Menus / entry points / gateways | ORANGE | `#FFF3E0` | `#EF6C00` | `#000000` |
| `debugCls` | Debug pages / tooling pages | CORAL | `#FBE9E7` | `#D84315` | `#000000` |
| `serviceCls` | Backend services / daemons / shared services | PURPLE | `#F3E5F5` | `#6A1B9A` | `#000000` |
| `dataCls` | Databases / tables / storage | PINK | `#FADBD8` | `#C0392B` | `#000000` |
| `externalCls` | External systems / third parties / cross-module references | GREY | `#F5F5F5` | `#616161` | `#000000` |

### 3.3 Category Aliases per Diagram Type

Every diagram type reuses the same palette; only the `classDef` names change to fit the semantics:

| Diagram type | Category → palette |
|--------------|--------------------|
| Business process `_business_process.mmd` | `roleACls`→BLUE, `roleBCls`→PURPLE, `roleCCls`→GREEN, `systemCls`→ORANGE, `decisionCls`→YELLOW, `dataCls`→PINK |
| Technical architecture `_technical_architecture.mmd` | `clientCls`→BLUE, `gatewayCls`→ORANGE, `portalCls`→CYAN, `componentCls`→GREEN, `serviceCls`→PURPLE, `dataCls`→PINK, `externalCls`→GREY |
| Class diagram `_class_diagram.mmd` | `baseCls`→BLUE, `entityCls`→GREEN, `valueCls`→CYAN, `enumCls`→YELLOW, `refCls`→GREY |
| Database schema `_db_schema.mmd` | `tableCls`→BLUE, `templateCls`→ORANGE, `lookupCls`→YELLOW, `refTableCls`→GREY |
| API diagram `_api_diagram.mmd` | `pageCls`→CYAN, `apiCls`→GREEN, `extCls`→GREY |
| Page flow `_page_flow.mmd` | `menuCls`→ORANGE, `listCls`→BLUE, `detailCls`→GREEN, `dashCls`→PURPLE, `debugCls`→CORAL |

### 3.4 Container (subgraph) Fills

A `subgraph` fill must be **lighter** than the nodes inside it, otherwise the nodes cannot "lift off"
the container. Use the near-white tones below, and always declare `color:#000` in the `style`
statement as well:

| Container hue | fill | stroke |
|---------------|---------|---------|
| Blue group | `#F5FAFE` | `#1565C0` |
| Cyan group | `#F2FBFC` | `#00838F` |
| Green group | `#F5FCF7` | `#2E7D32` |
| Yellow group | `#FFFDF2` | `#F9A825` |
| Orange group | `#FFFAF3` | `#EF6C00` |
| Purple group | `#FBF6FD` | `#6A1B9A` |
| Pink group | `#FEF7F6` | `#C0392B` |
| Neutral group | `#FAFAFA` | `#CCCCCC` |
| **Legend** | `#F8F9FA` | `#CCCCCC` |

---

## 4 Legend

Every diagram except class diagrams **must carry a legend**. The form is fixed:

```
    subgraph legend["Legend"]
        direction LR
        LG1["Role A"]:::roleACls
        LG2["Role B"]:::roleBCls
    end
    style legend fill:#F8F9FA,stroke:#CCCCCC,color:#000
```

Legend node IDs always use the `LG1`, `LG2`, ... prefix so they cannot collide with business nodes.
A `classDiagram` does not support `subgraph`, so it documents the colour semantics in a `%%` comment
block at the top of the file instead.

---

## 5 Naming and File Placement

### 5.1 File Names

```
{component_code_name}_{diagram_type}.mmd
{component_code_name}_{diagram_type}.png     # same name and directory as the .mmd
```

| diagram_type | Template | Directory |
|-------------|----------|-----------|
| `business_process` | `_business_process.mmd` | `docs/components/{component}/` |
| `class_diagram` | `_class_diagram.mmd` | `docs/components/{component}/` |
| `db_schema` | `_db_schema.mmd` | `docs/components/{component}/` |
| `api` | `_api_diagram.mmd` | `docs/components/{component}/` |
| `page_flow` | `_page_flow.mmd` | `docs/components/{component}/ui_page_design/` |

Platform-level diagrams carry no component prefix and sit directly in the `docs/` root:
`business_process.mmd`, `technical_architecture.mmd`.

> **Note**: `page_flow` is the only diagram that lives in the `ui_page_design/` subdirectory,
> because it is a UI design asset and is managed alongside each page's `code.html` / `screen.png`.

### 5.2 Referencing a Diagram from a Document

Markdown always uses two lines — the image, then a link to the source:

```markdown
![{DiagramTitle}](./{component_code_name}_class_diagram.png)

> Source: [{component_code_name}_class_diagram.mmd](./{component_code_name}_class_diagram.mmd)
```

### 5.3 Rendering to PNG

Use the Mermaid CLI (`@mermaid-js/mermaid-cli`):

```bash
# one-time install
npm install -g @mermaid-js/mermaid-cli

# single file: -b white forces a white canvas, -s 3 renders at 3x for printing
mmdc -i my_component_class_diagram.mmd \
     -o my_component_class_diagram.png \
     -b white -s 3

# render every mmd in the current directory
for f in *.mmd; do mmdc -i "$f" -o "${f%.mmd}.png" -b white -s 3; done
```

> `-b white` and `"background": "#FFFFFF"` in the init block are **belt and braces** — keep both:
> the former controls the exported canvas, the latter controls the theme's internal fill
> calculations.

### 5.4 Using a Template

1. Copy the matching `_{diagram_type}.mmd` from this directory.
2. Rename it to `{component_code_name}_{diagram_type}.mmd` and move it to the target directory.
3. Delete the `%% ===== TEMPLATE NOTES =====` comment block at the top of the file.
4. Replace every `{curly brace placeholder}`. Node IDs (`A1`, `P01`, `SM_LIST`, ...) may be renamed
   as needed, but the `classDef` classification must never be lost.
5. Render the PNG and commit it with the same name and directory as the `.mmd`.

---

## 6 Mandatory Pre-Publish Checklist

Before committing a `.mmd` and its `.png`, check every line:

| # | Check | Pass criterion |
|---|-------|----------------|
| 1 | **White canvas** | The init block contains `"background": "#FFFFFF"`; the render command carries `-b white` |
| 2 | **All text black** | `textColor` / `primaryTextColor` / `titleColor` (plus `classText` for class diagrams) are all `#000000` |
| 3 | **Every colouring statement declares color** | Every `classDef` / `style` statement ends with `,color:#000000` |
| 4 | **Every node is classified** | `graph`/`flowchart`: no node lacks a `:::xxxCls`; `classDiagram`: every class has its own `style` statement |
| 4b | **Colouring syntax matches the diagram type** | No leftover `classDef` / `cssClass` / `:::` inside a `classDiagram` (they do not work — see §2.3) |
| 4c | **Verify the colours in the rendered PNG** | Open the PNG and confirm the node categories really are different colours, not a wall of default blue (the only reliable way to spot a missed classification) |
| 5 | **Colours come from the palette** | Every fill / stroke appears in the 9-colour table in §3.1 |
| 6 | **No dark fills** | No `fill:#333`, `fill:#4A90D9` or similar dark blocks (every light fill has a brightness of `#E0` or above) |
| 7 | **Containers lighter than nodes** | Every `style {subgraph} fill:` comes from §3.4 and carries `color:#000` |
| 8 | **Legend present** | Non-class diagrams must contain `subgraph legend["Legend"]`; class diagrams must contain the colour comment block at the top |
| 9 | **Placeholders replaced** | Searching for `{` finds no leftover template placeholders; class names / table names / namespace names contain **no** curly braces |
| 9b | **No empty `%%` lines** | `grep -c '^%%$' x.mmd` returns 0 (see §2.4) |
| 10 | **PNG in sync** | The `.png` shares the `.mmd`'s name and directory and was re-rendered after the latest edit |
| 11 | **Referenced from the document** | The image link and source link in the owning `.md` resolve correctly |
| 12 | **No clipped text** | At the default `-s 1`, a long identifier in a narrow box (an enum value such as `CANCELED`) can be clipped to `CANCELE`. Always render with `-s 3` and confirm in the PNG that the longest line of text is fully visible |

---

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | {YYYY-MM-DD} | First release: white canvas + black text, 9-colour light palette, category mapping table, 6 diagram templates |
