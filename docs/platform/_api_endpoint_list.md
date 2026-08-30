# API Endpoint Summary

<!--
TEMPLATE NOTES (delete this comment block when using)
- Layer: **platform-level API summary**. Place in the `docs/APIs/` directory, drop the leading underscore in the filename → `api_endpoint_list.md`.
- Distinction from component-level `{component}_tech_design.md` §5 (this boundary must be kept):

  | Dimension | This document (platform-level api_endpoint_list.md) | Component-level tech_design.md §5 |
  |------|-----------------------------------|-------------------------|
  | Content | A **one-line-per-endpoint list** of all platform endpoints | The **complete definition** of each endpoint (parameters, examples, responses, error codes) |
  | Purpose | Duplicate/conflict checking, global view; a routing overview for frontend/gateway | Source for AI developers to write code from |
  | Authority | **Derived document** — compiled from component documents | **Authoritative definition** |
  | Update timing | Synced after component endpoints are added/removed/changed | At endpoint design time |

- This document is a derived document: on any inconsistency, **the component's tech_design.md takes precedence**, and this document must be corrected immediately.
- After every component API change, this document must be updated in sync, and "endpoint count consistency" must be included as a review checklist item.
- It is also recommended to cross-check a third location: `src/api/{component}/openapi.yaml` and the component's API debug page endpoint groupings.
-->

This document summarizes **all REST APIs** exposed by the backend services of every product component in "{PlatformName}".

| Item | Description |
|------|------|
| **Document Layer** | Platform-level (derived document) |
| **Document Version** | {x.y.z} |
| **Deployment Form** | Each component deployed independently, listening on its own port |
| **Unified Prefix** | `/api/v{n}/` |
| **Authoritative Source** | Each component's `{component_code_name}_tech_design.md` §5 |
| **Sync Requirement** | This document **must** be synced after component endpoints are added/removed/changed; on inconsistency, the component document takes precedence |

> For the detailed request/response definitions of each API, refer to the corresponding component's `tech_design.md`.
> For the unified response format, pagination format, and global error codes, see
> [technical_overview.md](../technical_overview.md) §4.1.

---

## 0 Endpoint Overview

| # | Component | Port | Endpoint Count | tech_design Section | OpenAPI Contract |
|---|------|------|--------|-----------------|-------------|
| 1 | `{component_code_name_1}` | {8080} | {n} | [§5](../components/{component_code_name_1}/{component_code_name_1}_tech_design.md) | `src/api/{component_code_name_1}/openapi.yaml` |
| 2 | `{component_code_name_2}` | {8081} | {n} | [§5](../components/{component_code_name_2}/{component_code_name_2}_tech_design.md) | `src/api/{component_code_name_2}/openapi.yaml` |
| | **Total** | | **{N}** | | |

**Cross-module call registry** (cases where one component calls another component's endpoint must be registered here):

| # | Calling Component | Called Component | Called Endpoint | Purpose |
|---|-----------|-----------|-----------|------|
| 1 | `{caller_component}` | `{owner_component}` | `{METHOD} /api/v1/{path}` | {Purpose} |

> A cross-module endpoint must appear in **both parties'** component documents: the owner defines it, and the caller shows it
> as a "cross-module call" group in both the UiUx Page-Object-API Mapping and its API debug page.

---

## 1 {component_code_name_1} (port {8080})

> Authoritative definition: [{component_code_name_1}_tech_design.md §5](../components/{component_code_name_1}/{component_code_name_1}_tech_design.md)

### 1.1 {Resource group name, e.g. SourceMedia CRUD}

| Method | Endpoint | Description | tech_design |
|--------|----------|-------------|-------------|
| GET | `/api/v1/{resource}` | {List {resource}} | §5.1.1 |
| GET | `/api/v1/{resource}/:{id}` | {Get {resource} by ID} | §5.1.2 |
| POST | `/api/v1/{resource}` | {Create {resource}} | §5.1.3 |
| PUT | `/api/v1/{resource}/:{id}` | {Update {resource}} | §5.1.4 |
| DELETE | `/api/v1/{resource}/:{id}` | {Soft delete {resource}} | §5.1.5 |

### 1.2 {Resource group name, e.g. Material Record Query}

| Method | Endpoint | Description | tech_design |
|--------|----------|-------------|-------------|
| GET | `/api/v1/{resource}/{path_param}` | {List} | §5.2.1 |
| DELETE | `/api/v1/{resource}/{path_param}/:{id}` | {Delete one} | §5.2.3 |
| POST | `/api/v1/{resource}/{path_param}/batch-delete` | {Batch delete} | §5.2.4 |

> Repeat section 1.x for each resource group. **Batch operations** are always expressed as sub-resource verbs
> (`batch-delete` / `batch-cancel`), not distinguished via query parameters.

---

## 2 {component_code_name_2} (port {8081})

> Authoritative definition: [{component_code_name_2}_tech_design.md §5](../components/{component_code_name_2}/{component_code_name_2}_tech_design.md)

### 2.1 {Resource group name}

| Method | Endpoint | Description | tech_design |
|--------|----------|-------------|-------------|
| GET | `/api/v1/{resource}` | {Description} | §5.1.1 |

> Repeat for chapters 2, 3, 4… per component.

---

## {n} Consistency Checklist

After every change to this document or any component's API definitions, check each item in the table below (record the result in
the review report under `ai_dev_history/01_DocReviewRefine/`):

| # | Check Item | Passing Criteria |
|---|--------|---------|
| 1 | Endpoint count consistent | This document = component tech_design §5 = openapi.yaml = API debug page |
| 2 | Endpoint paths consistent | Identical paths across all four locations (including path parameter names) |
| 3 | HTTP methods consistent | Identical methods across all four locations |
| 4 | No port conflicts | Each component's listening port is pairwise distinct |
| 5 | Prefix convention | All are `/api/v{n}/`, resource names are plural kebab-case |
| 6 | Cross-module endpoints bidirectionally registered | Recorded in both the owner's and the caller's documents |
| 7 | UiUx reference consistency | Every endpoint in the component's UiUx Page-Object-API Mapping can be found in this document |
| 8 | No leftover placeholders | This document contains no `{TBD}` / `(TBD)` markers |

---

## Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {Description of change} |
