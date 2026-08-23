<!--
模板说明（使用时删除本注释块）

【文档层级】
本文件是 **组件级（Component-Level）业务文档** 模板。

- 存放位置：`docs/components/{component_code_name}/`
- 文件命名：将前导 `_` 替换为组件代码名。
  例如组件 `crawler` → `crawler_business_desc.md`

【与平台级文档的边界区分】
`docs/` 根目录下的 `business_overview.md` 是 **平台级** 业务文档，两者名字相似但层级完全不同：

| 维度 | 平台级 `docs/business_overview.md` | 组件级 `{component}_business_desc.md`（本模板） |
|------|-----------------------------------|--------------------------------------------|
| 描述对象 | 整个平台的业务全景 | 单个产品组件的内部业务 |
| 内容 | 平台定位、全部组件的职责划分、跨组件端到端业务流程、平台级用户角色 | 本组件的业务对象、本组件内部的业务流程、本组件的输入输出 |
| 业务流程图 | 跨组件的端到端流程（`docs/business_process.mmd`） | 仅本组件内部流程（`{component}_business_process.mmd`） |
| 名词解释 | 平台通用术语的**权威定义** | 仅本组件私有术语；平台通用术语只引用不重复定义 |

**硬性规则**：
1. 组件级文档 **不得重复定义** 平台级已定义的术语、角色、跨组件流程。需要时以
   `见 [business_overview.md](../../business_overview.md) §x.y` 的形式引用章节号。
2. 组件级文档 **不写任何技术实现细节**（类、表、API、技术栈）。这些内容属于
   同目录下的 `{component}_tech_design.md`。
3. 本文描述的每个业务对象，都必须能在 `{component}_tech_design.md §2 Class Definitions`
   中找到对应的类；两边字段必须保持一致，不一致即为文档缺陷。

【文档约定】
- 第 1 章必须是 Table of Contents。
- 所有章节使用数字编号（1、1.1、1.1.1）。
- `{花括号}` 内为占位符，使用时替换为实际内容。
- 可选章节在正文中已标注保留/删除条件，不需要的整章删除并重新编号。
-->

# {组件中文名}（{component_code_name}） Business Description

**Document Version**: {x.y.z}

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Component Overview](#2-component-overview)
  - [2.1 Description](#21-description)
  - [2.2 Key Concepts](#22-key-concepts)
  - [2.3 Product Form](#23-product-form)
  - [2.4 Main Inputs and Outputs](#24-main-inputs-and-outputs)
- [3 Business Objects](#3-business-objects)
  - [3.1 {ObjectName1}（{对象中文名1}）](#31-objectname1对象中文名1)
  - [3.2 {ObjectName2}（{对象中文名2}）](#32-objectname2对象中文名2)
  - [3.3 {ObjectName3}（{对象中文名3}）](#33-objectname3对象中文名3)
- [4 Business Process](#4-business-process)
  - [4.1 Process Overview](#41-process-overview)
  - [4.2 Process Description](#42-process-description)
    - [4.2.1 {子流程1名称}](#421-子流程1名称)
    - [4.2.2 {子流程2名称}](#422-子流程2名称)
- [5 Data Migration](#5-data-migration)
  - [5.1 迁移背景](#51-迁移背景)
  - [5.2 迁移概述](#52-迁移概述)
  - [5.3 数据映射关系](#53-数据映射关系)
  - [5.4 迁移工具运行方式](#54-迁移工具运行方式)
  - [5.5 已知局限](#55-已知局限)
- [6 Appendix](#6-appendix)
  - [6.1 Glossary](#61-glossary)
  - [6.2 Enum Definitions](#62-enum-definitions)
  - [6.3 Scoring Standards](#63-scoring-standards)
- [Change Log](#change-log)

---

## 2 Component Overview

### 2.1 Description

{用 2–5 段文字说明本组件是什么、在平台中承担什么职责、为哪些下游组件提供什么产出。
第一段必须能独立回答"这个组件是干什么的"。}

{组件中文名}（{component_code_name}）是本平台的{一句话定位}组件，负责{核心职责}，
为后续的 {下游组件1}（{downstream_1}）和 {下游组件2}（{downstream_2}）提供{产出物}。

本组件的主要功能包括：

- **{功能1}**：{说明}
- **{功能2}**：{说明}
- **{功能3}**：{说明}

> 本组件在平台整体业务链条中的位置，见
> [business_overview.md](../../business_overview.md) §1.2.{n}。

### 2.2 Key Concepts

> **可选章节**：当本组件存在若干需要先讲清楚才能理解后续内容的核心概念（尤其是概念之间
> 存在包含、派生、多对多等关系时）保留本节；概念简单直白的组件删除本节并重新编号。

在阅读本文档其余部分之前，需要先理解以下核心概念及其相互关系：

| 概念 | 英文 / 代码名 | 定义 | 与其他概念的关系 |
|------|--------------|------|----------------|
| {概念1} | `{concept_1}` | {一句话定义} | {如：一个 {概念1} 可包含多个 {概念2}} |
| {概念2} | `{concept_2}` | {一句话定义} | {如：隶属于某个 {概念1}} |
| {概念3} | `{concept_3}` | {一句话定义} | {如：{概念1} 与 {概念3} 为多对多关系} |

{用 1–3 段文字补充说明这些概念之间容易混淆的地方。例如两个名字相近的概念的区别、
某个概念在不同上下文中的不同含义等。}

### 2.3 Product Form

{说明本组件的产品形态。可能的形态包括：独立运行的 daemon 程序、后端 REST API 服务、
前端页面包、命令行工具、公共类库。多形态组件需逐一列出。}

本组件以{形态概述}的形式呈现：

| # | 形态 | 名称 | 说明 |
|---|------|------|------|
| 1 | Daemon 程序 | `{daemon_name}` | {职责说明；运行方式；触发策略} |
| 2 | REST API 服务 | `{service_name}` | {提供哪些资源的接口；集成于 unified_portal} |
| 3 | 前端页面包 | `{package-name}` | {提供哪些页面；作为 unified_portal 的菜单组呈现} |
| 4 | 命令行工具 | `{cli_name}` | {用途；运行时机} |
| 5 | 公共类库 | `{lib_name}` | {被哪些组件引用} |

> 技术实现形态（编程语言、发布形式、部署方式）见
> [{component_code_name}_tech_design.md](./{component_code_name}_tech_design.md) §6。

### 2.4 Main Inputs and Outputs

| Direction | Name | Description |
|-----------|------|-------------|
| Input | {InputName1} | {数据来源；由哪个组件/外部系统提供；数据形态} |
| Input | {InputName2} | {说明} |
| Output | {OutputName1} | {产出内容；供哪个下游组件消费；数据形态} |
| Output | {OutputName2} | {说明} |

---

## 3 Business Objects

> 本章列出的每个业务对象，在 `{component_code_name}_tech_design.md §2 Class Definitions`
> 中都有对应的类定义。本章描述**业务语义**（这个属性在业务上意味着什么），
> 技术文档描述**技术实现**（类型、存储要求、索引）。两边的属性集合必须一致。

### 3.1 {ObjectName1}（{对象中文名1}）

{2–4 句业务描述：这个对象代表什么、由谁创建、生命周期是怎样的、被谁消费。}

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {attr1} | {int} | Y | {业务含义；取值范围或约束} |
| {attr2} | {string} | Y | {业务含义} |
| {attr3} | {enum} | N | {业务含义；枚举值说明见 §6.2} |
| {attr4} | {datetime} | N | {业务含义；为空表示什么} |

**业务规则**：

| # | 规则 | 说明 |
|---|------|------|
| 1 | {规则1} | {如：{attr2} 一旦创建不可修改} |
| 2 | {规则2} | {如：{attr3} 为 {值} 时 {attr4} 必填} |

### 3.2 {ObjectName2}（{对象中文名2}）

> **继承关系写法示例**（对象派生自某个基类对象时使用本写法）

{ObjectName2} 继承自 {CBaseObject}（{基础对象中文名}），
基类的权威定义在 {定义所在模块，如 `common_lib` / `channel_management`}，
见 [{base_module}_business_desc.md](../{base_module}/{base_module}_business_desc.md) §3.{n}。

**继承自 {CBaseObject} 的属性**（此处不重复列出，仅说明本组件对它们的特殊约束）：

| Inherited Attribute | 本组件的特殊约束 |
|--------------------|----------------|
| {base_attr1} | {如：本组件中该字段恒为 {值}} |
| {base_attr2} | {如：无特殊约束} |

**{ObjectName2} 新增属性**：

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {attr1} | {bool} | Y | {业务含义} |
| {attr2} | {string} | N | {业务含义；仅当 {attr1} = true 时必填} |

**业务规则**：

| # | 规则 | 说明 |
|---|------|------|
| 1 | {规则} | {说明} |

### 3.3 {ObjectName3}（{对象中文名3}）

> **分表 / 模板表写法示例**（对象按某个维度动态分表存储时使用本写法）

{ObjectName3} 不是单一集合，而是按 {分表维度，如"信息源"} 分别存储：
每个 {分表维度对象} 对应一组以其 `code_name` 为前缀的记录集合。

| 记录集合 | 命名模板 | 用途 | 创建时机 |
|---------|---------|------|---------|
| {集合1} | `{code_name}_{suffix1}` | {用途说明} | {如：该 {维度对象} 首次执行 {动作} 时自动创建} |
| {集合2} | `{code_name}_{suffix2}` | {用途说明} | {创建时机} |

#### 3.3.1 {ObjectName3Base}（{基类中文名}）

所有分表集合共享同一组基础属性：

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {attr1} | {int} | Y | {业务含义} |
| {attr2} | {enum} | Y | {业务含义；枚举值说明见 §6.2} |
| {attr3} | {datetime} | Y | {业务含义} |

#### 3.3.2 `{code_name}_{suffix1}`

{说明该集合相对基类的差异：新增了哪些属性、哪些属性在此集合中的语义不同、
记录何时写入、何时被清理。}

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| {extra_attr1} | {type} | {Y/N} | {业务含义} |

#### 3.3.3 `{code_name}_{suffix2}`

{同上结构。}

> 按业务对象数量增删 3.x 小节。每个对象都必须在 tech_design §2 中有对应类。

---

## 4 Business Process

### 4.1 Process Overview

![{组件中文名}业务流程图](./{component_code_name}_business_process.png)

> 源文件：[{component_code_name}_business_process.mmd](./{component_code_name}_business_process.mmd)
>
> 绘图规范见 [templates/mermaid/](../../../templates/mermaid/)：
> 白色画布、所有方框按类型归类并使用不同的浅色系背景色、所有文字为黑色。

{用 1–2 段文字概述整体流程：由谁发起、经过哪几个主要阶段、在什么条件下结束或循环。}

本组件包含以下相对独立的业务流程：

| # | 流程 | 触发方式 | 执行角色 | 产出 |
|---|------|---------|---------|------|
| 1 | {子流程1名称} | {定时 / 人工触发 / 事件驱动} | {角色或程序} | {产出物} |
| 2 | {子流程2名称} | {触发方式} | {角色或程序} | {产出物} |

### 4.2 Process Description

#### 4.2.1 {子流程1名称}

| Item | Detail |
|------|--------|
| **Actor** | {执行者：具体的用户角色，或具体的 daemon 程序名} |
| **Action** | {做了什么，一句话概括} |
| **Input Data** | {需要的数据/资源，以及它们来自哪里} |
| **Output/Result** | {产出的数据/制品，以及它们流向哪里} |

**详细步骤**：

| # | 步骤 | 说明 |
|---|------|------|
| 1 | {步骤1} | {做什么；判断条件；异常时如何处理} |
| 2 | {步骤2} | {说明} |
| 3 | {步骤3} | {说明} |

**业务规则与边界条件**：

- {规则1，如：当 {条件} 时跳过本次执行}
- {规则2，如：失败后的重试策略在业务上的表现}
- {规则3，如：与 {其他子流程} 的并发冲突如何处理}

#### 4.2.2 {子流程2名称}

| Item | Detail |
|------|--------|
| **Actor** | {执行者} |
| **Action** | {做了什么} |
| **Input Data** | {输入} |
| **Output/Result** | {产出} |

**详细步骤**：

| # | 步骤 | 说明 |
|---|------|------|
| 1 | {步骤1} | {说明} |

**业务规则与边界条件**：

- {规则}

> 按子流程数量增删 4.2.x 小节。每个子流程都应在 §4.1 的流程图中有对应的分支或子图。

---

## 5 Data Migration

> **可选章节**：仅当本组件需要从老系统 / 老项目迁移历史数据时保留本章。
> 不涉及数据迁移的组件请整章删除，并将后续章节重新编号。

### 5.1 迁移背景

{说明：老系统是什么、为什么要迁移、迁移的数据量级、迁移的时间窗口要求、
迁移期间新老系统是否并行运行。}

| 项目 | 内容 |
|------|------|
| **源系统** | {老系统名称 / 老项目路径} |
| **源存储** | {如：MySQL 5.7，库名 {old_db}} |
| **目标存储** | {如：PostgreSQL 17，库名 {new_db}} |
| **数据量级** | {记录数量级；预估耗时} |
| **并行策略** | {一次性割接 / 双写并行 / 只读迁移} |

### 5.2 迁移概述

{用 1–2 段说明迁移的整体策略：全量还是增量、按什么顺序迁移、如何保证可重入
（重复执行不产生重复数据）、如何校验。}

**迁移范围**：

| # | 源数据集 | 目标数据集 | 迁移方式 | 说明 |
|---|---------|-----------|---------|------|
| 1 | {source_table_1} | {target_table_1} | {全量 / 增量} | {说明} |
| 2 | {source_table_2} | {target_table_2} | {全量 / 增量} | {说明} |

### 5.3 数据映射关系

#### 5.3.1 {source_table_1} → {target_table_1}

| 源字段 | 源类型 | 目标字段 | 目标类型 | 转换规则 |
|--------|-------|---------|---------|---------|
| {src_col1} | {type} | {tgt_col1} | {type} | {直接映射 / 类型转换 / 值映射规则} |
| {src_col2} | {type} | {tgt_col2} | {type} | {如：老系统的 0/1 映射为新系统的枚举字面量} |
| — | — | {tgt_col3} | {type} | {源系统无此字段，迁移时填充的默认值} |
| {src_col4} | {type} | — | — | {源系统有但不迁移的字段，说明原因} |

#### 5.3.2 {source_table_2} → {target_table_2}

{同上结构。}

### 5.4 迁移工具运行方式

迁移工具：`{migration_tool_path}`

**Dry-run（预览将要迁移的内容，不写入数据库）**：

```bash
{migration_tool_command} --mode=dry-run --source={source_dsn} --target={target_dsn}
```

**正式迁移**：

```bash
{migration_tool_command} --mode=migrate --source={source_dsn} --target={target_dsn} [--batch-size={n}]
```

**仅校验（不重新迁移，只比对源与目标的一致性）**：

```bash
{migration_tool_command} --mode=verify --source={source_dsn} --target={target_dsn}
```

**运行参数说明**：

| 参数 | 必填 | 默认值 | 说明 |
|------|------|-------|------|
| `--mode` | Y | — | `dry-run` / `migrate` / `verify` |
| `--source` | Y | — | {源数据库连接串} |
| `--target` | Y | — | {目标数据库连接串} |
| `--batch-size` | N | {1000} | {每批处理的记录数} |

### 5.5 已知局限

| # | 局限 | 影响 | 处理方式 |
|---|------|------|---------|
| 1 | {局限1} | {影响范围} | {人工补录 / 后续版本解决 / 接受} |
| 2 | {局限2} | {影响范围} | {处理方式} |

---

## 6 Appendix

### 6.1 Glossary

> 仅收录**本组件私有**的术语。平台通用术语的权威定义在
> [business_overview.md](../../business_overview.md) §4.1，此处不重复。

| Term | 英文 / 代码名 | Definition |
|------|--------------|-----------|
| {术语1} | `{term_1}` | {定义} |
| {术语2} | `{term_2}` | {定义} |

### 6.2 Enum Definitions

> **权威定义在** [{component_code_name}_tech_design.md](./{component_code_name}_tech_design.md) §2.{n}
> **Enum Definitions**。本节只做**业务解释**：说明每个枚举值在业务上意味着什么、
> 什么情况下会取到这个值。技术层面的类型、存储方式、默认值以 tech_design 为准。
>
> 若某个枚举的权威定义在其他模块，此处必须注明其权威文件路径，例如：
> `{EEnumName}` 的权威定义在 `{other_module}`，见
> [{other_module}_tech_design.md](../{other_module}/{other_module}_tech_design.md) §2.{n}。

#### 6.2.1 {EEnumName1}（{枚举中文名}）

{一句话说明这个枚举用来表达什么。}

| Value | 业务含义 | 使用位置 | 取到该值的条件 |
|-------|---------|---------|--------------|
| `{VALUE_1}` | {含义} | {§3.1 {ObjectName1}.{attr}} | {什么情况下是这个值} |
| `{VALUE_2}` | {含义} | {§3.1 {ObjectName1}.{attr}} | {条件} |
| `{VALUE_3}` | {含义} | {§3.2 {ObjectName2}.{attr}} | {条件} |

**状态流转**（仅状态类枚举需要）：

| 从 | 到 | 触发条件 |
|----|----|---------|
| `{VALUE_1}` | `{VALUE_2}` | {条件} |
| `{VALUE_2}` | `{VALUE_3}` | {条件} |

#### 6.2.2 {EEnumName2}（{枚举中文名}）

{同上结构。}

### 6.3 Scoring Standards

> **可选章节**：仅当本组件存在需要人工或算法打分的字段（如倾向性、可信度、
> 优先级评分等）时保留本节。无打分字段的组件请删除本节。

#### 6.3.1 {score_field_1}（{评分字段中文名}）

| 项目 | 内容 |
|------|------|
| **取值范围** | {如：-10 ~ +10 的整数} |
| **默认值 / 未处理值** | {如：{值}，表示尚未评分} |
| **评分主体** | {人工填写 / 由 {组件} 自动计算 / 从 {上游对象} 继承} |
| **使用场景** | {哪些业务流程会读取这个分数} |

| 取值 | 含义 | 判定标准 |
|------|------|---------|
| {值区间1} | {含义} | {什么样的内容应打这个分} |
| {值区间2} | {含义} | {判定标准} |
| {值区间3} | {含义} | {判定标准} |

#### 6.3.2 {score_field_2}（{评分字段中文名}）

{同上结构。}

> **一致性要求**：本节的"默认值 / 未处理值"必须与 tech_design 中对应字段的
> 默认值定义完全一致。历史上这里是文档冲突的高发区（业务文档写 null/-1，
> 技术文档写继承自上游对象的实际值），务必在每次 review 时交叉核对。

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {变更说明} |
| {x.y.z} | {YYYY-MM-DD} | {初版} |
