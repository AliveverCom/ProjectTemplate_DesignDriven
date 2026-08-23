<!--
模板说明（使用时删除本注释块）

【文档层级】组件级（Component-level）子模块 / daemon / 可执行程序设计文档。

【存放位置】docs/components/{component_code_name}/sub_{sub_module_code_name}/{sub_module_code_name}_design.md
  - 每个子模块一个独立目录，目录名以 `sub_` 前缀开头，例如：
      docs/components/material_collector/sub_unified_feed_cltr/unified_feed_cltr_design.md
      docs/components/material_collector/sub_whole_site_cltr/whole_site_cltr_design.md
  - 文件名把前导 `_` 换成子模块代码名。

【为什么需要这一层】
  - 组件的 `{component}_tech_design.md` §3「Software Program Descriptions」只给每个
    daemon / 可执行程序留一小节做概述（职责 + 适用输入 + 一句话运行方式）。
  - 单个 daemon 的主循环、重试规则、并发模型、配置参数往往有数百行细节，
    全写进主文档会把 tech_design 撑到不可维护（实际项目中曾达 1500+ 行）。
  - 因此把细节拆到本文件，主文档只保留概述 + 指向本文件的链接。

【与其他文档的边界】
  - §1 业务描述：**摘抄**自 `{component}_business_desc.md` 的对应小节，不得自行改写业务语义；
    节首必须注明 `> 本章节摘自 {component}_business_desc.md §x.x`，便于 review 做一致性核对。
  - 类定义、数据表结构、REST 端点的**权威定义**在 `{component}_tech_design.md`，
    本文只写"读哪张表的哪些列、写哪张表"，不重复 DDL 与字段类型。
  - 全局规范（日志级别、命名、注入防护）引用 `technical_overview.md §4.x`，不重复定义。
-->

# {SubModuleName}（{子模块中文名}） Design

**Document Version**: {x.y.z}
**Parent Component**: [{component_code_name}]({component_code_name}_tech_design.md) §3.{n}

---

## 1 业务描述

> 本章节摘自 [{component_code_name}_business_desc.md](../{component_code_name}_business_desc.md) §{x}.{y}。
> 若业务描述发生变更，先改 business_desc，再同步本节；不得只改本节。

### 1.1 {业务流程名称}

| Item | Detail |
|------|--------|
| **Actor** | {执行者，如 "{子模块中文名}（daemon 程序）"} |
| **Action** | {做什么，如 "定时从 {某类型} 信息源获取内容"} |
| **Input Data** | {输入的数据/资源，如 "信息源的 URL 和抓取配置"} |
| **Output/Result** | {产出，如 "{业务对象} 记录 + {任务} 执行记录"} |

{一到三段展开说明。若该业务存在多种实现模式，用无序列表逐一说明模式差异：}

- **{模式一}**（`{mode_a_code_name}`）：{说明该模式的运作方式、适用场景、优缺点}
- **{模式二}**（`{mode_b_code_name}`）：{说明}

{补充说明业务上的关键约定，例如去重规则、幂等规则、数据保留策略：}
{例："写入 {业务对象} 时不做 {字段} 去重：同一资源可能因各种原因被多次抓取，
后续由 {下游组件} 通过时间段筛选来处理。"}

### 1.2 {任务管理流程 / 第二个相关业务流程}

| Item | Detail |
|------|--------|
| **Actor** | {执行者} |
| **Action** | {做什么} |
| **Input Data** | {输入} |
| **Output/Result** | {产出} |

{说明。}

---

## 2 技术设计

### 2.1 程序职责与适用输入

| 项目 | 内容 |
|------|------|
| **程序名** | `{sub_module_code_name}` |
| **可执行文件** | `{binary_name}`（源码：`src/backend/{component_code_name}/cmd/{sub_module_code_name}/`） |
| **程序形态** | {常驻 daemon / 定时任务 / 一次性 CLI 工具} |
| **职责** | {一句话职责} |
| **适用输入** | {处理哪一类输入，如 "`media_type = {ENUM_A}` 且 `trigger_strategy = {ENUM_B}` 的信息源"} |
| **加载条件** | {从数据库加载工作项的过滤条件，如 "`is_active = true` 且 `deleted_at IS NULL`"} |
| **实例数** | {单实例 / 每个工作项一个子进程 / 可水平扩展 N 个} |

> **加载条件必须写死**：daemon 在每轮重新加载工作项列表时，如何处理"新增的已停用/已软删除项"
> 必须显式说明（是跳过、还是主动清理其待执行任务）。历史 review（R3-05）曾因不同 daemon 的
> 此项描述不一致而返工。

### 2.2 运行方式与触发策略

| 项目 | 内容 |
|------|------|
| **启动方式** | {命令行 / systemd / 容器；启动命令示例} |
| **触发策略** | {固定间隔轮询 / cron 表达式 / 事件驱动 / 外部 API 触发} |
| **触发间隔** | {如 "以所有工作项中的最小 `interval_ms` 为轮询间隔"} |
| **工作项列表刷新** | {如 "每 {n} 轮或每 {m} 分钟从数据库重新加载一次，以及时感知配置变更"} |
| **优雅退出** | {收到 SIGTERM 后的行为：完成当前任务后退出 / 立即中断并标记任务状态} |
| **心跳** | {如何对外暴露"我还活着"：写入 {某表} 的 `{字段}` / HTTP 健康检查端点} |

**启动命令示例**：

```bash
# 正常运行
{binary_name} --config {config_path}

# 指定单个工作项调试运行
{binary_name} --config {config_path} --{work_item_flag} {value} --once
```

### 2.3 主循环步骤

| # | 步骤 | 说明 | 失败时行为 |
|---|------|------|-----------|
| 1 | {加载配置} | {说明} | {退出并输出 ERROR 日志} |
| 2 | {连接数据库} | {说明} | {重试 {n} 次后退出} |
| 3 | {加载工作项列表} | {按 §2.1 加载条件从 `{table}` 查询} | {保留上一轮列表，记 WARN} |
| 4 | {挑选到期的工作项} | {比较 `last_polled` 与 `interval_ms`，跳过未到期项} | — |
| 5 | {创建任务记录} | {向 `{table}_plan` 写入一条 `exe_status = PLANNED` 记录} | {跳过本项，记 ERROR} |
| 6 | {执行抓取 / 处理} | {说明核心处理逻辑} | {见 §2.5 失败与重试规则} |
| 7 | {解析并写入结果} | {批量写入 `{table}_mrecord`} | {见 §2.5} |
| 8 | {结转任务记录} | {将 `_plan` 记录移入 `{table}_done` 并置终态 `exe_status`} | {记 ERROR，下轮补偿} |
| 9 | {更新 `last_polled` / 统计字段} | {说明} | {记 WARN} |
| 10 | {清理} | {清理已停用/已删除工作项遗留的 `PLANNED` 任务，置为 `CANCELED`} | {记 WARN} |
| 11 | {休眠至下一轮} | {说明} | — |

> **步骤编号是本文件的核心价值**：开发计划（ai_dev/03_DevPlan）会直接按这些步骤编号拆 Phase，
> review 也按步骤编号定位问题。步骤一经发布，尽量只追加不重排。

### 2.4 数据读写

**读取的表**：

| # | 表 | 读取列 | 过滤条件 | 用途 |
|---|---|-------|---------|------|
| 1 | `{table_a}` | `{col1}`, `{col2}`, `{col3}` | `is_active = true AND deleted_at IS NULL` | {加载工作项} |
| 2 | `{table_b}_done` | `{col}` | `exe_name = '{sub_module_code_name}' ORDER BY {col} DESC LIMIT 1` | {推断上次执行时间} |

**写入的表**：

| # | 表 | 写入方式 | 写入列 | 说明 |
|---|---|---------|-------|------|
| 1 | `{table}_plan` | INSERT | `{cols}` | {创建待执行任务} |
| 2 | `{table}_plan` | UPDATE | `exe_status`, `{cols}` | {更新任务状态} |
| 3 | `{table}_done` | INSERT | `{cols}` | {结转已完成任务} |
| 4 | `{table}_mrecord` | 批量 INSERT | `{cols}` | {写入抓取结果，批大小 {n}} |
| 5 | `{table_a}` | UPDATE | `{col}` | {回写统计字段} |

**动态表处理**：

| 场景 | 处理方式 |
|------|---------|
| 目标分表不存在 | {首次执行时由本程序 `CREATE TABLE IF NOT EXISTS` 动态创建，模板见 tech_design §{n}} |
| 表名来自用户输入 | {必须先用正则 `^[a-z][a-z0-9_]*$` 白名单校验 `code_name`，再拼接表名；见 technical_overview.md §4.4} |

> 表结构与列类型的**权威定义**在 [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) §{n}，
> 本节不重复 DDL。

### 2.5 失败与重试规则

| # | 失败场景 | 重试策略 | 最终失败处理 | 状态标记 |
|---|---------|---------|-------------|---------|
| 1 | {网络超时} | {进程内立即重试 {n} 次，间隔 {退避策略}} | {记 ERROR，跳过本项} | `exe_status = {FAILED}` |
| 2 | {目标返回 4xx} | {不重试} | {记 WARN，跳过} | `exe_status = {FAILED}` |
| 3 | {解析失败} | {不重试} | {记 ERROR，保留原始内容至 {字段}} | `exe_status = {FAILED}` |
| 4 | {数据库写入失败} | {重试 {n} 次} | {记 ERROR，本轮中止} | `exe_status = {ERROR}` |
| 5 | {进程崩溃} | {由 {父进程 / systemd} 自动重启} | {重启后由 §2.3 步骤 10 清理孤儿任务} | — |

> **重试只在进程内进行，不涉及跨轮次的状态重置**（该约定源自 tech_design review TD-14）。
> 若需要跨轮补偿，必须在 §2.3 主循环中显式增加补偿步骤，不能依赖隐式行为。

### 2.6 并发与资源限制

| 项目 | 约定 |
|------|------|
| **并发模型** | {单协程串行 / 每工作项一个协程，最大并发 {n} / 每工作项一个子进程} |
| **最大并发数** | {n}（配置项 `{cfg_key}`） |
| **单任务超时** | {n} 秒（配置项 `{cfg_key}`） |
| **内存上限** | {说明或"无显式限制"} |
| **对外请求速率** | {每个目标站点最小请求间隔 {n} ms，避免被封禁} |
| **数据库连接池** | {最大连接数 {n}，见 technical_overview.md §4.2} |

**子进程管理**（仅"每工作项一个子进程"模式需要）：

| 场景 | 主进程行为 |
|------|-----------|
| 发现新增工作项 | {启动新子进程} |
| 工作项被停用 / 软删除 | {kill 对应子进程} |
| 子进程崩溃 | {检测到退出码非 0 后自动重启，最多 {n} 次} |
| 主进程退出 | {先 kill 全部子进程再退出} |

### 2.7 配置参数

| # | 配置项 | 类型 | 默认值 | 环境变量 | 说明 |
|---|-------|------|-------|---------|------|
| 1 | `{cfg_key_1}` | {int} | {5000} | `{ENV_VAR_1}` | {轮询间隔（毫秒）} |
| 2 | `{cfg_key_2}` | {int} | {3} | `{ENV_VAR_2}` | {失败重试次数} |
| 3 | `{cfg_key_3}` | {int} | {30} | `{ENV_VAR_3}` | {单任务超时（秒）} |
| 4 | `{cfg_key_4}` | {string} | {""} | `{ENV_VAR_4}` | {说明} |
| 5 | `{cfg_key_5}` | {bool} | {false} | `{ENV_VAR_5}` | {说明} |

> 配置项命名遵循 [naming_convention.md](../../../naming_convention.md) §3.4 配置属性前缀规则。
> 数据库连接等公共配置不在此列出，见组件 tech_design 的环境配置章节。

### 2.8 日志与监控

**日志**：

| 级别 | 触发场景 | 必含字段 |
|------|---------|---------|
| INFO | {每轮循环开始/结束、每个工作项处理完成} | `{exe_name}`, `{work_item}`, `{duration_ms}`, `{count}` |
| WARN | {单个工作项跳过、重试成功} | `{exe_name}`, `{work_item}`, `{reason}` |
| ERROR | {单个工作项最终失败、数据库错误} | `{exe_name}`, `{work_item}`, `{error}`, `{stack}` |
| DEBUG | {逐条记录的解析细节} | {字段} |

> 日志格式与 `trace_id` 透传规则遵循 [technical_overview.md §4.5](../../../technical_overview.md)。

**监控**：

| # | 监控项 | 数据来源 | 展示位置 |
|---|-------|---------|---------|
| 1 | 运行状态（RUNNING / STOPPED / UNKNOWN） | {由 `{table}_done` 中 `exe_name = '{sub_module_code_name}'` 的最新记录时间推断，阈值 {n} 分钟} | {组件 uiux P{nn} 监控仪表板} |
| 2 | 最近错误列表 | {`{table}_done` 中 `exe_status = {FAILED}` 的最近 {n} 条} | 同上 |
| 3 | {处理量统计} | {说明} | 同上 |

### 2.9 已知局限

| # | 局限 | 影响 | 计划改进版本 |
|---|------|------|-------------|
| 1 | {如"心跳采用固定 {n} 分钟阈值，无法区分'空闲无任务'与'进程挂死'"} | {误报 STOPPED} | {v0.x / 暂不改进} |
| 2 | {如"跨工作项之间不做冲突检测，同一目标被多个工作项重复抓取时会产生重复记录"} | {下游需去重} | {见 business_desc 去重约定} |
| 3 | {局限} | {影响} | {版本} |

> 已知局限必须显式列出。历史经验（TD-15）表明：未写明的局限会在 review 中被反复提出，
> 写明后即视为工程师已知并接受。

---

## 3 Appendix

### 3.1 术语表

| 术语 | 英文 / 代码名 | 定义 |
|------|--------------|------|
| {术语1} | `{term_1}` | {定义} |

> 与组件级、平台级同名术语必须一致；此处只写本子模块私有的术语。

### 3.2 相关文档

| # | 文档 | 相关章节 | 说明 |
|---|------|---------|------|
| 1 | [{component_code_name}_business_desc.md](../{component_code_name}_business_desc.md) | §{x}.{y} | 本文 §1 的来源 |
| 2 | [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) | §2 类定义 / §3.{n} 程序概述 / §4 数据库定义 | 类与表的权威定义 |
| 3 | [{component_code_name}_version_plan.md](../{component_code_name}_version_plan.md) | §{n} v0.{x} | 本子模块所属版本范围 |
| 4 | [technical_overview.md](../../../technical_overview.md) | §4.2 / §4.4 / §4.5 | 全局数据库、后端、日志规范 |

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {变更说明；若源于 review，标注条目 ID} |
