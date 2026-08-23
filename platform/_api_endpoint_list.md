# API Endpoint 汇总

<!--
模板说明（使用时删除本注释块）
- 层级：**平台级 API 汇总**。放在 `docs/APIs/` 目录，文件名去掉前导下划线 → `api_endpoint_list.md`。
- 与组件级 `{component}_tech_design.md` §5 的区别（务必守住边界）：

  | 维度 | 本文（平台级 api_endpoint_list.md） | 组件级 tech_design.md §5 |
  |------|-----------------------------------|-------------------------|
  | 内容 | 全平台端点的**一行式清单** | 每个端点的**完整定义**（参数、示例、响应、错误码） |
  | 用途 | 查重、查冲突、看全局；给前端/网关做路由总览 | 给 AI 开发者写代码 |
  | 权威性 | **派生文档** —— 从组件文档汇总而来 | **权威定义** |
  | 更新时机 | 组件端点增删改之后同步 | 端点设计时 |

- 本文是派生文档：出现不一致时**以组件 tech_design.md 为准**，并立即修正本文。
- 每次组件 API 变更后，必须同步更新本文，并在 review 中把"端点总数一致性"列为检查项。
- 建议同时核对第三处：`src/api/{component}/openapi.yaml` 与组件的 API 调试页端点分组。
-->

本文件汇总「{平台中文名}」各产品组件后端服务对外暴露的**全部 REST API**。

| 项目 | 说明 |
|------|------|
| **文档层级** | 平台级（派生文档） |
| **文档版本** | {x.y.z} |
| **部署形态** | 每个组件独立部署、独立监听端口 |
| **统一前缀** | `/api/v{n}/` |
| **权威来源** | 各组件的 `{component_code_name}_tech_design.md` §5 |
| **同步要求** | 组件端点增删改后**必须**同步本文；不一致时以组件文档为准 |

> 各接口的详细请求/响应定义请参阅对应组件的 `tech_design.md`。
> 通用响应格式、分页格式和全局错误码见
> [technical_overview.md](../technical_overview.md) §4.1。

---

## 0 端点总览

| # | 组件 | 端口 | 端点数 | tech_design 章节 | OpenAPI 契约 |
|---|------|------|--------|-----------------|-------------|
| 1 | `{component_code_name_1}` | {8080} | {n} | [§5](../components/{component_code_name_1}/{component_code_name_1}_tech_design.md) | `src/api/{component_code_name_1}/openapi.yaml` |
| 2 | `{component_code_name_2}` | {8081} | {n} | [§5](../components/{component_code_name_2}/{component_code_name_2}_tech_design.md) | `src/api/{component_code_name_2}/openapi.yaml` |
| | **合计** | | **{N}** | | |

**跨模块调用登记**（一个组件调用另一个组件端点的情况，必须在此登记）：

| # | 调用方组件 | 被调用组件 | 被调用端点 | 用途 |
|---|-----------|-----------|-----------|------|
| 1 | `{caller_component}` | `{owner_component}` | `{METHOD} /api/v1/{path}` | {用途} |

> 跨模块端点在**双方**的组件文档中都要出现：拥有方定义它，调用方在 UiUx 的
> Page-Object-API Mapping 和 API 调试页中以「跨模块调用」分组展示。

---

## 1 {component_code_name_1}（port {8080}）

> 权威定义：[{component_code_name_1}_tech_design.md §5](../components/{component_code_name_1}/{component_code_name_1}_tech_design.md)

### 1.1 {资源组名称，如 SourceMedia CRUD}

| Method | Endpoint | Description | tech_design |
|--------|----------|-------------|-------------|
| GET | `/api/v1/{resource}` | {List {resource}} | §5.1.1 |
| GET | `/api/v1/{resource}/:{id}` | {Get {resource} by ID} | §5.1.2 |
| POST | `/api/v1/{resource}` | {Create {resource}} | §5.1.3 |
| PUT | `/api/v1/{resource}/:{id}` | {Update {resource}} | §5.1.4 |
| DELETE | `/api/v1/{resource}/:{id}` | {Soft delete {resource}} | §5.1.5 |

### 1.2 {资源组名称，如 Material Record 查询}

| Method | Endpoint | Description | tech_design |
|--------|----------|-------------|-------------|
| GET | `/api/v1/{resource}/{path_param}` | {List} | §5.2.1 |
| DELETE | `/api/v1/{resource}/{path_param}/:{id}` | {Delete one} | §5.2.3 |
| POST | `/api/v1/{resource}/{path_param}/batch-delete` | {Batch delete} | §5.2.4 |

> 按资源组数量重复 1.x 小节。**批量操作**统一以子资源动词表达
> （`batch-delete` / `batch-cancel`），不使用查询参数区分。

---

## 2 {component_code_name_2}（port {8081}）

> 权威定义：[{component_code_name_2}_tech_design.md §5](../components/{component_code_name_2}/{component_code_name_2}_tech_design.md)

### 2.1 {资源组名称}

| Method | Endpoint | Description | tech_design |
|--------|----------|-------------|-------------|
| GET | `/api/v1/{resource}` | {描述} | §5.1.1 |

> 按组件数量重复第 2、3、4… 章。

---

## {n} 一致性检查清单

每次修改本文或任一组件的 API 定义后，按下表逐项核对（结果记入
`ai_dev_history/01_DocReviewRefine/` 的 review 报告）：

| # | 检查项 | 通过标准 |
|---|--------|---------|
| 1 | 端点总数一致 | 本文 = 组件 tech_design §5 = openapi.yaml = API 调试页 |
| 2 | 端点路径一致 | 四处路径逐字相同（含路径参数名） |
| 3 | HTTP 方法一致 | 四处方法相同 |
| 4 | 端口无冲突 | 各组件监听端口两两不同 |
| 5 | 前缀规范 | 全部为 `/api/v{n}/`，资源名为复数 kebab-case |
| 6 | 跨模块端点双向登记 | 拥有方与调用方文档都有记录 |
| 7 | UiUx 引用一致 | 组件 uiux 的 Page-Object-API Mapping 中所有端点都能在本文找到 |
| 8 | 无残留占位 | 本文无 `{待补充}` / `(TBD)` 标记 |

---

## Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {变更说明} |
