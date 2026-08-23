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
    v0.3_DevPlan.md        — 该版本的总体开发计划（本模板）
    v0.3_BE_DevPlan.md     — 后端专项开发计划
    v0.3_FE_DevPlan.md     — 前端专项开发计划
    v0.3_DevReport.md      — 该版本的开发报告 / 进度审查
    v0.3_Snapshot.md       — 保存现场
    v0.3_Handover.md       — AI 之间交接
    v0.3_ImprovePlan.md    — 改进计划
  同一版本内同类型有多份时，追加日期后缀：v0.3_DevPlan_20260320.md
  版本号取自组件的 {component}_version_plan.md；
  跨版本的整理类计划（RenamePlan / ReorganizePlan）取**发起时的当前版本号**。

【三份开发计划模板的分工】
  _DevPlan.md（本文件）= **版本总纲**。面向一次完整的 AI 编码 session，
      是交给执行者的作战简报，偏"开工前必须知道什么"：读什么（带 🔴必读 /
      🟠重要 / 🟡参考 优先级）、红线是什么、当前代码到哪了、Phase 划分、
      产出文件清单、路由速查、技术版本锁定、给执行者的提示。
      前后端都在同一个小版本里完成时用它。
  _BE_DevPlan.md = 后端专项计划。关联设计文档索引（本组件 / 平台规范 /
      公共库 / 上一版本代码 四类）、技术栈与架构约束、目标目录结构、
      数据库脚本规划、按 Phase 的开发任务、API 端点路由表、开发注意事项、
      完成状态 Review。
  _FE_DevPlan.md = 前端专项计划。背景与动机、关联文档索引、技术方案
      （架构变更 / 新增目录 / API 对接端点清单）、Phase 任务、
      路由注册最终状态、文件变更汇总、验证方法。

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

# {ComponentDisplayName} v{目标版本} 开发计划（版本总纲）

**Document**: v{目标版本}_DevPlan.md
**Created**: {YYYY-MM-DD}
**Target Version**: v{目标版本}（{版本代号，如"纯前端，硬编码数据"}）
**Executor**: {AI 模型名称}
**Status**: {⚪ 待执行 / 🟡 进行中 / 🟢 已完成}

---

## 1 开发前须知

### 1.1 必须先阅读的文档（按顺序）

| # | 文档路径 | 阅读重点 | 优先级 |
|---|---------|---------|--------|
| 1 | `docs/components/{component}/{component}_version_plan.md` | **§{n} v{目标版本} 全部内容**：功能范围、技术栈、不包含的内容。这是 v{目标版本} 的权威范围定义。 | 🔴 必读 |
| 2 | `docs/components/{component}/{component}_tech_design.md` | **§2 Class Definitions**（{前端 TypeScript 类型定义的依据，注意每个类的全部成员和 §{n} 枚举}）、**§{n} Source Code Directory Layout**（{目录树}）。注意各章节的 ⚠️ 版本标注。 | 🔴 必读 |
| 3 | `docs/components/{component}/{component}_uiux.md` | **§2 Page Overview**（{n} 个页面总览）、**§3 Page Details**（{全局样式规则 + 逐页详细 UI 设计}）、**§4 路由定义**（{n} 条路由）。注意各处版本标注。 | 🔴 必读 |
| 4 | `docs/uiux_design_specification.md` | **§{n} 配色**、**§{n} 技术栈版本**、**§{n} 状态管理**、**§{n} 响应式**、**§{n} 性能优化**、**§{n} 测试策略**、**§{n} 文件命名**。 | 🟠 重要 |
| 5 | `docs/naming_convention.md` | {前端/后端代码命名规范。类名前缀（C/E/I）、变量命名规则。} | 🟡 参考 |
| 6 | `docs/technical_overview.md` | {§{n} API 设计规范、§{n} 前端开发规范} | 🟡 参考 |
| 7 | `docs/components/{component}/{component}_business_desc.md` | {业务语义，理解字段含义} | 🟡 参考 |

> 优先级：🔴 必读（不读无法开工）| 🟠 重要（影响实现方式）| 🟡 参考（遇到问题时查）

### 1.2 HTML 设计稿（Mock 数据来源）

v{目标版本} 的所有页面硬编码数据**直接来自**以下 HTML 文件中的示例数据。开发前必须逐个打开阅读，提取其中的表格数据、表单值、下拉选项等。

| Page | 页面名 | HTML 路径 |
|------|--------|----------|
| {P01} | {页面中文名} | `docs/components/{component}/ui_page_design/P01  {页面中文名}/code.html` |
| {P02} | {页面中文名} | `docs/components/{component}/ui_page_design/P02  {页面中文名}/code.html` |
| {P03} | {页面中文名} | `docs/components/{component}/ui_page_design/P03  {页面中文名}/code.html` |

> 同目录下的 `screen.png` 是渲染截图，用于核对布局与配色。
> **数据一致性要求**：同一业务对象在不同页面出现时，硬编码数据必须一致（同一 ID 对应同一条记录），否则页面跳转会出现数据错位。

### 1.3 关联参考文档

| # | 文档路径 | 参考内容 |
|---|---------|---------|
| 1 | `src/frontend/packages/{已完成的参考组件}/` | {已完成组件的完整实现，作为结构与代码风格的参照} |
| 2 | `docs/components/{component}/ai_dev_history/01_DocReviewRefine/{最近的 review}.md` | {设计文档审查结论中已确认的决策} |
| 3 | `project_directory_plan.md` §{n} | {目录结构约定} |
| 4 | `docs/common_lib/common_frontend.md` | {公共前端组件与工具} |

### 1.4 核心约束（v{目标版本} 红线）

> 以下是本版本**绝对不能越界**的约束。越界即为返工。

| # | 红线 | 说明 |
|---|------|------|
| 1 | **{不接入任何后端 API}** | {所有数据来自 `mock/` 目录的硬编码常量，禁止出现 fetch/axios 调用} |
| 2 | **{不实现认证与权限}** | {页面直接可达，不做登录校验} |
| 3 | **{不做响应式布局}** | {按 uiux_design_specification.md §{n}，仅适配桌面端} |
| 4 | **{不做性能优化}** | {禁止 lazy load / memo / code split，保持代码直白可读} |
| 5 | **{不写自动化测试}** | {按 uiux_design_specification.md §{n}，本版本不要求} |
| 6 | **{不自行升级依赖版本}** | {版本锁定见 §6，与平台规范一致} |
| 7 | **{不新增设计文档中没有的页面或字段}** | {发现设计缺失应记录到遗留问题，不得自行发挥} |
| 8 | **{文件命名严格 kebab-case}** | {前端目录与文件命名规范} |

---

## 2 当前项目状态

{开工前的代码现状。说明哪些已存在、哪些是空的、本次要从哪里接续。}

| 路径 | 现状 | 本次动作 |
|------|------|---------|
| `src/frontend/packages/{component}/` | {不存在 / 空壳 / 已有 {n} 个页面} | {新建 / 补齐 / 改造} |
| `src/frontend/packages/{参考组件}/` | {已完成 v{n}} | {仅作参考，不修改} |
| `src/frontend/packages/shared/` | {已有 {组件清单}} | {复用，必要时补充} |
| `src/frontend/{monorepo 配置}` | {现状} | {注册新包} |

---

## 3 开发计划

> Phase 之间串行。每完成一个 Phase 应可独立编译通过。

### Phase 1: {阶段名，如 包初始化}

| # | 任务 | 产出 | 说明 | 状态 |
|---|------|------|------|------|
| 1.1 | {创建包目录与配置} | `packages/{component}/package.json` 等 | {依赖版本严格按 §6 锁定} | |
| 1.2 | {注册到 monorepo} | `{workspace 配置文件}` | {说明} | |

### Phase 2: {阶段名，如 TypeScript 类型定义}

| # | 任务 | 产出 | 说明 | 状态 |
|---|------|------|------|------|
| 2.1 | {定义业务对象接口} | `src/models/{resource}.ts` | {字段逐一对应 tech_design §2，含可空性与默认值} | |
| 2.2 | {定义枚举} | `src/models/enums.ts` | {E 前缀，取值与 tech_design §{n} 一致} | |

### Phase 3: {阶段名，如 Mock 数据}

| # | 任务 | 产出 | 说明 | 状态 |
|---|------|------|------|------|
| 3.1 | {提取 HTML 稿中的数据} | `src/mock/{resource}.ts` | {数据来源见 §1.2；跨页面 ID 必须一致} | |

### Phase 4: {阶段名，如 页面组件实现 — 列表页}

| # | 任务 | 产出 | 说明 | 状态 |
|---|------|------|------|------|
| 4.1 | {实现 P{nn}} | `src/pages/{page-kebab}/index.tsx` | {按 uiux §3.x：查询条件区、功能按钮区、列表区、行交互} | |

### Phase 5: {阶段名，如 页面组件实现 — 详情页}

| # | 任务 | 产出 | 说明 | 状态 |
|---|------|------|------|------|
| 5.1 | {实现 P{nn}} | `src/pages/{page-kebab}/index.tsx` | {创建/只读/编辑三模式切换} | |

### Phase 6: {阶段名，如 路由集成}

| # | 任务 | 产出 | 说明 | 状态 |
|---|------|------|------|------|
| 6.1 | {注册路由} | `src/routes.tsx` | {与 uiux §4 逐条对齐，见 §5 速查表} | |
| 6.2 | {挂载到 Portal 菜单} | `{portal 菜单配置}` | {菜单结构见 uiux §2.2} | |

### Phase 7: {阶段名，如 集成验证}

| # | 任务 | 产出 | 说明 | 状态 |
|---|------|------|------|------|
| 7.1 | {编译验证} | — | `{build 命令}` 无错误 | |
| 7.2 | {逐页目视核对} | — | {对照 `screen.png` 与 uiux §3.x} | |
| 7.3 | {跳转链路验证} | — | {对照 uiux 的 page_flow 图，逐条走通} | |

---

## 4 文件清单汇总

### 4.1 新建文件

```
src/frontend/packages/{component}/
├── package.json
├── tsconfig.json
├── src/
│   ├── index.ts
│   ├── routes.tsx
│   ├── models/
│   │   ├── {resource}.ts
│   │   └── enums.ts
│   ├── mock/
│   │   └── {resource}.ts
│   ├── stores/
│   │   └── {resource}Store.ts
│   └── pages/
│       ├── {page-1-kebab}/index.tsx
│       └── {page-2-kebab}/index.tsx
```

### 4.2 修改的已有文件

| # | 文件路径 | 修改内容 |
|---|---------|---------|
| 1 | `{monorepo workspace 配置}` | {注册新包} |
| 2 | `{portal 菜单/路由配置}` | {挂载 {n} 个菜单项} |
| 3 | `{共享组件}` | {补充 {组件}} |

---

## 5 路由速查表

| # | 路径 | 页面组件 | Page ID | 模式 | 参数 |
|---|------|---------|---------|------|------|
| 1 | `{/component/resource}` | `{ResourceListPage}` | {P01} | {列表} | — |
| 2 | `{/component/resource/new}` | `{ResourceDetailPage}` | {P02} | {创建} | — |
| 3 | `{/component/resource/:id}` | `{ResourceDetailPage}` | {P02} | {只读} | `{id}` |
| 4 | `{/component/resource/:id/edit}` | `{ResourceDetailPage}` | {P02} | {编辑} | `{id}` |

**路由总数**：{n} 条。必须与 `{component}_uiux.md` §4 完全一致。

---

## 6 技术版本锁定

> 版本来自 `docs/uiux_design_specification.md` §{n} 和 `docs/technical_overview.md` §5。
> **不得自行升级**，遇到版本冲突应先提出问题而非擅自改版本。

| 依赖 | 锁定版本 | 用途 |
|------|---------|------|
| {React} | {18.x} | {UI 框架} |
| {TypeScript} | {5.x} | {类型系统} |
| {构建工具} | {版本} | {构建} |
| {路由库} | {版本} | {路由} |
| {状态库} | {版本} | {状态管理} |
| {UI 组件库} | {版本} | {基础组件} |

---

## 7 给执行者的提示

> 这一章沉淀的是**上一次开发踩过的坑**和容易误解的地方。开工前务必通读。

| # | 提示 |
|---|------|
| 1 | {设计文档中带 ⚠️ 标注的章节是"待完善"或"暂不实现"，遇到时按标注跳过，不要自行补全} |
| 2 | {列表页的"选中"有两种表现（复选框列 vs 整行高亮），不同页面可能不同，严格按 uiux §3.x.3 的说明实现} |
| 3 | {日期、数字、空值的展示格式统一走 uiux_design_specification.md §{n}，不要各页面自行 format} |
| 4 | {枚举值在 UI 上的展示文案与代码取值不同，代码用 E 前缀枚举取值，UI 展示用映射表} |
| 5 | {软删除记录在列表中不消失，而是以灰色样式显示，见 uiux_design_specification.md §{n}} |
| 6 | {发现设计文档存在矛盾或缺失时，**不要自行决定** —— 记录到 §8 补充说明，并在 review 中提出} |
| 7 | {提交前逐条核对 §1.4 红线，确认无越界} |

---

## 8 补充说明

### 8.1 开发过程中发现的设计问题

> 执行者在开发中发现的文档缺失、矛盾、歧义记录在此，供后续 review 处理。

| # | 位置 | 问题 | 临时处理方式 | 工程师的答复 |
|---|------|------|-------------|-------------|
| 1 | `{文档} §{章节}` | {问题描述} | {本次如何处理的} | |

> 「工程师的答复」列由工程师人工填写，AI 生成时留空。

### 8.2 有意偏离设计文档之处

| # | 设计文档要求 | 实际实现 | 原因 | 是否需回写文档 |
|---|-------------|---------|------|---------------|
| 1 | {要求} | {实现} | {原因} | {是/否} |

### 8.3 遗留项

| # | 遗留项 | 原因 | 计划版本 |
|---|--------|------|---------|
| 1 | {遗留内容} | {原因} | {v{n}} |
