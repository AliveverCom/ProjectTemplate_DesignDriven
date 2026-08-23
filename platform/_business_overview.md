# Index

<!--
TEMPLATE NOTES (delete this comment block when using)
- Level: **platform-level business/product document**. Place in the `docs/` root directory, filename `business_overview.md`.
- Difference from the component-level `{component}_business_desc.md`:
  This document describes the business positioning, full product component landscape, user roles, and end-to-end business process of **the entire platform**;
  component-level documents describe only the business objects and business processes **inside a single component**, without repeating the platform-level landscape.
- This document is the parent document of all component-level business_desc documents; component documents should link back to this one.
- Platform-level documents use `# level-1 headings` for sections (unlike component-level documents, which use `## 2 xxx`),
  with the first chapter being "Index" and chapter 0 being "Document Description".
-->

- [0 Document Description](#0-document-description)
- [1 Platform Overview](#1-platform-overview)
  - [1.1 Platform Introduction](#11-platform-introduction)
  - [1.2 Product Components Overview](#12-product-components-overview)
    - [1.2.1 {ComponentName} ({component_code_name})](#121-componentname-component_code_name)
    - [1.2.2 {ComponentName} ({component_code_name})](#122-componentname-component_code_name)
  - [1.3 Primary User Roles](#13-primary-user-roles)
    - [1.3.1 {Role1}](#131-role1)
    - [1.3.2 {Role2}](#132-role2)
- [2 Business Process](#2-business-process)
  - [2.1 Business Process Diagram](#21-business-process-diagram)
  - [2.2 Business Process Summary](#22-business-process-summary)
- [3 Project Directory Plan](#3-project-directory-plan)
- [4 Appendix](#4-appendix)
  - [4.1 Glossary](#41-glossary)

---

# 0 Document Description

| Item | Content |
|------|------|
| **Document Name** | {PlatformName} Business & Product Overview (business_overview.md) |
| **Document Level** | Platform-level — describes the business landscape of the entire platform |
| **Document Version** | {x.y.z} |
| **Intended Readers** | Product managers, architects, AI developers, newly onboarded engineers |
| **Child Documents** | Each component's `docs/components/{component}/{component}_business_desc.md` |
| **Corresponding Technical Document** | [technical_overview.md](./technical_overview.md) |

> **Document Boundary**: This document only describes "what the business is, who uses it, how it flows" and contains no technical implementation detail
> (technology stack, database, API all belong in `technical_overview.md`).

---

# 1 Platform Overview

## 1.1 Platform Introduction

{Use 3–8 paragraphs to explain: what business problem the platform solves, what industry/scenario it targets, its core value proposition,
and how it differs from traditional approaches. Avoid technical jargon.}

**Core Platform Capabilities**:

| # | Capability | Description |
|---|------|------|
| 1 | {Capability 1} | {Description} |
| 2 | {Capability 2} | {Description} |

## 1.2 Product Components Overview

The platform consists of the following product components, each of which can be designed, developed, and released independently:

| # | Component Name | Component Code Name | One-line Responsibility | Primary User Roles |
|---|-----------|-----------|-----------|-------------|
| 1 | {Unified Portal} | `unified_portal` | {Responsibility} | {Role} |
| 2 | {Component 2} | `{component_code_name}` | {Responsibility} | {Role} |

### 1.2.1 {ComponentName} ({component_code_name})

{2–5 paragraphs of business description: what responsibility this component holds in the overall business chain, who its upstream is, who its downstream is.}

**Key Features**:

- {Feature 1}
- {Feature 2}

**Upstream/Downstream Relationships**:

| Direction | Connected Component | Interaction Content |
|------|---------|---------|
| Upstream | {component_x} | {Input content} |
| Downstream | {component_y} | {Output content} |

> For a detailed business description, see [{component_code_name}_business_desc.md](./components/{component_code_name}/{component_code_name}_business_desc.md).

### 1.2.2 {ComponentName} ({component_code_name})

{Same structure as above}

> Repeat section 1.2.x for each component.

## 1.3 Primary User Roles

| Role | English Identifier | Core Needs | Primary Components Used |
|------|---------|---------|---------------|
| {Role1} | `{role_1}` | {Need} | {Component list} |
| {Role2} | `{role_2}` | {Need} | {Component list} |

### 1.3.1 {Role1}

{This role's profile, day-to-day work, and typical sequence of operations on the platform.}

**Typical Tasks**:

| # | Task | Involved Component | Frequency |
|---|------|---------|------|
| 1 | {Task} | {Component} | {Daily/Weekly/On demand} |

### 1.3.2 {Role2}

{Same structure as above}

---

# 2 Business Process

## 2.1 Business Process Diagram

![Business Process Diagram](./business_process.png)

> Source file: [business_process.mmd](./business_process.mmd)
> Drawing conventions: see [templates/mermaid/mmd_style_guide.md](../templates/mermaid/mmd_style_guide.md):
> white canvas, light-colored boxes categorized by role/type, all text in black.

## 2.2 Business Process Summary

The end-to-end business process is numbered by step, with each step indicating the **executing role** and **owning component**:

| Step | Name | Executing Role | Owning Component | Input | Output |
|------|------|---------|---------|------|------|
| 1 | {StepName} | {Role} | {Component} | {Input} | {Output} |
| 2 | {StepName} | {Role} | {Component} | {Input} | {Output} |

**Step 1 — {StepName}**

{Business description of this step: what the role did, what the system did, what was produced, and under what conditions it proceeds to the next step.}

**Step 2 — {StepName}**

{Same as above}

> Repeat for each step. If the process has branches or loops (e.g., a review that fails and is sent back for revision), the branch condition
> and the target step to roll back to must be explicitly stated here, and must stay consistent with the connections in `business_process.mmd`.

---

# 3 Project Directory Plan

For the platform's complete directory plan (source code, documentation, deployment), see the root-level
[project_directory_plan.md](../project_directory_plan.md).

This document only lists the documentation directories relevant to understanding the business:

```
docs/
├── business_overview.md              # This file — platform-level business overview
├── technical_overview.md             # Platform-level technical overview
├── uiux_design_specification.md      # Platform-level UI/UX specification
├── naming_convention.md              # Platform-level naming convention
└── components/
    └── {component_code_name}/
        └── {component_code_name}_business_desc.md   # Component-level business description
```

---

# 4 Appendix

## 4.1 Glossary

| Term | English / Code Name | Definition |
|------|--------------|------|
| {Term1} | `{term_1}` | {Definition} |
| {Term2} | `{term_2}` | {Definition} |

> The platform-level glossary is the **authoritative** definition. If the same term appears in component-level documents, it must be consistent with this table;
> component-private terms should be placed in that component document's own Glossary.

---

## Change Log

| Version | Date | Change Description |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {Change description} |
