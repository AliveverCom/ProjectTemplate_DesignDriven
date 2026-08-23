<!--
模板说明（使用时删除本注释块）

【文档层级】
本文件是 **组件级（Component-Level）技术文档** 模板。

- 存放位置：`docs/components/{component_code_name}/`
- 文件命名：将前导 `_` 替换为组件代码名。
  例如组件 `crawler` → `crawler_tech_design.md`

【与平台级文档的边界区分】
`docs/` 根目录下的 `technical_overview.md` 是 **平台级** 技术文档，两者层级完全不同：

| 维度 | 平台级 `docs/technical_overview.md` | 组件级 `{component}_tech_design.md`（本模板） |
|------|-------------------------------------|-------------------------------------------|
| 描述对象 | 整个平台的技术架构 | 单个产品组件的技术实现 |
| 内容 | 架构分层、后端服务架构模式、跨模块 API 调用规则、数据库划分、全局技术规范（API/DB/前端/后端/日志/安全/版本） | 本组件的类定义、数据库表、API 端点、源码目录 |
| 技术栈 | 全平台统一的语言版本、依赖版本、中间件版本的**权威清单** | 只声明本组件用到了其中哪些，版本必须与平台级一致 |
| 规范 | 定义规范本身 | 引用规范章节号，说明本组件如何遵循 |

**硬性规则**：
1. 组件级文档 **不得重复定义** 平台级已有的技术规范（统一响应格式、错误码体系、
   命名规范、日志规范、分层规则等）。需要时以
   `遵循 [technical_overview.md](../../technical_overview.md) §4.x` 的形式引用。
2. 技术栈版本 **不得与** `technical_overview.md §5` 冲突。若本组件需要新的语言或
   主要依赖，必须先更新平台级文档，再在本文引用。
3. 本文 §2 的每个类，必须能在 `{component}_business_desc.md §3 Business Objects`
   中找到对应的业务对象；两边的属性集合必须一致。
4. 本文 §5 定义的每个端点，必须与 `{component}_uiux.md §2.3 Page-Object-API Mapping`
   中引用的端点路径完全一致，并同步反映在 `{component}_api.mmd` 中。

【文档约定】
- 第 1 章必须是 Table of Contents。
- 所有章节使用数字编号（1、1.1、1.1.1）。
- `{花括号}` 内为占位符，使用时替换为实际内容。
- 可选章节在正文中已标注保留/删除条件，不需要的整章删除并重新编号。
-->

# {组件中文名}（{component_code_name}） Technical Design

**Document Version**: {x.y.z}

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Class Definitions](#2-class-definitions)
  - [2.1 Class Diagram](#21-class-diagram)
  - [2.2 {CClassName1}](#22-cclassname1)
  - [2.3 {CClassName2}](#23-cclassname2)
  - [2.4 {CBaseClass} (Reference)](#24-cbaseclass-reference)
  - [2.5 Enum Definitions](#25-enum-definitions)
- [3 Software Program Descriptions](#3-software-program-descriptions)
  - [3.1 {ProgramName1}](#31-programname1)
  - [3.2 {ProgramName2}](#32-programname2)
- [4 Database Definitions](#4-database-definitions)
  - [4.0 Database Schema Diagram](#40-database-schema-diagram)
  - [4.1 {table_name_1}](#41-table_name_1)
  - [4.2 {code_name}_{suffix}（动态分表）](#42-code_name_suffix动态分表)
- [5 API Definitions](#5-api-definitions)
  - [5.0 API Overview Diagram](#50-api-overview-diagram)
  - [5.1 {Resource1} CRUD](#51-resource1-crud)
  - [5.2 {Resource2} 查询](#52-resource2-查询)
  - [5.3 通用分页响应格式](#53-通用分页响应格式)
  - [5.4 通用错误码定义](#54-通用错误码定义)
  - [5.5 请求/响应 JSON Schema 示例](#55-请求响应-json-schema-示例)
- [6 Development Technology](#6-development-technology)
  - [6.1 Operating System](#61-operating-system)
  - [6.2 Software Form](#62-software-form)
  - [6.3 Programming Language](#63-programming-language)
  - [6.4 Major Dependencies](#64-major-dependencies)
  - [6.5 Release Form](#65-release-form)
- [7 Environment Configuration](#7-environment-configuration)
  - [7.1 环境变量清单](#71-环境变量清单)
  - [7.2 配置文件格式](#72-配置文件格式)
- [8 Build & Run](#8-build--run)
- [9 Server Parameters](#9-server-parameters)
- [10 Source Code Directory Layout](#10-source-code-directory-layout)
- [11 Appendix](#11-appendix)
  - [11.1 Glossary](#111-glossary)
  - [11.2 code_name 命名规则](#112-code_name-命名规则)
  - [11.3 输入验证规则](#113-输入验证规则)
  - [11.4 日志规范](#114-日志规范)
  - [11.5 测试策略](#115-测试策略)
- [Change Log](#change-log)

---

## 2 Class Definitions

> 类型命名遵循 [naming_convention.md](../../naming_convention.md)：
> 类以 `C` 前缀、枚举以 `E` 前缀、接口以 `I` 前缀、配置属性以 `cfg` 前缀。

### 2.1 Class Diagram

![Class Diagram](./{component_code_name}_class_diagram.png)

> 源文件：[{component_code_name}_class_diagram.mmd](./{component_code_name}_class_diagram.mmd)
>
> 绘图规范见 [templates/mermaid/](../../../templates/mermaid/)：
> 白色画布、类框按类型（本模块类 / 引用类 / 枚举 / 抽象基类）归类着色、所有文字为黑色。
> 引用自其他模块的类使用虚线边框，并在图上标注 `<<ref: {module}>>`。

**本图中的类归属**：

| 类 | 归属模块 | 图中表现 |
|----|---------|---------|
| {CClassName1} | 本模块 | 实线框 |
| {CClassName2} | 本模块 | 实线框 |
| {CBaseClass} | `{other_module}` | 虚线框，`<<abstract - ref: {other_module}>>` |
| {EEnumName1} | 本模块 | 实线框，`<<enumeration>>` |
| {EEnumName2} | `{other_module}` | 虚线框，`<<enumeration - ref: {other_module}>>` |

### 2.2 {CClassName1}

**映射业务对象**：[{component_code_name}_business_desc.md](./{component_code_name}_business_desc.md) §3.1 {ObjectName1}（{对象中文名1}）

{1–3 句说明这个类的职责、生命周期、被哪些 service 使用。}

| Member | Type | UI/DB Display or Storage Requirements | Description |
|--------|------|---------------------------------------|-------------|
| {member_id} | {int} | {PK, auto-increment；列表页右对齐显示，不加 `#` 前缀} | {业务含义} |
| {display_name} | {string} | {NOT NULL UNIQUE；列表页第 1 列，14px 粗体} | {业务含义} |
| {code_name} | {string} | {NOT NULL UNIQUE；创建后不可变，是所有分表命名依据} | {业务含义；命名规则见 §11.2} |
| {some_enum} | {EEnumName1} | {NOT NULL；以 Tag 徽章展示} | {业务含义；枚举定义见 §2.5.1} |
| {some_json} | {CSubStruct} | {NOT NULL, JSONB；子字段需建表达式索引} | {业务含义} |
| {is_active} | {bool} | {NOT NULL DEFAULT true；false 时列表行使用 inactive 样式} | {业务含义} |
| {deleted_at} | {datetime?} | {NULL 表示未删除；非 NULL 时列表行使用 soft-deleted 样式} | {软删除时间戳} |
| {created_at} | {datetime} | {NOT NULL DEFAULT NOW()；只读} | {创建时间} |
| {updated_at} | {datetime} | {NOT NULL DEFAULT NOW()；auto-update via DB trigger；只读} | {最后更新时间} |

**字段级约束**：

| # | 字段 | 约束 | 说明 |
|---|------|------|------|
| 1 | {code_name} | 创建后不可修改 | {原因：作为分表名前缀，修改会导致数据丢失} |
| 2 | {stat_field} | 系统计算值，API 不可写 | {由 {daemon_name} 定期刷新} |

### 2.3 {CClassName2}

**映射业务对象**：[{component_code_name}_business_desc.md](./{component_code_name}_business_desc.md) §3.2 {ObjectName2}（{对象中文名2}）

{CClassName2} 继承自 {CBaseClass}（见 §2.4）。下表**只列出本类新增的成员**，
继承成员见 §2.4。

| Member | Type | UI/DB Display or Storage Requirements | Description |
|--------|------|---------------------------------------|-------------|
| {member1} | {bool} | {NOT NULL DEFAULT false} | {业务含义} |
| {member2} | {string?} | {NULL；仅当 {member1} = true 时必填；UI 中密码类字段需遮掩显示} | {业务含义} |

### 2.4 {CBaseClass} (Reference)

> **引用类写法**：本类的**权威定义不在本模块**。此处仅为完整展示继承关系而复制，
> 任何修改都必须先在权威模块中进行，再同步到此处。

| 项目 | 内容 |
|------|------|
| **权威定义模块** | `{other_module}` |
| **权威定义文件** | [{other_module}_tech_design.md](../{other_module}/{other_module}_tech_design.md) §2.{n} |
| **权威类图** | [{other_module}_class_diagram.mmd](../{other_module}/{other_module}_class_diagram.mmd) |
| **本模块使用方式** | {如：{CClassName2} 继承自本类；本模块的 {table_name} 表包含其全部列} |

| Member | Type | UI/DB Display or Storage Requirements | Description |
|--------|------|---------------------------------------|-------------|
| {base_member1} | {int} | {PK, auto-increment} | {业务含义} |
| {base_member2} | {string} | {NOT NULL UNIQUE} | {业务含义} |

> **同步检查项**：每次 review 必须核对本节字段与权威模块定义是否一致。
> 历史上"基类字段在一个模块改了、另一个模块没改"是最高频的跨模块文档缺陷。

### 2.5 Enum Definitions

> 枚举的**业务解释**见 [{component_code_name}_business_desc.md](./{component_code_name}_business_desc.md) §6.2。
> 本节是**技术权威定义**：值的字面量、存储类型、默认值。

#### 2.5.1 {EEnumName1}

| 项目 | 内容 |
|------|------|
| **权威定义** | 本模块 |
| **存储类型** | {TEXT}（存储枚举字面量，不使用数据库 enum 类型） |
| **默认值** | `{DEFAULT_VALUE}` |
| **使用位置** | {§2.2 {CClassName1}.{member}；§4.1 {table_name}.{column}} |

| Value | 含义 | 说明 |
|-------|------|------|
| `{VALUE_1}` | {含义} | {何时取该值；是否为终态} |
| `{VALUE_2}` | {含义} | {说明} |
| `{VALUE_3}` | {含义} | {说明} |

#### 2.5.2 {EEnumName2}

| 项目 | 内容 |
|------|------|
| **权威定义** | {本模块 / `{other_module}`，见 [{other_module}_tech_design.md](../{other_module}/{other_module}_tech_design.md) §2.{n}} |
| **存储类型** | {TEXT} |
| **默认值** | `{DEFAULT_VALUE}` |
| **使用位置** | {§2.3 {CClassName2}.{member}} |

| Value | 含义 | 说明 |
|-------|------|------|
| `{VALUE_1}` | {含义} | {说明} |

---

## 3 Software Program Descriptions

> **可选章节**：仅当本组件包含 daemon 常驻进程、CLI 工具或多个可执行程序时保留本章。
> 纯 REST 服务 + 前端包的组件请整章删除，并将后续章节重新编号。

本组件包含以下可执行程序：

| # | 程序 | 形态 | 适用输入 | 触发方式 | 详细设计 |
|---|------|------|---------|---------|---------|
| 1 | {ProgramName1} | {daemon} | {适用的数据类型/配置条件} | {定时轮询 / 事件驱动} | [sub_{program_1}/{program_1}_design.md](./sub_{program_1}/{program_1}_design.md) |
| 2 | {ProgramName2} | {daemon} | {适用条件} | {触发方式} | [sub_{program_2}/{program_2}_design.md](./sub_{program_2}/{program_2}_design.md) |

> **设计拆分约定**：每个 daemon / CLI 程序的完整设计（详细主循环、状态机、错误处理、
> 配置项）拆分到 `sub_{program_name}/` 子目录下的独立设计文档。本章只保留**摘要**，
> 用于让读者快速理解本组件由哪些程序构成、各自的边界在哪里。
>
> **约束说明**：多个 daemon 之间的执行时序、并发冲突、共享数据的读写顺序，
> 若在业务上无害，应在本章引言明确声明"不纠结 daemon 间时序冲突"，
> 避免 review 反复纠缠于无实际影响的竞态讨论。

### 3.1 {ProgramName1}

| Item | Detail |
|------|--------|
| **职责** | {一句话说明} |
| **适用输入** | {加载条件，如：`{table}` 中 `is_active = true 且 deleted_at IS NULL` 的记录} |
| **触发方式** | {如：以最小触发间隔轮询；每轮重新加载配置列表} |
| **并发模型** | {单进程单线程 / 主进程 + 每输入一个子进程 / 协程池} |
| **产出** | {写入哪些表；产生哪些日志} |

**主循环步骤**：

| # | 步骤 | 说明 |
|---|------|------|
| 1 | {加载配置} | {从哪里加载；多久重载一次；如何感知新增/停用/删除} |
| 2 | {判定触发} | {判定条件；跳过条件} |
| 3 | {执行任务} | {做什么；写入哪些记录} |
| 4 | {清理} | {清理什么；如何处理已停用/已删除输入对应的遗留任务} |

**失败重试规则**：

| 失败类型 | 重试策略 | 上限 | 超限后行为 |
|---------|---------|------|-----------|
| {网络超时} | {进程内立即重试} | {n 次} | {标记任务为 {状态}，写入错误日志} |
| {解析失败} | {不重试} | — | {标记任务为 {状态}} |

**已知局限**：

| # | 局限 | 影响 | 备注 |
|---|------|------|------|
| 1 | {局限} | {影响} | {是否计划在后续版本解决} |

### 3.2 {ProgramName2}

{同上结构。}

---

## 4 Database Definitions

> 数据库划分、命名规范、软删除约定、索引要求遵循
> [technical_overview.md](../../technical_overview.md) §4.2。
>
> 本组件独立数据库：`{db_name}`。**禁止跨库 JOIN**，跨模块数据一律通过 REST API 获取。

### 4.0 Database Schema Diagram

![Database Schema](./{component_code_name}_db_schema.png)

> 源文件：[{component_code_name}_db_schema.mmd](./{component_code_name}_db_schema.mmd)
>
> 绘图规范见 [templates/mermaid/](../../../templates/mermaid/)：
> 白色画布、表框按用途（配置表 / 业务数据表 / 动态分表模板 / 引用表）归类着色、所有文字为黑色。

### 4.1 {table_name_1}

{1–2 句说明这张表的用途、写入方、读取方、数据量级预期。}

| Column | Type | Constraints | Index | Description |
|--------|------|-------------|-------|-------------|
| {col_id} | {BIGSERIAL} | {PK} | {PK} | {业务含义} |
| {display_name} | {TEXT} | {NOT NULL, UNIQUE} | {UNIQUE} | {业务含义} |
| {code_name} | {TEXT} | {NOT NULL, UNIQUE} | {UNIQUE} | {业务含义；创建后不可变，命名规则见 §11.2} |
| {some_enum} | {TEXT} | {NOT NULL} | {BTREE} | {存储 {EEnumName1} 字面量，见 §2.5.1} |
| {some_json} | {JSONB} | {NOT NULL} | {子字段表达式索引：`(({some_json}->>'{sub_field}')::int)`} | {业务含义} |
| {is_active} | {BOOLEAN} | {NOT NULL DEFAULT true} | {BTREE} | {业务含义} |
| {deleted_at} | {TIMESTAMPTZ} | {NULL} | {BTREE} | {软删除时间戳；NULL 表示未删除} |
| {created_at} | {TIMESTAMPTZ} | {NOT NULL DEFAULT NOW()} | {BTREE} | {创建时间} |
| {updated_at} | {TIMESTAMPTZ} | {NOT NULL DEFAULT NOW()} | {None} | {最后更新时间；auto-update via DB trigger} |

> **索引强制要求**：任何出现在 API 的 `WHERE` 过滤条件或 `ORDER BY` 排序字段中的列，
> 都必须在本表标注索引类型。Index 列填 `None` 的字段不得出现在 §5 的
> 查询参数或排序参数中——这是 review 的固定检查项。

**索引汇总**：

| 索引名 | 类型 | 字段 | 用途 |
|--------|------|------|------|
| `{idx_name_1}` | {BTREE} | {col} | {支撑 §5.1.1 的 {param} 过滤} |
| `{idx_name_2}` | {BTREE} | {col_a, col_b} | {支撑 §5.1.1 的复合排序} |

### 4.2 {code_name}_{suffix}（动态分表）

> **动态分表模板写法**：本组件按 {分表维度} 动态创建数据表，表名模板为
> `{code_name}_{suffix}`，其中 `{code_name}` 取自 §4.1 `{table_name_1}.code_name`。

| 项目 | 内容 |
|------|------|
| **表名模板** | `{code_name}_{suffix}` |
| **分表维度** | {如：每个信息源一张表} |
| **创建时机** | {如：该 {维度对象} 首次执行 {动作} 时由 {daemon_name} 动态创建} |
| **表不存在时的处理** | {单表查询 API 返回 404；跨表聚合 API 跳过不存在的表，不报错} |
| **建表安全** | {表名由 `code_name` 拼接，必须先按 §11.2 的正则校验，防止 SQL 注入} |
| **清理策略** | {如：{维度对象} 被软删除后，其分表保留 / 由 {daemon} 清理} |

**表结构**：

| Column | Type | Constraints | Index | Description |
|--------|------|-------------|-------|-------------|
| {col_id} | {BIGSERIAL} | {PK} | {PK} | {业务含义} |
| {parent_code_name} | {TEXT} | {NOT NULL} | {BTREE} | {冗余存储所属 {维度对象} 的 code_name} |
| {exe_status} | {TEXT} | {NOT NULL} | {BTREE} | {存储 {EEnumName} 字面量} |
| {exe_name} | {TEXT} | {NOT NULL} | {BTREE} | {写入该记录的程序名；支撑按程序名查询} |
| {created_at} | {TIMESTAMPTZ} | {NOT NULL DEFAULT NOW()} | {BTREE} | {创建时间} |

> 按表数量增删 4.x 小节。

---

## 5 API Definitions

> API 设计规范（前缀、资源命名、方法语义、统一响应体、批量操作命名）遵循
> [technical_overview.md](../../technical_overview.md) §4.1。
>
> 本组件对外 API 前缀：`/api/v1/`，监听端口见 §9。
>
> **跨模块调用说明**：本组件的 {列出被跨模块调用的端点} 也被 `{other_module}` 调用，
> 依据 [technical_overview.md](../../technical_overview.md) §4.{n}。这些端点必须在
> 调用方的 API 调试页中以"跨模块调用"分组展示。

**端点总览**：

| # | 分组 | 端点数 | 说明 |
|---|------|-------|------|
| 1 | §5.1 {Resource1} CRUD | {5} | {说明} |
| 2 | §5.2 {Resource2} 查询 | {4} | {说明} |
| | **合计** | **{n}** | |

> **一致性要求**：端点总数与路径必须在三处保持一致 ——
> 本章、`{component_code_name}_uiux.md` 的 API 调试页章节、`{component_code_name}_api.mmd`。

### 5.0 API Overview Diagram

![API Overview](./{component_code_name}_api.png)

> 源文件：[{component_code_name}_api.mmd](./{component_code_name}_api.mmd)
>
> 绘图规范见 [templates/mermaid/](../../../templates/mermaid/)：
> 白色画布、节点按类型（页面 / 本模块 API / 跨模块调用方）归类着色、所有文字为黑色。
> 每个 API 分组用一个 subgraph，subgraph 标题带上本文的章节号。

### 5.1 {Resource1} CRUD

{1–2 句说明这组接口服务于哪些页面、对应哪个业务对象。}

**可写字段范围**（POST / PUT 共用）：

| 类别 | 字段 | 说明 |
|------|------|------|
| **可写** | {列出全部可写字段} | {由客户端提交} |
| **不可写 — 系统生成** | {id}、{created_at}、{updated_at} | {由数据库生成} |
| **不可写 — 系统计算** | {stat_field_1}、{stat_field_2} | {由 {daemon_name} 定期刷新，客户端提交将被忽略} |
| **不可写 — 状态字段** | {deleted_at} | {只能通过 DELETE 端点变更} |
| **创建后不可变** | {code_name} | {见 §2.2 字段级约束} |

#### 5.1.1 获取{Resource1}列表

| Item | Detail |
|------|--------|
| **Endpoint** | `GET /api/v1/{resource-1}` |
| **Description** | {说明} |
| **Authentication** | {Required / Optional / None} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{filter_1}` | query | {string} | N | {过滤条件；对应 §4.1 的 {col}，已建 {BTREE} 索引} |
| `{filter_2}` | query | {bool} | N | {过滤条件} |
| `include_deleted` | query | {bool} | N | {默认 false；true 时返回软删除记录} |
| `keyword` | query | {string} | N | {模糊搜索字段范围：{col_a}、{col_b}；大小写不敏感} |
| `sort_by` | query | {string} | N | {**必须完整列举全部合法值**，禁止使用"等"字}：`{col_1}`、`{col_2}`、`{col_3}` |
| `sort_order` | query | {string} | N | `asc` / `desc`，默认 `{desc}` |
| `page` | query | {int} | N | {默认 1} |
| `page_size` | query | {int} | N | {默认 50，上限 {200}} |

> **排序字段特殊说明**：若某个 `sort_by` 值实际是 JSONB 子字段（如 `{sub_field}`），
> 必须在此注明 SQL 表达式映射：`ORDER BY (({some_json}->>'{sub_field}')::int)`，
> 否则实现方会误以为存在同名独立列。

**Request Example**:

```
GET /api/v1/{resource-1}?{filter_1}={value}&sort_by={col_1}&sort_order=asc&page=1&page_size=50
```

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [
      {
        "{col_id}": 1,
        "{display_name}": "{示例值}",
        "{code_name}": "{example_code_name}",
        "{some_enum}": "{VALUE_1}",
        "{is_active}": true,
        "{deleted_at}": null,
        "{created_at}": "2026-01-01T00:00:00Z",
        "{updated_at}": "2026-01-01T00:00:00Z"
      }
    ],
    "total": 1,
    "page": 1,
    "page_size": 50
  }
}
```

**Error Responses**:

| HTTP | code | 触发条件 |
|------|------|---------|
| 400 | {40001} | {参数格式非法，如 sort_by 不在合法值列表中} |
| 500 | {50001} | {服务器内部错误} |

#### 5.1.2 获取单个{Resource1}

| Item | Detail |
|------|--------|
| **Endpoint** | `GET /api/v1/{resource-1}/{id}` |
| **Description** | {说明} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{id}` | path | {int} | Y | {说明} |

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "{col_id}": 1,
    "{display_name}": "{示例值}"
  }
}
```

**Error Responses**:

| HTTP | code | 触发条件 |
|------|------|---------|
| 404 | {40401} | {记录不存在} |

#### 5.1.3 创建{Resource1}

| Item | Detail |
|------|--------|
| **Endpoint** | `POST /api/v1/{resource-1}` |
| **Description** | {说明} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{display_name}` | body | {string} | Y | {说明；长度 {1–128}} |
| `{code_name}` | body | {string} | Y | {说明；格式校验见 §11.2；创建后不可变} |
| `{some_enum}` | body | {string} | Y | {合法值见 §2.5.1} |

**字段级校验规则**：见 §11.3。服务端在写库前必须完成全部校验，不合规返回 400。

**Request Example**:

```json
{
  "{display_name}": "{示例值}",
  "{code_name}": "{example_code_name}",
  "{some_enum}": "{VALUE_1}"
}
```

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "{col_id}": 1
  }
}
```

**Error Responses**:

| HTTP | code | 触发条件 |
|------|------|---------|
| 400 | {40001} | {字段校验失败，message 中返回具体字段名} |
| 409 | {40901} | {`{code_name}` 或 `{display_name}` 已存在} |

#### 5.1.4 更新{Resource1}

| Item | Detail |
|------|--------|
| **Endpoint** | `PUT /api/v1/{resource-1}/{id}` |
| **Description** | {全量更新；可写字段范围见本节开头} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{id}` | path | {int} | Y | {说明} |
| `{display_name}` | body | {string} | Y | {说明} |

> **不可变字段说明**：请求体中若包含 `{code_name}`，服务端必须校验其与现有值一致，
> 不一致时返回 400；不得静默忽略。

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

#### 5.1.5 删除{Resource1}（软删除）

| Item | Detail |
|------|--------|
| **Endpoint** | `DELETE /api/v1/{resource-1}/{id}` |
| **Description** | {软删除：将 `deleted_at` 置为当前时间，记录不物理删除} |
| **Authentication** | {Required} |

**级联处理说明**：

| 关联数据 | 处理方式 |
|---------|---------|
| {关联表/分表} | {不做级联处理 / 将 {状态} 记录标记为 {终态}} |

> 必须明确写出"是否级联"，留空会导致实现方各自猜测。

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

### 5.2 {Resource2} 查询

{同 5.1 结构，按需增减端点。}

#### 5.2.1 {端点名}

| Item | Detail |
|------|--------|
| **Endpoint** | `{METHOD} /api/v1/{resource-2}/{path}` |
| **Description** | {说明} |
| **Authentication** | {Required} |

**Request Parameters**:

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| `{param}` | {path/query/body} | {type} | {Y/N} | {说明} |

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

#### 5.2.2 批量{动作}

| Item | Detail |
|------|--------|
| **Endpoint** | `POST /api/v1/{resource-2}/batch-{action}` |
| **Description** | {批量操作；单条操作时数组含 1 个元素} |
| **Authentication** | {Required} |

**Request Example**:

```json
{
  "{id_list}": [1, 2, 3]
}
```

**Response**:

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "succeeded": 3,
    "failed": 0
  }
}
```

> 按资源分组数量增删 5.x 小节。

### 5.3 通用分页响应格式

> 遵循 [technical_overview.md](../../technical_overview.md) §4.1.1。此处仅复述本组件的具体取值。

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "items": [],
    "total": 0,
    "page": 1,
    "page_size": 50
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `items` | array | {当前页记录} |
| `total` | int | {符合条件的总记录数} |
| `page` | int | {当前页码，从 1 开始} |
| `page_size` | int | {每页条数；本组件固定 {50}} |

### 5.4 通用错误码定义

> 全局错误码见 [technical_overview.md](../../technical_overview.md) §4.1.2。
> 本节只列出**本组件特有**的错误码，不得与全局错误码冲突。

| HTTP | code | message | 触发条件 |
|------|------|---------|---------|
| 400 | {40001} | {invalid parameter} | {参数校验失败} |
| 404 | {40401} | {not found} | {记录不存在；或动态分表尚未创建} |
| 409 | {40901} | {conflict} | {唯一约束冲突} |
| 500 | {50001} | {internal error} | {服务器内部错误} |

### 5.5 请求/响应 JSON Schema 示例

> 用于前端类型生成与 API 调试页的参数模板。完整 OpenAPI 契约见
> `src/apis/{component_code_name}/openapi.yaml`。

**{CClassName1} 完整 JSON 结构**：

```json
{
  "{col_id}": 1,
  "{display_name}": "{示例值}",
  "{code_name}": "{example_code_name}",
  "{some_enum}": "{VALUE_1}",
  "{some_json}": {
    "{sub_field_1}": 0,
    "{sub_field_2}": 0
  },
  "{is_active}": true,
  "{deleted_at}": null,
  "{created_at}": "2026-01-01T00:00:00Z",
  "{updated_at}": "2026-01-01T00:00:00Z"
}
```

**字段说明**：

| 字段 | JSON 类型 | 可空 | 说明 |
|------|----------|------|------|
| `{col_id}` | number | N | {说明} |
| `{some_json}` | object | N | {子字段结构说明} |
| `{deleted_at}` | string \| null | Y | {ISO 8601；null 表示未删除} |

---

## 6 Development Technology

> **版本一致性要求**：本章列出的语言版本、依赖版本、中间件版本**必须与**
> [technical_overview.md](../../technical_overview.md) §5 完全一致。
> 本组件若需要新的语言或主要依赖，必须先更新平台级文档，再在此引用。

### 6.1 Operating System

| OS | Version | Notes |
|----|---------|-------|
| {Linux} | {Ubuntu 22.04} | {生产} |
| {Windows} | {11} | {开发} |

### 6.2 Software Form

| Module | Form | Description |
|--------|------|-------------|
| {module_1} | {Service} | {REST API 服务} |
| {module_2} | {Daemon} | {常驻采集进程} |
| {module_3} | {Frontend} | {前端页面包} |

### 6.3 Programming Language

| Language | Version | Usage | 平台级对应章节 |
|----------|---------|-------|--------------|
| {Go} | {1.25.x} | {后端服务与 daemon} | technical_overview.md §5.1 |
| {TypeScript} | {5.x} | {前端} | technical_overview.md §5.1 |

### 6.4 Major Dependencies

| Dependency | Version | Purpose | 平台级对应章节 |
|-----------|---------|---------|--------------|
| {Gin} | {1.x} | {Web 框架} | technical_overview.md §5.3 |
| {GORM} | {2.x} | {ORM} | technical_overview.md §5.3 |
| {React} | {18.x} | {前端框架} | technical_overview.md §5.2 |
| {PostgreSQL} | {17.x} | {数据库} | technical_overview.md §5.5 |

### 6.5 Release Form

| Artifact | Form | Description |
|----------|------|-------------|
| {artifact_1} | {Binary} | {单一可执行文件} |
| {artifact_2} | {Static file directory} | {前端构建产物} |
| {artifact_3} | {Docker image} | {镜像名与 tag 规则} |

---

## 7 Environment Configuration

> **可选章节**：仅当本组件是可独立部署的服务/daemon（需要环境变量或配置文件）时保留。
> 纯前端包或纯类库请整章删除，并将后续章节重新编号。

### 7.1 环境变量清单

| 变量名 | 必填 | 默认值 | 说明 |
|--------|------|-------|------|
| `{COMPONENT}_DB_DSN` | Y | — | {数据库连接串} |
| `{COMPONENT}_PORT` | N | {8081} | {HTTP 监听端口} |
| `{COMPONENT}_LOG_LEVEL` | N | {INFO} | {日志级别：DEBUG/INFO/WARN/ERROR} |

> **敏感信息**：密码、Token、密钥一律通过环境变量注入，**禁止写入配置文件并提交仓库**，
> 遵循 [technical_overview.md](../../technical_overview.md) §4.6。

### 7.2 配置文件格式

配置文件路径：`{config/config.yaml}`

```yaml
server:
  port: {8081}
  read_timeout_ms: {5000}
  write_timeout_ms: {5000}

database:
  max_open_conns: {20}
  max_idle_conns: {5}

log:
  level: {INFO}
  format: {json}
```

| 配置项 | 类型 | 默认值 | 说明 |
|--------|------|-------|------|
| `server.port` | int | {8081} | {说明；可被环境变量覆盖} |
| `database.max_open_conns` | int | {20} | {说明} |

---

## 8 Build & Run

> **可选章节**：仅当本组件包含可构建的可执行产物时保留。

**构建**：

```bash
{build_command}
```

**运行（开发）**：

```bash
{run_dev_command}
```

**运行（生产）**：

```bash
{run_prod_command}
```

**健康检查**：

```bash
curl http://localhost:{8081}/healthz
```

| 检查项 | 端点 | 预期响应 |
|--------|------|---------|
| 存活 | `GET /healthz` | `{"code":0,"message":"success"}` |
| 就绪 | `GET /readyz` | {说明：包含数据库连通性检查} |

---

## 9 Server Parameters

> **可选章节**：仅当本组件是后端服务时保留。

| 参数 | 值 | 说明 |
|------|-----|------|
| 监听端口 | {8081} | {与 technical_overview.md §3 的端口分配表一致} |
| API 前缀 | `/api/v1/` | {遵循 technical_overview.md §4.1} |
| 读超时 | {5000 ms} | {说明} |
| 写超时 | {5000 ms} | {说明} |
| 数据库最大连接数 | {20} | {说明} |
| 数据库空闲连接数 | {5} | {说明} |
| 优雅停机等待 | {10 s} | {说明} |

---

## 10 Source Code Directory Layout

> 目录命名遵循 [naming_convention.md](../../naming_convention.md)：
> `src/backend/` 使用 snake_case，`src/frontend/` 使用 kebab-case。

**后端**：

```
src/backend/{component_code_name}/
├── cmd/                        # 各可执行程序入口
│   ├── server/                 # REST API 服务入口          [v0.2+]
│   └── {daemon_name}/          # daemon 程序入口             [v0.5+]
├── config/                     # 配置加载                     [v0.2+]
├── internal/
│   ├── handler/                # HTTP 层：参数解析、验证、响应封装  [v0.2+]
│   ├── service/                # 业务逻辑层                    [v0.2+]
│   ├── repository/             # 数据访问层（接口 + 实现）        [v0.2 接口 / v0.3 实现]
│   ├── model/                  # 数据结构定义（对应 §2 类定义）    [v0.2+]
│   ├── enum/                   # 枚举定义（对应 §2.5）           [v0.2+]
│   └── mock/                   # 硬编码 mock 数据               [v0.2 生产使用 / v0.3+ 仅测试]
├── pkg/                        # 可复用工具包（响应封装、错误码等） [v0.2+]
└── sql/                        # 数据库脚本                     [v0.3+]
    ├── init_databases.sql      # 建库脚本
    ├── migrations/             # 迁移脚本
    └── sample_data.sql         # 样例数据
```

**前端**：

```
src/frontend/packages/{component-code-name}/
├── src/
│   ├── pages/                  # 页面组件，对应 uiux.md 的 P01–P{nn}   [v0.1+]
│   │   └── {page-name}/
│   ├── components/             # 本包内复用组件                        [v0.1+]
│   ├── models/                 # TypeScript 类型定义（对应 §2 类定义）   [v0.1+]
│   ├── mock/                   # 硬编码数据                            [v0.1 使用 / v0.2+ 移除]
│   ├── services/               # API 客户端封装                        [v0.2+]
│   ├── hooks/                  # 数据钩子                              [v0.2+]
│   ├── stores/                 # 状态管理                              [v0.1+]
│   └── routes.tsx              # 路由注册（对应 uiux.md §4 路由定义）    [v0.1+]
└── package.json
```

**版本范围说明**：

| 版本 | 本组件的目录范围 |
|------|----------------|
| {v0.1} | {仅前端，`mock/` 提供全部数据，无 `services/`} |
| {v0.2} | {新增后端 `cmd/server`、`handler`、`service`、`model`、`mock`；前端新增 `services/`、`hooks/`，移除 `mock/` 引用} |
| {v0.3} | {新增 `repository` GORM 实现、`sql/`；`mock/` 降级为仅测试使用} |
| {v0.5+} | {新增 `cmd/{daemon_name}/` 各 daemon 程序} |

> 详细版本范围见 [{component_code_name}_version_plan.md](./{component_code_name}_version_plan.md)。

---

## 11 Appendix

### 11.1 Glossary

> 仅收录**本组件私有**的技术术语。平台通用术语见
> [technical_overview.md](../../technical_overview.md) §7.2。

| Term | Definition |
|------|-----------|
| {术语1} | {定义} |
| {术语2} | {定义} |

### 11.2 code_name 命名规则

`code_name` 是 {说明其用途，如：所有动态分表的命名依据}，因此必须严格约束格式。

| 项目 | 规则 |
|------|------|
| **正则** | `^[a-z][a-z0-9_]*$` |
| **长度** | {3–64} 字符 |
| **大小写** | 全小写 snake_case |
| **可变性** | 创建后不可修改 |
| **校验时机** | {前端提交前校验 + 服务端写库前强制校验}；不合规返回 400 |

**前端自动生成规则**：

| # | 规则 | 说明 |
|---|------|------|
| 1 | {display_name} 失焦时自动生成 | {全部转小写；空格与标点替换为下划线} |
| 2 | 含非 ASCII 字符时 | {code_name 置空，必须由用户手动填写后方可保存} |
| 3 | 保存前 | {前端先本地校验格式，不合规则提示用户，不发起请求} |

> **安全要求**：`code_name` 会被拼接进 `CREATE TABLE` / 查询语句构造表名，
> 服务端在任何拼接前必须重新校验正则，**不得信任客户端校验结果**。

### 11.3 输入验证规则

> 验证在 handler 层完成，遵循 [technical_overview.md](../../technical_overview.md) §4.4。

| 字段 | 规则 | 失败时的 message |
|------|------|-----------------|
| `{display_name}` | {非空；长度 1–128} | {`{display_name} is required`} |
| `{code_name}` | {正则 `^[a-z][a-z0-9_]*$`；长度 3–64} | {`invalid {code_name} format`} |
| `{some_enum}` | {必须在 §2.5.1 的合法值集合内} | {`invalid {some_enum} value`} |
| `{some_int}` | {范围 {min}–{max}} | {`{some_int} out of range`} |
| `{url_field}` | {合法 URL；scheme 限 http/https} | {`invalid url`} |
| `{conditional_field}` | {当 `{trigger_field}` = {值} 时必填} | {`{conditional_field} is required when ...`} |

### 11.4 日志规范

> 级别定义、输出格式、trace_id 透传遵循
> [technical_overview.md](../../technical_overview.md) §4.5。本节仅列出本组件的具体约定。

| 场景 | 级别 | 必含字段 | 示例 |
|------|------|---------|------|
| {HTTP 请求} | INFO | {method, path, status, duration_ms, trace_id} | {示例} |
| {daemon 轮询开始} | INFO | {exe_name, {code_name}} | {示例} |
| {任务失败} | ERROR | {exe_name, {code_name}, error, retry_count} | {示例} |
| {外部请求重试} | WARN | {url, attempt, error} | {示例} |

**脱敏字段**：{列出必须脱敏的字段，如 `{media_pwd}`、`token`}。

### 11.5 测试策略

| 层 | 测试类型 | 覆盖要求 | 工具 |
|----|---------|---------|------|
| {service} | {单元测试} | {核心业务逻辑分支} | {工具} |
| {repository} | {集成测试} | {依赖真实数据库的查询} | {工具} |
| {handler} | {接口测试} | {参数校验与错误码} | {工具} |
| {前端} | {不做自动化测试 / 说明} | {遵循 uiux_design_specification.md §17} | — |

**测试数据**：{说明来源，如 `sql/sample_data.sql`}。

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {变更说明} |
| {x.y.z} | {YYYY-MM-DD} | {初版} |
