<!--
模板说明（使用时删除本注释块）

【文档层级】AI 开发过程文档 —— 02_DevPlanAndReport

【本目录收什么】
  开发计划（DevPlan / BE_DevPlan / FE_DevPlan）、开发报告（DevReport）、
  保存现场文档（Snapshot）、AI 之间的交接文档（Handover）、改进计划（ImprovePlan）、
  命名规范化与目录整理计划（RenamePlan / ReorganizePlan）。

【落盘目录】
  平台级：docs/ai_dev_history/02_DevPlanAndReport/
  组件级：docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/

【文件命名 —— 本目录的硬规则】
  所有文档一律以**所在版本号**开头：v{版本号}_{DocType}[_{YYYYMMDD}].md
    v0.3_DevPlan.md        — 该版本的总体开发计划
    v0.3_BE_DevPlan.md     — 后端专项开发计划（本模板）
    v0.3_FE_DevPlan.md     — 前端专项开发计划
    v0.3_DevReport.md      — 该版本的开发报告 / 进度审查
    v0.3_Snapshot.md       — 保存现场
    v0.3_Handover.md       — AI 之间交接
    v0.3_ImprovePlan.md    — 改进计划
  同一版本内同类型有多份时，追加日期后缀：v0.3_BE_DevPlan_20260320.md
  版本号取自组件的 {component}_version_plan.md；
  跨版本的整理类计划（RenamePlan / ReorganizePlan）取**发起时的当前版本号**。

【三份开发计划模板的分工】
  _DevPlan.md = **版本总纲**。面向一次完整的 AI 编码 session，是交给执行者的
      作战简报，偏"开工前必须知道什么"：读什么（带 🔴必读 / 🟠重要 / 🟡参考
      优先级）、红线是什么、当前代码到哪了、Phase 划分、产出文件清单、
      路由速查、技术版本锁定、给执行者的提示。前后端同版本一并完成时用它。
  _BE_DevPlan.md（本文件）= **后端专项计划**。关联设计文档索引（本组件 /
      平台规范 / 公共库 / 上一版本代码 四类）、技术栈与架构约束、
      目标目录结构、数据库脚本规划、按 Phase 的开发任务、API 端点路由表、
      开发注意事项、完成状态 Review。
  _FE_DevPlan.md = **前端专项计划**。背景与动机、关联文档索引、技术方案
      （架构变更 / 新增目录 / API 对接端点清单）、Phase 任务、
      路由注册最终状态、文件变更汇总、验证方法。

【关键设计意图】
  §0 的存在是为了解决"AI 上下文冷启动"问题 —— 新 session 的 AI 不知道该读哪些
  文档，§0 用表格精确指定文件路径 + 必读章节，避免 AI 漏读或读错版本。
  §6 沉淀的是上一版本踩过的坑，防止重复犯错。
  §7 在开发完成后回填，使这份计划同时成为完成度记录。

【权威范围依据】
  开发计划只能实现 {component}_version_plan.md 已定义的版本范围。
  超出范围必须先回去修订 version_plan，不得在计划里自行扩张。

【配套文档】
  计划 → 执行 → v{版本}_DevReport.md 回填完成状态；
  中途换 session / 换模型 → v{版本}_Handover.md 交接；
  中断需保存上下文 → v{版本}_Snapshot.md。

【模板文件名】
  前导 `_` 只是模板标记，复制时按上面的命名规则重命名。
-->

# {ComponentDisplayName} 后端开发计划 — v{目标版本} {版本代号}

**Document**: v{目标版本}_BE_DevPlan.md
**Date**: {YYYY-MM-DD}
**Component**: {component}
**Target**: v{目标版本} — {本版本要达成的目标一句话}
**Developer**: AI ({模型名称})
**Prerequisite**: {前置条件，例如：v0.2 后端 API + 硬编码 mock 数据已完成}
**Status**: {🟡 进行中 / 🟢 全部完成 / ⚪ 待启动}

---

## 0 关联设计文档索引

> **⚠️ 开发前必读**：以下文件包含本组件后端开发所需的全部设计规格。AI 开发者在编码前必须逐一阅读这些文件以获取完整上下文。

### 0.1 本组件设计文档

| # | 文件路径 | 必读章节 | 说明 |
|---|---------|---------|------|
| 1 | `docs/components/{component}/{component}_tech_design.md` | **全文** | 技术设计核心文档：类定义（§2）、程序描述（§3）、数据库定义（§4）、API 定义（§5，共 {n} 个端点）、源码目录布局（§{n}） |
| 2 | `docs/components/{component}/{component}_version_plan.md` | §{n}（v{目标版本} 定义） | v{目标版本} 版本功能范围和技术栈 |
| 3 | `docs/components/{component}/{component}_business_desc.md` | §{n}（业务对象）、§{n}（业务流程） | 业务语义，理解字段含义与流程约束 |
| 4 | `docs/components/{component}/{component}_db_schema.mmd` | 全文 | 数据库 ER 图，建表参照 |
| 5 | `docs/components/{component}/{component}_class_diagram.mmd` | 全文 | 类关系与继承，Model 定义参照 |

### 0.2 平台级规范文档

| # | 文件路径 | 必读章节 | 说明 |
|---|---------|---------|------|
| {n} | `docs/naming_convention.md` | §2（缩写表）、§3（类型前缀）、§4（变量命名） | 项目统一命名规范：C 前缀类、E 前缀枚举、snake_case 列名 |
| {n} | `docs/technical_overview.md` | §{n}（数据库划分）、§{n}（跨模块 API 调用规则）、§{n}（API 设计规范） | 平台技术全局设计 |
| {n} | `project_directory_plan.md` | §{n}（后端目录）、§{n}（API 定义） | 后端代码目录结构和 OpenAPI 契约路径 |

### 0.3 公共库参考文档

| # | 文件路径 | 必读章节 | 说明 |
|---|---------|---------|------|
| {n} | `docs/common_lib/common_lib_tech_design.md` | §{n}（{共享类型1}）、§{n}（{共享类型2}） | 公共库中的跨模块共享类型定义 |
| {n} | `docs/common_lib/common_backend.md` | **全文** | 后端公共代码规范：统一响应、中间件、数据库工具、日志工具 |

### 0.4 v{上一版本} 代码参考（在此基础上改造）

| # | 文件路径 | 参考内容 |
|---|---------|---------|
| {n} | `src/backend/{component}/` | v{上一版本} 完整代码：{cmd/、config/、internal/(handler+service+model+enum+repository)、pkg/、sql/} |
| {n} | `src/backend/{component}/{路径}` | {该文件在本版本中的改造点} |
| {n} | `docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{上一版本}_BE_DevPlan.md` | 上一版本开发计划（了解既有架构和样例数据关联键） |

---

## 1 技术栈与架构约束

| 项目 | 值 |
|------|-----|
| 后端语言 | {Go 1.25.x（go.mod 使用 1.22 兼容）} |
| Web 框架 | {Gin 1.x（v{上一版本} 已有）} |
| 配置管理 | {Viper 1.x（v{上一版本} 已有）} |
| CORS | {gin-contrib/cors（v{上一版本} 已有）} |
| **数据库** | **{PostgreSQL 17.x（v{目标版本} 新增）}** |
| **ORM** | **{GORM 2.x + gorm/driver/postgres（v{目标版本} 新增）}** |
| 迁移工具 | {golang-migrate（v{目标版本} 新增）} |
| 日志 | {方案} |
| 监听端口 | {8081} |
| API 前缀 | `/api/v1/` |

> 本版本新增的技术栈用**加粗**标注，便于快速识别改造范围。
> 版本必须与 `docs/technical_overview.md` §{n} 的锁定版本一致，不得自行升级。

### 1.1 v{目标版本} 架构变更概要

| 层 | v{上一版本} 状态 | v{目标版本} 目标 |
|----|-----------------|-----------------|
| handler | {现状} | {目标} |
| service | {桩实现，返回 mock 数据} | {注入 Repository + 真实业务逻辑} |
| repository | {仅接口定义} | {GORM 实现} |
| model | {纯结构体} | {增加 GORM 标签} |
| 数据源 | {internal/mock/ 硬编码} | {PostgreSQL} |

### 1.2 v{目标版本} 不包含的内容

> 明确写出**不做什么**，防止 AI 超范围实现，导致版本边界模糊。

| # | 不包含项 | 计划版本 |
|---|---------|---------|
| 1 | {daemon 采集程序} | v{n} |
| 2 | {用户认证与权限} | v{n} |
| 3 | {数据迁移工具} | v{n} |
| 4 | {缓存层} | {未规划} |

---

## 2 目标目录结构

v{目标版本} 完成后 `src/backend/{component}/` 的目标形态（**加粗**为本版本新增/改造）：

```
src/backend/{component}/
├── cmd/
│   └── {service_name}/
│       └── main.go                 # 服务入口
├── config/
│   ├── config.go                   # 配置结构体
│   └── config.yaml                 # 默认配置
├── internal/
│   ├── handler/
│   │   ├── router.go               # 路由注册
│   │   └── {resource}_handler.go   # 各资源 handler
│   ├── service/
│   │   └── {resource}_service.go   # 业务逻辑
│   ├── repository/
│   │   ├── {resource}_repo.go      # 接口定义
│   │   └── {resource}_repo_gorm.go # GORM 实现
│   ├── model/
│   │   └── {resource}.go           # 数据模型
│   ├── enum/
│   │   └── {enum_name}.go          # 枚举
│   └── db/
│       └── db.go                   # 数据库连接管理
├── pkg/
│   ├── response.go                 # 统一响应封装
│   └── {util}.go
├── sql/
│   ├── init_databases.sql          # 建库脚本
│   ├── migrations/                 # 迁移脚本
│   └── sample_data.sql             # 样例数据
├── go.mod
└── go.sum
```

{对关键目录的职责补充说明。}

---

## 3 数据库脚本规划

### 3.1 init_databases.sql — 建库脚本

| 项目 | 内容 |
|------|------|
| 路径 | `src/backend/{component}/sql/init_databases.sql` |
| 职责 | {创建数据库、schema、扩展、专用角色} |
| 幂等性 | {使用 IF NOT EXISTS，可重复执行} |
| 执行方式 | `{psql -U postgres -f init_databases.sql}` |

### 3.2 migrations/ — {迁移工具} 迁移脚本

| 序号 | 文件名 | 内容 |
|------|--------|------|
| {000001} | `{000001_create_{table}.up.sql}` / `.down.sql` | {建 {table} 表 + 索引} |
| {000002} | `{000002_xxx.up.sql}` / `.down.sql` | {说明} |

| 约定 | 规则 |
|------|------|
| 命名 | `{序号}_{动作}_{对象}.{up|down}.sql` |
| 可回滚 | 每个 up 必须有对应 down |
| 不可修改 | 已执行过的迁移脚本禁止修改，只能新增 |

### 3.3 sample_data.sql — 样例数据脚本

| 项目 | 内容 |
|------|------|
| 路径 | `src/backend/{component}/sql/sample_data.sql` |
| 记录量 | {每张主表 {n} 条，覆盖各枚举取值与边界情况} |
| 用途 | {本地开发、API 联调、前端对接} |
| 幂等性 | {先 TRUNCATE 再插入 / 使用固定主键 + ON CONFLICT DO NOTHING} |

### 3.4 样例数据关联键对照表

> 前后端联调时需要用固定的 ID 对齐，此表是前端 mock 与后端样例数据的契约。

| {主对象} | {主键值} | {code_name} | 关联的{子对象} | 备注 |
|---------|---------|------------|---------------|------|
| {对象名1} | {1} | `{code_name_1}` | {子对象清单} | {覆盖 {枚举值} 场景} |
| {对象名2} | {2} | `{code_name_2}` | {子对象清单} | {覆盖 {边界} 场景} |

---

## 4 开发任务列表

> 每个 Phase 内的任务可并行；Phase 之间严格串行。
> 「状态」列在开发过程中回填：🟢 已完成 / 🟡 部分完成 / 🔴 未开始。

### Phase 1: {阶段名，如 依赖更新}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 1.1 | {任务描述} | `{文件路径}` | {实现要点、注意事项} | |
| 1.2 | {任务描述} | `{文件路径}` | {要点} | |

### Phase 2: {阶段名，如 配置扩展}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 2.1 | {任务描述} | `{文件路径}` | {要点} | |
| 2.2 | {任务描述} | `{文件路径}` | {要点} | |

### Phase 3: {阶段名，如 数据库连接管理}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 3.1 | {任务描述} | `{文件路径}` | {要点} | |

### Phase 4: {阶段名，如 Model 与 ORM 标签}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 4.1 | {任务描述} | `{文件路径}` | {字段级要点：JSONB 序列化、时间字段、软删除} | |

### Phase 5: {阶段名，如 数据库脚本}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 5.1 | {任务描述} | `{文件路径}` | {要点} | |

### Phase 6: {阶段名，如 Repository 实现}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 6.1 | {任务描述} | `{文件路径}` | {查询构造、分页、排序白名单、动态表名校验} | |

### Phase 7: {阶段名，如 Service 层重构}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 7.1 | {任务描述} | `{文件路径}` | {业务规则、事务边界、级联处理} | |

### Phase 8: {阶段名，如 Handler 层输入验证}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 8.1 | {任务描述} | `{文件路径}` | {验证规则来源：tech_design 附录「输入验证规则」} | |

### Phase 9: {阶段名，如 入口程序更新}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 9.1 | {任务描述} | `{文件路径}` | {依赖装配顺序、优雅退出} | |

### Phase 10: {阶段名，如 单元测试}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 10.1 | {任务描述} | `{文件路径}` | {覆盖层次、mock 方式} | |

---

## 5 API 端点路由表

> 本表是 handler 路由注册的验收清单，端点必须与 `{component}_tech_design.md` §5 完全一致。

| # | Method | Endpoint | Handler | Service | 认证 | tech_design 章节 | 状态 |
|---|--------|----------|---------|---------|------|-----------------|------|
| 1 | GET | `/api/v1/{resource}` | `{Handler}.List` | `{Service}.List` | {是/否} | §5.1.1 | |
| 2 | GET | `/api/v1/{resource}/:{id}` | `{Handler}.Get` | `{Service}.Get` | {是/否} | §5.1.2 | |
| 3 | POST | `/api/v1/{resource}` | `{Handler}.Create` | `{Service}.Create` | {是/否} | §5.1.3 | |
| 4 | PUT | `/api/v1/{resource}/:{id}` | `{Handler}.Update` | `{Service}.Update` | {是/否} | §5.1.4 | |
| 5 | DELETE | `/api/v1/{resource}/:{id}` | `{Handler}.Delete` | `{Service}.Delete` | {是/否} | §5.1.5 | |
| 6 | POST | `/api/v1/{resource}/batch-delete` | `{Handler}.BatchDelete` | `{Service}.BatchDelete` | {是/否} | §5.2.4 | |

**端点总数**：{n} 个。

---

## 6 开发注意事项

### 6.1 v{目标版本} 版本边界

{再次强调本版本做什么、不做什么。引用 §1.2。遇到设计文档中标注为后续版本的功能，一律跳过并在代码中留 TODO 注释标明目标版本。}

### 6.2 动态表安全

{若本组件使用由用户输入拼接的动态表名（如 `{code_name}_mrecord`）：}

- 表名参数在进入 SQL 前**必须**先用白名单正则校验：`^[a-z][a-z0-9_]*$`，长度 {3–64}。
- 校验失败直接返回 {400}，不得进入 repository 层。
- 禁止使用字符串拼接构造 WHERE 条件值，一律参数化。

### 6.3 表不存在的处理

{动态表在 {什么时机} 才被创建。API 访问尚未创建的表时的约定：}

| 场景 | 处理方式 |
|------|---------|
| {单表查询，表不存在} | {返回 404 + 明确 message} |
| {跨表聚合，某表不存在} | {跳过该表，不报错，仅统计已存在的表} |

### 6.4 {ORM} 使用注意事项

| # | 注意事项 |
|---|---------|
| 1 | {JSONB 字段的序列化方式与自定义类型实现} |
| 2 | {软删除：使用 `deleted_at` 而非 ORM 默认的 soft delete 语义，需显式配置} |
| 3 | {`updated_at` 由数据库触发器维护，ORM 侧不得自动写入} |
| 4 | {动态表名通过 `Table()` 指定，不用 AutoMigrate} |
| 5 | {排序字段必须走白名单映射，禁止直接把用户传入的 sort_by 拼进 ORDER BY} |
| 6 | {JSONB 子字段排序需使用表达式：`(jsonb_col->>'field')::int`，并确认已有表达式索引} |

### 6.5 跨数据库查询

{平台约定每组件独立数据库，**禁止跨库 JOIN**。需要他模块数据时，通过 REST API 跨模块调用。
 本组件的跨模块调用清单：}

| 调用方向 | 对方组件 | 端点 | 用途 |
|---------|---------|------|------|
| {本组件调用他人} | {component_x} | `{GET /api/v1/xxx}` | {用途} |
| {他人调用本组件} | {component_y} | `{GET /api/v1/yyy}` | {用途} |

### 6.6 响应格式约定

与 v{上一版本} 一致，所有端点返回统一结构：

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

分页端点的 `data`：

```json
{
  "items": [],
  "total": 0,
  "page": 1,
  "page_size": 50
}
```

### 6.7 日志策略

| 级别 | 使用场景 | 必含字段 |
|------|---------|---------|
| DEBUG | {SQL 语句、请求体} | {trace_id} |
| INFO | {请求进出、关键业务动作} | {trace_id, method, path, status, latency} |
| WARN | {可恢复异常、降级} | {trace_id, reason} |
| ERROR | {未处理异常、依赖失败} | {trace_id, error, stack} |

> 密码、Token 等敏感字段一律脱敏后再记录。

### 6.8 命名规范速查

| 对象 | 规则 | 示例 |
|------|------|------|
| 类 / 结构体 | `C` 前缀 + PascalCase | `{CSourceMedia}` |
| 枚举 | `E` 前缀 + PascalCase | `{EExeStatus}` |
| 接口 | `I` 前缀 + PascalCase | `{IRepository}` |
| 数据库表 | snake_case | `{source_media}` |
| 数据库列 | snake_case | `{created_at}` |
| API 路径 | 复数 kebab-case | `{/api/v1/source-media}` |
| 后端目录 | snake_case | `{material_collector}` |
| 配置属性 | `cfg` 前缀 | `{cfgDbHost}` |

> 完整规则见 `docs/naming_convention.md`。

---

## 7 完成状态 Review（{YYYY-MM-DD}）

> 本章在开发完成后回填，使这份计划同时成为完成度记录。
> 版本级的完整进度审查另出独立文档 `v{目标版本}_DevReport.md`（同目录 02_DevPlanAndReport）；
> 本章只回填本计划内各 Phase 任务的完成情况。

### 7.1 总览

| Phase | 任务数 | 🟢 完成 | 🟡 部分 | 🔴 未开始 | 备注 |
|-------|-------|--------|--------|----------|------|
| Phase 1 {阶段名} | {n} | {n} | {n} | {n} | |
| Phase 2 {阶段名} | {n} | {n} | {n} | {n} | |
| Phase 3 {阶段名} | {n} | {n} | {n} | {n} | |
| **合计** | **{N}** | **{N}** | **{N}** | **{N}** | |

### 7.2 🟡 partial 项说明

| # | 任务 | 已完成部分 | 未完成部分 | 原因 | 后续计划 |
|---|------|-----------|-----------|------|---------|
| {n.n} | {任务} | {已做} | {未做} | {原因} | {v{n} 补齐 / 转入遗留问题} |

### 7.3 与设计文档的偏差

| # | 设计文档要求 | 实际实现 | 偏差原因 | 是否需回写文档 |
|---|-------------|---------|---------|---------------|
| 1 | {tech_design §x.y 要求} | {实际做法} | {原因} | {是/否} |
