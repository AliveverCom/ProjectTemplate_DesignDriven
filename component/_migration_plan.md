<!--
模板说明（使用时删除本注释块）

【文档层级】组件级（Component-level）老系统数据迁移计划。

【存放位置】docs/components/{component_code_name}/old_projects_migration/{old_system_code_name}_migration_plan.md
  - 每个组件把所有"老项目迁入"相关文档集中放在 `old_projects_migration/` 目录下，
    与该组件的常规设计文档分开，例如：
      docs/components/material_collector/old_projects_migration/data_migration_plan.md
      docs/components/material_collector/old_projects_migration/main_DailyNewsRss_tech_design.md
  - 文件名把前导 `_` 换成老系统代码名（或直接用 `data_migration_plan.md`）。
  - 若老系统本身也需要留档技术设计，另建 `{old_system}_tech_design.md` 放在同目录。

【与其他文档的边界】
  - 目标端的表结构、列类型、约束的**权威定义**在 `{component}_tech_design.md` §数据库定义，
    本文只写"源列 → 目标列"的映射与转换规则，不重复 DDL。
  - 迁移属于哪个版本、是否阻塞其他功能，由 `{component}_version_plan.md` 定义，本文只引用。
  - 迁移过程中发现的业务语义差异（例如老系统的某字段语义与新业务对象不一致），
    必须回写到 `{component}_business_desc.md` 的「Data Migration」章节，本文只做技术说明。

【填写要点】
  - §4 数据映射关系是本文的核心，必须逐表逐列写全，"转换规则"列不得留空或写"直接映射"以外的模糊描述。
  - §5 必须给出 dry-run / 正式 / 仅校验 三种运行模式的完整命令，工程师照抄即可执行。
  - §9 已知局限要显式写明"哪些数据迁不过来、为什么、怎么补"。
-->

# {OldSystemName} → {NewComponentName} 数据迁移计划

| 项目 | 内容 |
|------|------|
| **文档名称** | {老系统名} 到 {新组件中文名}（{component_code_name}）数据迁移计划 |
| **文档版本** | {x.y.z} |
| **创建日期** | {YYYY-MM-DD} |
| **目标版本** | `v0.{n}`，见 [{component_code_name}_version_plan.md](../{component_code_name}_version_plan.md) §{n} |
| **源系统** | {老系统名}（{源库类型}，代码路径 `{old_source_path}`） |
| **目标系统** | {component_code_name}（{目标库类型}，库名 `{target_db_name}`） |
| **迁移方式** | {一次性全量迁移 / 全量 + 增量补迁 / 分批迁移} |
| **执行者** | {AI / 工程师} |
| **状态** | {🟡 进行中 / 🟢 已完成 / ⚪ 未开始} |

---

## 1 迁移背景与目标

### 1.1 背景

{说明老系统的由来、当前运行状态、为什么要迁移。例如："{老系统名} 是本平台上线前使用的
{语言} 单体程序，已积累 {n} 万条历史 {业务对象} 数据。新平台 {component_code_name} 组件
承接了该系统的全部业务职责，需要把历史数据迁入新库，以保证 {下游组件} 的数据连续性。"}

### 1.2 目标

| # | 目标 | 验收标准 |
|---|------|---------|
| 1 | {迁移 {源表} 的全部历史数据} | {目标表行数 = 源表有效行数，误差为 0} |
| 2 | {字段语义正确转换} | {抽样 {n} 条逐字段比对通过} |
| 3 | {迁移过程可重复执行} | {重复执行不产生重复数据（幂等）} |
| 4 | {老系统可停机下线} | {迁移完成并校验通过后，老系统不再写入新数据} |

### 1.3 非目标（本次不做）

| # | 不做 | 原因 |
|---|------|------|
| 1 | {迁移 {某表}} | {该表数据已失效 / 新业务不再使用} |
| 2 | {双写与实时同步} | {老系统迁移后即停机，无需双写} |
| 3 | {历史数据的语义清洗与补全} | {归属 {下游组件} 的预处理流程} |

---

## 2 源系统概述

### 2.1 源系统基本信息

| 项目 | 内容 |
|------|------|
| **系统名** | {老系统名} |
| **实现语言** | {Python / C# / PHP …} |
| **源码位置** | `{old_source_path}` |
| **数据库类型** | {MySQL 5.7 / SQLite / MongoDB …} |
| **数据库名** | `{old_db_name}` |
| **字符集 / 排序规则** | {utf8mb4 / utf8mb4_general_ci} |
| **数据量级** | {约 n 万行，m GB} |
| **数据时间跨度** | {YYYY-MM ~ YYYY-MM} |
| **访问方式** | {连接串来源、是否只读账号} |

### 2.2 源表清单

| # | 源表 | 行数（约） | 是否迁移 | 目标表 | 说明 |
|---|-----|-----------|---------|-------|------|
| 1 | `{old_table_1}` | {100,000} | ✅ 是 | `{new_table_1}` | {说明} |
| 2 | `{old_table_2}` | {5,000} | ✅ 是 | `{new_table_2}` | {说明} |
| 3 | `{old_table_3}` | {2,000,000} | ✅ 是 | `{new_table_3_template}`（按 `{key}` 分表） | {一对多分表，见 §4.3} |
| 4 | `{old_table_4}` | {300} | ❌ 否 | — | {已失效，不迁移} |
| 5 | `{old_table_5}` | {50} | ❌ 否 | — | {新系统用配置文件替代} |

### 2.3 源系统技术设计留档

{若老系统缺少文档，需先补一份精简的技术设计留档，便于后续排查历史数据问题：}

> 详见 [{old_system_code_name}_tech_design.md](./{old_system_code_name}_tech_design.md)。

---

## 3 目标系统概述

### 3.1 目标库基本信息

| 项目 | 内容 |
|------|------|
| **组件** | {component_code_name} |
| **数据库类型** | {PostgreSQL 17.x} |
| **数据库名** | `{target_db_name}` |
| **Schema** | `{schema_name}` |
| **建库脚本** | `src/backend/{component_code_name}/sql/init_databases.sql` |
| **迁移脚本目录** | `src/backend/{component_code_name}/sql/migrations/` |

### 3.2 目标表清单

| # | 目标表 | 是否已存在 | 创建方式 | 说明 |
|---|-------|-----------|---------|------|
| 1 | `{new_table_1}` | ✅ v0.3 已建 | 迁移脚本 | {说明} |
| 2 | `{new_table_2}` | ✅ v0.3 已建 | 迁移脚本 | {说明} |
| 3 | `{new_table_3_template}` | ❌ 动态创建 | {由迁移工具按 `{key}` 逐个 `CREATE TABLE IF NOT EXISTS`} | {分表模板见 tech_design §{n}} |

> 表结构的**权威定义**在 [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) §{n}，
> 本文不重复 DDL。

### 3.3 迁移前置条件

| # | 前置条件 | 检查方式 | 状态 |
|---|---------|---------|------|
| 1 | 目标库已建库建表（v0.3 完成） | {执行 `\dt` 确认表存在} | {🟢 / ⚪} |
| 2 | 源库只读账号可连通 | {执行 `SELECT 1`} | {🟢 / ⚪} |
| 3 | 目标库有足够磁盘空间（≥ {n} GB） | {`df -h`} | {🟢 / ⚪} |
| 4 | 已备份目标库当前数据 | {`pg_dump` 产物路径} | {🟢 / ⚪} |
| 5 | 老系统已停止写入 | {确认 daemon 已停} | {🟢 / ⚪} |

---

## 4 数据映射关系

> **本章是迁移工具实现的唯一依据**。每一列都必须有明确的转换规则，
> 不允许出现"待定"或空白；确实无对应关系的写"不迁移，目标列使用默认值 {x}"。

### 4.1 `{old_table_1}` → `{new_table_1}`

| # | 源列 | 源类型 | 目标列 | 目标类型 | 转换规则 | 备注 |
|---|-----|-------|-------|---------|---------|------|
| 1 | `{old_col_id}` | `INT` | `{new_col_id}` | `INT PK` | 直接映射 | {是否保留原 ID：保留可保持外键关系；不保留则需建 ID 映射表} |
| 2 | `{old_col_name}` | `VARCHAR(255)` | `{new_col_display_name}` | `TEXT NOT NULL UNIQUE` | `TRIM()` 后直接映射；为空时用 `'{fallback}'` | {源数据存在 {n} 条空值} |
| 3 | — | — | `{new_col_code_name}` | `TEXT NOT NULL UNIQUE` | **源无对应列**：由 `{new_col_display_name}` 生成 snake_case（转小写、标点与空格转 `_`）；冲突时追加 `_{序号}` | {必须满足 `^[a-z][a-z0-9_]*$`，因其用于构造分表名} |
| 4 | `{old_col_type}` | `TINYINT` | `{new_col_media_type}` | `TEXT` | 枚举值映射，见 §4.4 | {源用数字，目标用枚举字面量} |
| 5 | `{old_col_flag}` | `TINYINT(1)` | `{new_col_is_active}` | `BOOLEAN NOT NULL DEFAULT true` | `0 → false`，`1 → true`，`NULL → true` | |
| 6 | `{old_col_deleted}` | `TINYINT(1)` | `deleted_at` | `TIMESTAMPTZ NULL` | `0 → NULL`；`1 → {old_col_updated}` 的值，若为空则用迁移执行时间 | **软删除字段语义变更**：老系统 `is_deleted` 布尔 → 新系统 `deleted_at` 时间戳（见 technical_overview.md §4.2 规则 6） |
| 7 | `{old_col_a}` / `{old_col_b}` / `{old_col_c}` | `INT` ×3 | `{new_col_json}` | `JSONB NOT NULL` | 三列合并为 JSON 对象：`{"a": {old_col_a}, "b": {old_col_b}, "c": {old_col_c}}`；源列为 `NULL` 时用默认值 `{n}` | {结构化字段合并} |
| 8 | `{old_col_time}` | `DATETIME` | `created_at` | `TIMESTAMPTZ` | 按源库时区 `{TZ}` 解释后转 UTC | **时区必须显式指定**，否则会整体偏移 |
| 9 | — | — | `updated_at` | `TIMESTAMPTZ` | 迁移时统一置为 `created_at` 的值 | |
| 10 | `{old_col_unused}` | `TEXT` | — | — | **不迁移**：{原因} | |

### 4.2 `{old_table_2}` → `{new_table_2}`

| # | 源列 | 源类型 | 目标列 | 目标类型 | 转换规则 | 备注 |
|---|-----|-------|-------|---------|---------|------|
| 1 | `{old_col}` | `{type}` | `{new_col}` | `{type}` | {规则} | {备注} |

### 4.3 `{old_table_3}` → `{new_table_3_template}`（分表）

源表为单张大表，目标端按 `{key_field}` 拆分为多张分表 `{key_value}_{suffix}`。

| # | 源列 | 源类型 | 目标列 | 目标类型 | 转换规则 | 备注 |
|---|-----|-------|-------|---------|---------|------|
| 1 | `{old_col_fk}` | `INT` | — | — | **不写入列**，用于决定目标分表名：先查 §4.1 生成的 `{new_col_code_name}`，拼接为 `{code_name}_{suffix}` | 分表键 |
| 2 | `{old_col}` | `{type}` | `{new_col}` | `{type}` | {规则} | |

**分表处理规则**：

| # | 场景 | 处理方式 |
|---|------|---------|
| 1 | 目标分表不存在 | `CREATE TABLE IF NOT EXISTS` 按模板创建（含索引） |
| 2 | 分表名不合法 | 跳过该批数据并记入错误报告；`code_name` 必须匹配 `^[a-z][a-z0-9_]*$` |
| 3 | 源行的外键在 §4.1 中未迁移 | 跳过并计入"孤儿行"统计 |

### 4.4 枚举值映射表

| 源值 | 源含义 | 目标枚举值 | 说明 |
|------|-------|-----------|------|
| `{0}` | {含义} | `{ENUM_A}` | |
| `{1}` | {含义} | `{ENUM_B}` | |
| `{2}` | {含义} | `{ENUM_C}` | |
| 其他 / NULL | {未知} | `{ENUM_DEFAULT}` | 计入警告统计 |

> 目标枚举的**权威定义**在 [{component_code_name}_tech_design.md](../{component_code_name}_tech_design.md) §{n} Enum Definitions。

### 4.5 关联键对照表

{迁移涉及跨表引用时，说明 ID 如何对应：}

| 源表 | 源键 | 目标表 | 目标键 | 对应策略 |
|------|-----|-------|-------|---------|
| `{old_table_1}` | `{old_col_id}` | `{new_table_1}` | `{new_col_id}` | {保留原 ID / 新建自增 ID 并维护内存映射表 `map[old_id]new_id`} |

---

## 5 迁移工具设计

### 5.1 工具基本信息

| 项目 | 内容 |
|------|------|
| **工具名** | `{migration_tool_name}` |
| **实现语言** | {Go 1.25.x / Python 3.12} |
| **源码目录** | `src/backend/{component_code_name}/cmd/{migration_tool_name}/`（或 `{old_projects_migration_src_path}`） |
| **依赖** | {源库驱动、目标库驱动、配置库；版本见 technical_overview.md §5} |
| **产物** | {可执行文件 `{binary}` / 脚本入口 `main.py`} |

### 5.2 目录结构

```
{migration_tool_path}/
├── cmd/{migration_tool_name}/main.go   # 入口，解析参数与模式
├── internal/
│   ├── source/          # 源库读取（只读）
│   ├── transform/       # §4 映射与转换规则实现
│   ├── sink/            # 目标库写入（含分表动态创建）
│   ├── verify/          # §7 校验逻辑
│   └── report/          # 迁移报告与错误明细输出
├── config/
│   └── {config_file}    # 源库/目标库连接串、批大小、并发度
└── README.md
```

### 5.3 运行模式

**模式一：Dry-run（预览，不写入目标库）**

```bash
{migration_tool_name} \
  --config {config_path} \
  --mode dry-run \
  --tables {old_table_1},{old_table_2} \
  --report {report_dir}/dryrun_{YYYYMMDD}.md
```

- 完整执行读取与转换，但**不执行任何写入**。
- 输出：将要迁移的行数、将要创建的分表清单、转换告警（枚举未知值、空值兜底、孤儿行）。

**模式二：正式迁移**

```bash
{migration_tool_name} \
  --config {config_path} \
  --mode migrate \
  --tables {old_table_1},{old_table_2},{old_table_3} \
  --batch-size {1000} \
  --report {report_dir}/migrate_{YYYYMMDD}.md
```

- 按 §6 执行步骤顺序迁移；每批 `{1000}` 行提交一次事务。
- 幂等保证：{使用 `INSERT ... ON CONFLICT DO NOTHING` / 迁移前按 `{key}` 清空目标表}。

**模式三：仅校验（不重新迁移）**

```bash
{migration_tool_name} \
  --config {config_path} \
  --mode verify \
  --sample-size {200} \
  --report {report_dir}/verify_{YYYYMMDD}.md
```

- 只执行 §7 的行数比对与抽样比对，不做任何写入。

### 5.4 通用参数

| 参数 | 类型 | 默认值 | 说明 |
|------|------|-------|------|
| `--config` | string | — | 配置文件路径（必填） |
| `--mode` | enum | `dry-run` | `dry-run` / `migrate` / `verify` |
| `--tables` | string | 全部 | 逗号分隔的源表白名单 |
| `--batch-size` | int | {1000} | 每批提交行数 |
| `--sample-size` | int | {200} | 抽样校验条数 |
| `--continue-on-error` | bool | `false` | 单行失败时是否继续 |
| `--report` | string | stdout | 报告输出路径 |

---

## 6 执行步骤

| # | 步骤 | 命令 / 说明 | 负责人 | 状态 |
|---|------|-----------|-------|------|
| 1 | 备份目标库 | `pg_dump {target_db_name} > {backup_path}` | {执行者} | {⚪} |
| 2 | 确认源库只读连通 | {命令} | {执行者} | {⚪} |
| 3 | 停止老系统写入 | {命令 / 操作说明} | {执行者} | {⚪} |
| 4 | 执行 dry-run | 见 §5.3 模式一 | {执行者} | {⚪} |
| 5 | 审阅 dry-run 报告 | {重点看：行数、分表清单、转换告警数} | {工程师} | {⚪} |
| 6 | 迁移 `{old_table_1}` → `{new_table_1}` | 见 §5.3 模式二，`--tables {old_table_1}` | {执行者} | {⚪} |
| 7 | 迁移 `{old_table_2}` → `{new_table_2}` | 同上 | {执行者} | {⚪} |
| 8 | 迁移 `{old_table_3}` → 分表 | 同上（依赖步骤 6 生成的 `code_name`） | {执行者} | {⚪} |
| 9 | 执行校验 | 见 §5.3 模式三 | {执行者} | {⚪} |
| 10 | 审阅校验报告 | {全部比对项通过方可进入下一步} | {工程师} | {⚪} |
| 11 | 更新组件 `VERSION` 与 Change Log | {说明} | {执行者} | {⚪} |
| 12 | 老系统下线 | {说明} | {工程师} | {⚪} |

> **状态色标**：🟢 已完成 | 🟡 进行中 | 🔴 失败 | ⚪ 未开始
>
> 步骤 6/7/8 存在**顺序依赖**（分表名依赖主表生成的 `code_name`），不得并行或调换顺序。

---

## 7 校验方案

### 7.1 行数比对

| # | 比对项 | 源侧查询 | 目标侧查询 | 允许误差 |
|---|-------|---------|-----------|---------|
| 1 | `{old_table_1}` 总行数 | `SELECT COUNT(*) FROM {old_table_1} WHERE {有效条件}` | `SELECT COUNT(*) FROM {new_table_1}` | 0 |
| 2 | `{old_table_2}` 总行数 | {查询} | {查询} | 0 |
| 3 | `{old_table_3}` 总行数 | `SELECT COUNT(*) FROM {old_table_3}` | 所有分表 `COUNT(*)` 之和 | {0 / 孤儿行数} |
| 4 | 分表数量 | `SELECT COUNT(DISTINCT {old_col_fk}) FROM {old_table_3}` | 实际创建的分表数 | 0 |

### 7.2 抽样比对

| # | 抽样策略 | 样本量 | 比对内容 |
|---|---------|-------|---------|
| 1 | 随机抽样 | {200} 条 | 逐列比对 §4 映射后的期望值 |
| 2 | 边界抽样：最早 / 最新记录 | 各 {10} 条 | 重点验证时间字段的时区转换 |
| 3 | 特殊值抽样：源为 NULL / 空串 / 枚举未知值 | 各 {20} 条 | 验证兜底规则 |
| 4 | 长文本 / 特殊字符记录 | {20} 条 | 验证字符集与转义 |

### 7.3 唯一键冲突处理

| # | 冲突场景 | 处理方式 |
|---|---------|---------|
| 1 | `{new_col_display_name}` 重复 | {追加 `_{序号}` 去重，并记入报告} |
| 2 | `{new_col_code_name}` 生成后重复 | {追加 `_{序号}`；因其决定分表名，必须保证唯一} |
| 3 | 主键重复（重复执行迁移） | {`ON CONFLICT DO NOTHING`，保证幂等} |

### 7.4 校验报告格式

```
{report_dir}/verify_{YYYYMMDD}.md
├── 1 行数比对结果表（源 / 目标 / 差异 / 结论 ✅❌）
├── 2 抽样比对结果表（样本 ID / 不一致列 / 源值 / 目标值）
├── 3 转换告警汇总（枚举未知值 n 条、空值兜底 n 条、孤儿行 n 条）
└── 4 结论（✅ 通过 / ❌ 不通过 + 阻塞项清单）
```

---

## 8 回滚方案

| # | 回滚场景 | 回滚操作 | 数据损失 |
|---|---------|---------|---------|
| 1 | dry-run 阶段发现问题 | 无需回滚（未写入） | 无 |
| 2 | 迁移中途失败 | {按 `{key}` 删除本次已写入的行；或整库恢复步骤 1 的备份} | 无 |
| 3 | 迁移完成后发现映射错误 | {恢复步骤 1 的备份 → 修正 §4 映射 → 重新执行全流程} | 迁移后新产生的业务数据（因此迁移期间必须停机） |
| 4 | 分表创建错误 | {`DROP TABLE` 错误分表 → 修正 `code_name` 生成规则 → 重跑步骤 8} | 无 |

**回滚命令示例**：

```bash
# 整库恢复
psql -d {target_db_name} -f {backup_path}

# 仅回滚本次迁移写入的行
psql -d {target_db_name} -c "DELETE FROM {new_table_1} WHERE {migration_marker_condition};"
```

> **建议**：迁移工具为每行写入 `{migration_marker_column}`（如 `created_at = {迁移批次时间}` 或专用标记列），
> 使"仅回滚本次迁移"成为可能，避免只能整库恢复。

---

## 9 已知局限与遗留问题

| # | 局限 / 遗留问题 | 影响 | 处理方式 | 状态 |
|---|---------------|------|---------|------|
| 1 | {如"源表 `{old_table_3}` 的 `{col}` 列历史数据缺失约 {n}%，无法补全"} | {目标列使用默认值，下游统计口径受影响} | {在 business_desc 的 Data Migration 章节说明；由 {下游组件} 按空值处理} | {已接受} |
| 2 | {如"源库时区信息缺失，早于 {YYYY} 年的数据时间可能有 ±{n} 小时偏差"} | {影响按日期筛选的精度} | {已接受，不做修正} | {已接受} |
| 3 | {如"`{old_table_x}` 的迁移尚未实现"} | {该部分数据暂缺} | {计划在 v0.{n} 补齐} | {🔴 未完成} |
| 4 | {局限} | {影响} | {处理} | {状态} |

> 已知局限必须显式列出并由工程师确认。未写明的局限会在 ai_dev/01_DocReviewRefine 的
> 后续 review 中被反复提出。
>
> 若某项局限导致实际迁移范围与 [{component_code_name}_version_plan.md](../{component_code_name}_version_plan.md)
> 的版本定义产生偏差，必须同步在 version_plan 的 Change Log 中记录偏差。

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {变更说明} |
