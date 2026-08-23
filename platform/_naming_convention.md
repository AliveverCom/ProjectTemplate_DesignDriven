# 项目命名规范（Naming Convention）

**Document Version**: {x.y.z}

<!--
模板说明（使用时删除本注释块）
- 层级：**平台级命名规范**。放在 `docs/` 根目录，文件名去掉前导下划线 → `naming_convention.md`。
- 本文是**唯一权威**的缩写表和类型前缀表。组件级文档不得自定义缩写或前缀；
  需要新缩写时**先更新本文**，再在组件文档和代码中使用。
- 与其它平台级文档的分工：
  - 本文管**标识符怎么起名**（缩写、C/E/I/cfg 前缀、变量与列名）。
  - `technical_overview.md` §4 管**技术规范**（API 形状、表设计规则）。
  - `project_directory_plan.md` §1.1 管**目录与文件名的大小写风格**（snake_case / kebab-case）。
  三者不重叠，交叉引用即可。
- 注意本文有**适用范围限制**：C/E/I/cfg 前缀只用于技术文档和代码，
  business_desc / business_process / uiux 等业务文档使用自然名称，不加前缀。
-->

> 本文档定义项目在**技术设计（tech_design）和代码实现**中应统一遵循的命名规范。
>
> **适用范围**：`tech_design` 文档、数据库 schema、类图（`class_diagram.mmd`）、
> ER 图（`db_schema.mmd`）、源代码。
>
> **不适用范围**：`business_desc` 文档、`business_process` 图、`uiux` 文档及页面显示文字。
> 在业务文档中，业务对象应使用其**自然名称**（如 `{MediaBase}`、`{Channel}`、`{MediaType}`），
> **不加** C / E / I 等技术前缀。

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Common Abbreviations](#2-common-abbreviations)
  - [2.1 Business Domain](#21-business-domain)
  - [2.2 General Purpose](#22-general-purpose)
  - [2.3 Usage Rules](#23-usage-rules)
- [3 Type Definition Naming Convention](#3-type-definition-naming-convention)
  - [3.1 Class Prefix: C](#31-class-prefix-c)
  - [3.2 Enum Prefix: E](#32-enum-prefix-e)
  - [3.3 Special Interface Prefix: I](#33-special-interface-prefix-i)
  - [3.4 Configuration Property Prefix: cfg](#34-configuration-property-prefix-cfg)
  - [3.5 Summary Table](#35-summary-table)
- [4 Variable & Column Naming Rules](#4-variable--column-naming-rules)
- [5 Change Log](#5-change-log)

---

## 2 Common Abbreviations

> 缩写表适用于**所有文档类型**（含 business 和 tech）。
> 但 §3 的类型定义前缀（C / E / I / cfg）**仅用于技术文档和代码**。

### 2.1 Business Domain

以下缩写用于项目中的业务领域术语，在变量名、列名、文件名等场景中统一使用。

| English Full Name | Abbreviation | 中文 | Examples |
|-------------------|-------------|------|----------|
| {channel} | `{chn}` | {频道} | `{chn_id}`, `{chn_tags}` |
| {program} | `{prog}` | {栏目 / 节目} | `{prog_id}`, `{prog_tags}` |
| {media} | `{media}` | {媒体} | `{media_id}`, `{media_type}`（不缩写） |
| {source} | `{src}` | {信息源 / 来源} | `{src_medias}` |
| {target} | `{tgt}` | {目标} | `{tgt_medias}` |
| {material} | `{mtrl}` | {素材} | `{mtrl_id}`, `{mtrl_status}` |
| {collector} | `{cltr}` | {采集器} | `{cltr_status}` |
| {业务术语} | `{缩写}` | {中文} | `{示例}` |

> 按项目实际业务域补充行。**每新增一个缩写都必须先写进本表**，才允许在代码中使用。

### 2.2 General Purpose

以下为通用（非业务域）缩写。

| English Full Name | Abbreviation | 中文 | Examples |
|-------------------|-------------|------|----------|
| {configuration} | `{cfg}` | {配置} | `{cfg_scraper}` |
| {execution} | `{exe}` | {执行} | `{exe_status}`, `{exe_start}` |
| {record} | `{rec}` | {记录} | `{rec_id}` |
| {instance} | `{ins}` | {实例} | `{ins_id}` |
| {pipeline} | `{pip}` | {流水线} | `{pip_task}` |
| {description} | `{desc}` | {描述} | `{media_desc}` |
| {通用术语} | `{缩写}` | {中文} | `{示例}` |

### 2.3 Usage Rules

| # | 规则 | 说明 |
|---|------|------|
| 1 | **变量名 / 列名** | 使用小写 `snake_case`，缩写也不大写（如 `{chn_id}`、`{prog_tags}`） |
| 2 | **类名 / 枚举名 / 接口名** | 使用 `PascalCase`，缩写首字母大写（如 `{CChannel}`、`{EMediaType}`） |
| 3 | **一致性优先** | 同一概念在整个项目中必须使用**相同的缩写**，不允许混用（如 channel 统一用 `{chn}`，不允许出现 `ch` 或 `chan`） |
| 4 | **可读性优先** | 缩写不在本文列表中时优先使用**全称**，避免自创不直观的缩写。**新缩写需先添加到本文档后再使用** |
| 5 | **布尔型属性** | 以 `is_`、`has_`、`can_`、`need_` 等前缀开头，后面的词**不缩写**（如 `is_active`、`need_login`） |
| 6 | **跨模块一致** | 同一业务对象在不同模块中的字段名必须完全一致；发现分歧时以**权威定义模块**为准，并同步修正其它模块文档 |

---

## 3 Type Definition Naming Convention

> **重要**：本节的 C / E / I / cfg 前缀规则**仅适用于** tech_design 文档、数据库 schema、
> 类图、ER 图和源代码。
> 在 business_desc、business_process、uiux 等非技术文档中，业务对象和枚举使用**自然名称，不加前缀**。

### 3.1 Class Prefix: C

所有类定义的名称以大写字母 **C** 开头，表示 Class。

| Convention | Format | Example |
|-----------|--------|---------|
| 类名 | `C` + PascalCase | `{CMediaBase}`, `{CSourceMedia}`, `{CChannel}` |
| 数据库模型类 | `C` + PascalCase + `Model` | `{CChannelModel}` |
| 服务类 | `C` + PascalCase + `Service` | `{CChannelService}` |
| 仓库类 | `C` + PascalCase + `Repo` | `{CChannelRepo}` |

### 3.2 Enum Prefix: E

所有枚举类型的定义名以大写字母 **E** 开头，表示 Enum。

| Convention | Format | Example |
|-----------|--------|---------|
| 枚举名 | `E` + PascalCase | `{EMediaType}`, `{ETriggerStrategy}`, `{EExeStatus}` |

枚举**值**本身使用全大写 `UPPER_SNAKE_CASE`（如 `{NEWS_FEED}`、`{WHOLE_SITE}`），**不加前缀**。

> **枚举归属规则**：每个枚举有且仅有一个**权威定义模块**，在该模块的 `tech_design.md` §2 中定义。
> 其它模块的类图中以「引用」形式（虚线框 + `<<enumeration - ref: {module}>>`）出现，
> 并在注释中标明权威定义位置。

### 3.3 Special Interface Prefix: I

所有**特殊定制接口**（非默认 CRUD）的名称以大写字母 **I** 开头，表示 Interface。
默认的增删改查接口**不需要** I 前缀。

| Convention | Format | Example |
|-----------|--------|---------|
| 特殊接口名 | `I` + PascalCase | `{IChannelTags}`, `{IBatchImport}` |
| 默认 CRUD | 不加前缀 | `{CreateChannel}`, `{GetMediaBase}`, `{UpdateProgram}`, `{DeleteSourceMedia}` |

> **判断标准**：如果一个接口是对**单一业务对象**的 Create / Read / Update / Delete 操作，
> 则属于默认 CRUD，不加 `I` 前缀。其它所有接口（聚合查询、批量操作、跨对象关联、虚拟属性计算等）均加 `I` 前缀。

### 3.4 Configuration Property Prefix: cfg

类中用于表示**配置**的属性以小写 `cfg` 开头，表示 Configuration。
配置类本身的**内部属性不需要**再加 `cfg` 前缀。

| Convention | Format | Example |
|-----------|--------|---------|
| 类中的配置属性 | `cfg` + PascalCase（snake_case 语境下为 `cfg_xxx`） | `{cfg_retry_policy}`, `{cfg_scraper}` |
| 配置类名 | `C` + `Cfg` + PascalCase | `{CCfgRetryPolicy}`, `{CCfgScraper}` |
| 配置类内部属性 | 正常命名，**不加** cfg 前缀 | `{max_retries}`, `{timeout_ms}`, `{base_url}` |

**示例**：

```
{CSourceMedia}:
    ...
    cfg_scraper: {CCfgScraper}      # 配置属性，以 cfg 开头

{CCfgScraper}:                      # 配置类，以 CCfg 开头
    max_retries: int                # 配置类内部属性，不加 cfg
    timeout_ms: int
    user_agent: string
```

### 3.5 Summary Table

| Type | Prefix | Case | Example |
|------|--------|------|---------|
| Class | `C` | PascalCase | `{CChannel}`, `{CMediaBase}` |
| Enum | `E` | PascalCase | `{EMediaType}`, `{EExeStatus}` |
| Special Interface | `I` | PascalCase | `{IChannelTags}`, `{IBatchImport}` |
| Config Property | `cfg` | snake_case or camelCase | `{cfg_retry_policy}`, `{cfgAuth}` |
| Config Class | `CCfg` | PascalCase | `{CCfgScraper}` |
| CRUD Interface | （无） | PascalCase | `{CreateChannel}`, `{GetMediaBase}` |
| Variable / Column | （无） | snake_case | `{chn_id}`, `{prog_tags}` |
| Enum Value | （无） | UPPER_SNAKE_CASE | `{NEWS_FEED}`, `{WHOLE_SITE}` |

---

## 4 Variable & Column Naming Rules

| Rule | Description | Example |
|------|-------------|---------|
| snake_case | 变量名和数据库列名统一使用小写下划线格式 | `{media_id}`, `{chn_timezone}` |
| 前缀表示归属 | 属性名以所属对象的缩写为前缀（当属性在上下文中可能产生歧义时） | `{chn_id}`, `{prog_id}` |
| 无歧义时省略前缀 | 通用属性无需前缀 | `{display_name}`, `{code_name}`, `{is_active}` |
| 外键命名 | 目标对象缩写 + `_id` | `{chn_id}`（→ Channel）, `{media_id}`（→ MediaBase） |
| 布尔字段 | 以 `is_` / `has_` / `can_` / `need_` 开头 | `{is_active}`, `{need_login}` |
| 时间字段 | 以 `_at` 结尾表示时间点 | `{created_at}`, `{updated_at}`, `{deleted_at}` |
| 列表 / 数组字段 | 使用**复数**形式 | `{programs}`, `{src_medias}` |
| 唯一代码名 | 用作分表名或对外唯一标识的字段统一命名为 `code_name`，格式 `{^[a-z][a-z0-9_]*$}`，**创建后不可修改** | `{code_name}` |
| 显示名 | 面向用户展示的名称统一命名为 `display_name` | `{display_name}` |
| 状态字段 | 枚举型状态统一以 `_status` 结尾 | `{exe_status}`, `{dist_status}` |

> **列名与类成员一一对应**：数据库列名必须与对应类的成员名完全一致（去掉类型前缀后）。
> 若两者不一致，属于跨文档一致性缺陷，应在 `ai_dev_history/01_DocReviewRefine/` 的 review 中提出。

---

## 5 Change Log

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| {x.y.z} | {YYYY-MM-DD} | {Author} | {变更说明} |
