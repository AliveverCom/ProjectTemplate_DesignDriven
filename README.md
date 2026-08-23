# ProjectTemplate_DesignDriven

**设计驱动开发（Design-Driven Development）的全量文档模板库**

**Templates Version**: 见 [VERSION](./VERSION)

---

## 0 关于本仓库

本仓库是一套**面向 AI 协作开发的文档模板库**：先把业务、技术、UI/UX 设计写成结构化文档，
再由 AI 依据这些文档产出代码——文档是唯一权威，代码是文档的实现。

所有模板都不是凭空设计的，而是从一个真实运行中的多组件平台项目里**反向总结提炼**而来：
先按早期模板写出上万行真实设计文档，在书写和多轮 review 过程中不断优化模板结构，
最后把这些优化固化回模板。因此每份模板里的章节划分、表格列、硬性规则，
都对应着实际踩过的坑（详见各模板顶部的说明注释块）。

### 0.1 怎么用

把本仓库的内容整体放进你项目的 `templates/` 目录：

```bash
git clone https://github.com/AliveverCom/ProjectTemplate_DesignDriven.git templates
rm -rf templates/.git
```

之后按 [§7 使用流程](#7-使用流程) 复制模板到 `docs/` 与 `docs/components/{组件}/` 下并改名填写。
**本仓库的根目录 == 你项目中的 `templates/` 目录**，下文出现的 `templates/xxx` 路径
指的就是本仓库的 `xxx`。

### 0.2 仓库内容一览

| 目录 | 份数 | 内容 |
|------|------|------|
| `platform/` | 7 | 平台级文档模板 → 落盘到 `docs/` 根目录 |
| `component/` | 6 | 组件级文档模板 → 落盘到 `docs/components/{组件}/` |
| `mermaid/` | 7 | 绘图规范 + 6 种 `.mmd` 图模板（白底 / 分类浅色 / 全黑字） |
| `ai_dev/` | 18 | AI 开发过程文档模板 → 落盘到 `ai_dev_history/` |

---

## 1 Table of Contents

- [0 关于本仓库](#0-关于本仓库)
- [1 Table of Contents](#1-table-of-contents)
- [2 四大类模板](#2-四大类模板)
- [3 平台级 vs 组件级：最容易搞混的一件事](#3-平台级-vs-组件级最容易搞混的一件事)
- [4 目录结构](#4-目录结构)
- [5 命名约定](#5-命名约定)
- [6 文档通用写作约定](#6-文档通用写作约定)
- [7 使用流程](#7-使用流程)
- [8 模板与真实文档的对应关系](#8-模板与真实文档的对应关系)

---

## 2 四大类模板

| # | 类别 | 目录 | 落盘位置 | 说明 |
|---|------|------|---------|------|
| 1 | **平台级**产品/业务/技术文档 | `platform/` | `docs/` 根目录、项目根目录 | 描述**整个平台**的业务全景、技术架构与**全局规范** |
| 2 | **组件级**产品/业务/技术文档 | `component/` | `docs/components/{component}/` | 描述**单个组件内部**的业务对象、类、表、API、页面 |
| 3 | **Mermaid 图**模板 | `mermaid/` | 与所属文档同目录 | 统一的白底黑字、按类型浅色分类着色的绘图规范与模板 |
| 4 | **AI 开发过程**文档 | `ai_dev/` | `docs/ai_dev_history/`、`docs/components/{component}/ai_dev_history/` | Review 报告、开发计划、进度审查、改进计划、原始 Prompt |

---

## 3 平台级 vs 组件级：最容易搞混的一件事

平台级和组件级的文件名非常像，但**是两个不同层面的产品文档**，职责边界必须严格区分：

| 主题 | 平台级（`docs/` 根目录） | 组件级（`docs/components/{component}/`） |
|------|------------------------|----------------------------------------|
| **业务** | `business_overview.md`<br>平台定位、产品组件全景、用户角色、端到端业务流程 | `{component}_business_desc.md`<br>本组件的业务对象、属性、本组件内部的业务流程 |
| **技术** | `technical_overview.md`<br>分层架构、**全局规范**（API/DB/前端/后端/日志/安全/版本）、依赖版本锁定、部署 | `{component}_tech_design.md`<br>本组件的类、枚举、表、API 端点、源码目录 |
| **UI/UX** | `uiux_design_specification.md`<br>**设计模式**（单/双列表页、详情页、弹出框）、通用控件、配色、状态管理、格式化规则 | `{component}_uiux.md`<br>本组件的页面清单、菜单、逐页布局元素与行为、路由 |
| **版本** | `version_plan.md`<br>通用版本号规范（vA.B.C.D.E 五段语义、里程碑、VERSION 文件策略） | `{component}_version_plan.md`<br>本组件 v0.1→v1.0 每个版本的功能范围与技术栈 |
| **命名/目录** | `naming_convention.md`、`project_directory_plan.md` | 无（组件不重复定义，只遵守） |

### 三条硬规则

1. **规范只在平台级定义一次。** 组件级文档遇到全局规范时**只引用章节号**
   （例："本组件 API 遵循 `technical_overview.md` §4.1"），**不得重新定义**。
2. **冲突时以平台级为准。** 若组件文档与平台文档矛盾，视为组件文档的缺陷，
   应在 `01_DocReviewRefine` review 中提出并修正。
3. **上升路径。** 组件里发现的通用模式，先写
   `ai_dev/01_DocReviewRefine/_uiux_pattern_summary.md` 提案，经工程师确认后
   才写入平台级 `uiux_design_specification.md`，然后组件文档改为引用。

---

## 4 目录结构

```
templates/
├── readme.md                       # 本文件
├── VERSION                         # 模板库自身版本
│
├── platform/                       # ① 平台级文档模板 → docs/ 根目录
│   ├── _business_overview.md           # 平台业务与产品总览
│   ├── _technical_overview.md          # 平台技术总览 + 全局技术规范
│   ├── _uiux_design_specification.md   # 平台 UI/UX 设计规范
│   ├── _naming_convention.md           # 平台命名规范
│   ├── _project_directory_plan.md      # 项目目录规划（落盘在项目根目录）
│   ├── _version_plan.md                # 通用版本号规范 → docs/version_plan.md
│   └── _api_endpoint_list.md           # 全平台 API 端点汇总 → docs/APIs/
│
├── component/                      # ② 组件级文档模板 → docs/components/{component}/
│   ├── _business_desc.md               # 组件业务描述
│   ├── _tech_design.md                 # 组件技术设计
│   ├── _uiux.md                        # 组件 UI/UX 设计
│   ├── _version_plan.md                # 组件版本规划
│   ├── _sub_module_design.md           # 子模块/daemon 设计 → sub_{module}/
│   └── _migration_plan.md              # 老系统数据迁移 → old_projects_migration/
│
├── mermaid/                        # ③ Mermaid 图模板 → 与所属文档同目录
│   ├── mmd_style_guide.md              # 绘图规范（白底 / 分类浅色 / 黑字）
│   ├── _business_process.mmd           # 业务流程图
│   ├── _technical_architecture.mmd     # 技术架构图
│   ├── _class_diagram.mmd              # 类图
│   ├── _db_schema.mmd                  # 数据库表结构图
│   ├── _api_diagram.mmd                # API 调用关系图
│   └── _page_flow.mmd                  # 页面流转图 → ui_page_design/
│
└── ai_dev/                         # ④ AI 开发过程文档模板 → ai_dev_history/
    ├── readme.md                       # ai_dev 模板总说明 + Review 迭代协议
    ├── _prompt_history.md              # 原始 Prompt 归档 → ai_dev_history/ 根目录
    ├── 01_DocReviewRefine/             # 文档级 review 与 refine
    ├── 02_DevPlanAndReport/            # 开发计划 / 报告 / 保存现场 / AI 交接 / 改进计划
    ├── 03_CodeReviewRefine/            # 代码级 review 与 refine
    ├── 04_DebugFix/                    # 调试与缺陷修复
    └── 99_Prompts/                     # 可复用 Prompt 库
```

`ai_dev/` 的编号目录代表 **AI 开发生命周期的阶段**，落盘时**编号目录名原样保留**，
建在 `docs/ai_dev_history/`（平台级）或
`docs/components/{component}/ai_dev_history/`（组件级）下。

两条特别规则：

- `02_DevPlanAndReport/` 下**所有文档一律以所在版本号开头**：
  `v0.3_DevPlan.md`、`v0.3_BE_DevPlan.md`、`v0.3_DevReport.md`、`v0.3_Snapshot.md`、
  `v0.3_Handover.md`、`v0.3_ImprovePlan.md`。
- `prompt_history.md` 放在 `ai_dev_history/` **根目录**，不进任何编号子目录——
  它是贯穿整个开发周期的单一累积文件，每个组件各一份，只追加不新建。

详见 [ai_dev/readme.md](./ai_dev/readme.md)。

---

## 5 命名约定

### 5.1 模板文件名

- **前导 `_`** 表示"文件名后缀"：使用时把 `_` 替换为组件代码名。
  - `_business_desc.md` → `material_collector_business_desc.md`
  - `_tech_design.md` → `material_collector_tech_design.md`
  - `_class_diagram.mmd` → `material_collector_class_diagram.mmd`
- **平台级模板**同样带 `_`，但落盘时直接**去掉** `_`（不加前缀）。
  - `_business_overview.md` → `business_overview.md`
- **不带 `_` 的文件**（`readme.md`、`mmd_style_guide.md`、`VERSION`）是模板库自身的说明文件，
  不参与复制。

### 5.2 落盘文件名规则

| 范围 | 规则 | 示例 |
|------|------|------|
| `docs/` | snake_case | `material_collector_tech_design.md` |
| `src/backend/` | snake_case | `material_collector/` |
| `src/frontend/` | kebab-case | `material-collector/` |
| `ui_page_design/` 子目录 | `P{nn}  {页面中文名}`（两个空格） | `P01  信息源列表页` |
| `.mmd` / `.png` | 与所属文档同前缀 | `material_collector_page_flow.mmd` |
| ai_dev 文档 | `{类型}_{YYYYMMDD}[_v{版本}][-r{轮次}].md` | `docs_review_20260314-r10.md` |

---

## 6 文档通用写作约定

| # | 约定 | 说明 |
|---|------|------|
| 1 | **首章是目录** | 每份文档第一章必须是 Table of Contents / 索引 |
| 2 | **数字编号** | 所有章节使用 `1` / `1.1` / `1.1.1` 数字编号，锚点链接与标题一致 |
| 3 | **占位符** | `{花括号}` 内为待替换内容；发布前不得残留 |
| 4 | **表格优先** | 能用表格表达的不写长段落；属性、参数、行为一律用表 |
| 5 | **图文分离** | 图放 `.mmd` 源文件 + 渲染的 `.png`，Markdown 里引用 png 并给出 mmd 源链接 |
| 6 | **交叉引用** | 引用其它文档用相对路径 + 章节号，例：`[technical_overview.md](../../technical_overview.md) §4.3` |
| 7 | **可选章节** | 模板中标注 `> **可选章节**：...` 的部分按需保留或整节删除，删除后重排编号 |
| 8 | **版本号** | 文档头 `**Document Version**: {x.y.z}`，文末 `## Change Log` 记录每次变更 |
| 9 | **严重度色标** | 🔴 致命/高 · 🟠 高/中 · 🟡 中 · 🔵 低 |
| 10 | **进度色标** | 🟢 已完成 · 🟡 部分完成 · 🔴 未实现 · ⚪ 计划外 |
| 11 | **待办标记** | `> **⚠️ 待完善（{问题ID}）**：{说明}` |
| 12 | **人工填写列** | Review 类表格最后一列固定为「工程师的答复」，AI 生成时留空 |

---

## 7 使用流程

### 7.1 新建一个产品组件的文档

```
1. mkdir -p docs/components/{component}/{ai_dev_history,ui_page_design}
2. cp templates/component/_business_desc.md  docs/components/{component}/{component}_business_desc.md
   cp templates/component/_tech_design.md    docs/components/{component}/{component}_tech_design.md
   cp templates/component/_uiux.md           docs/components/{component}/{component}_uiux.md
   cp templates/component/_version_plan.md   docs/components/{component}/{component}_version_plan.md
3. echo 0.1.0 > docs/components/{component}/VERSION
4. 删除每个文件顶部的「模板说明」注释块，删除不需要的可选章节
5. 按需从 templates/mermaid/ 复制 .mmd 模板并改名，渲染 png
6. 逐节替换 {占位符}
```

### 7.2 新建平台级文档

```
cp templates/platform/_business_overview.md  docs/business_overview.md
cp templates/platform/_technical_overview.md docs/technical_overview.md
（去掉前导下划线，不加任何前缀）
```

### 7.3 AI 开发过程中

按生命周期从 `templates/ai_dev/{编号目录}/` 取对应模板，
按 `ai_dev/readme.md` 的命名规则和 Review 迭代协议产出报告。

---

## 8 模板与真实文档的对应关系

每个模板都是从来源项目里一份真实文档反向提炼出来的。下表给出**提炼来源**——
这些路径指的是来源项目（一个多组件内容生产平台）中的文件，不在本仓库内。
填写模板时若不确定某一节该写到什么颗粒度，可参照本表理解该节的设计意图。

| 模板 | 提炼来源（来源项目中最完整的那份） |
|------|---------------------|
| `platform/_business_overview.md` | `docs/business_overview.md` |
| `platform/_technical_overview.md` | `docs/technical_overview.md` |
| `platform/_uiux_design_specification.md` | `docs/uiux_design_specification.md` |
| `platform/_naming_convention.md` | `docs/naming_convention.md` |
| `platform/_project_directory_plan.md` | `project_directory_plan.md` |
| `platform/_version_plan.md` | `docs/templates/_version_plan.md`（旧位置，建议迁到 `docs/version_plan.md`） |
| `platform/_api_endpoint_list.md` | `docs/APIs/api_endpoint_list.md` |
| `component/_business_desc.md` | `docs/components/material_collector/material_collector_business_desc.md` |
| `component/_tech_design.md` | `docs/components/material_collector/material_collector_tech_design.md` |
| `component/_uiux.md` | `docs/components/material_collector/material_collector_uiux.md` |
| `component/_version_plan.md` | `docs/components/material_collector/material_collector_version_plan.md` |
| `component/_sub_module_design.md` | `docs/components/material_collector/sub_unified_feed_cltr/unified_feed_cltr_design.md` |
| `component/_migration_plan.md` | `docs/components/material_collector/old_projects_migration/data_migration_plan.md` |
| `mermaid/_business_process.mmd` | `docs/business_process.mmd`、`channel_management_business_process.mmd` |
| `mermaid/_technical_architecture.mmd` | `docs/technical_architecture.mmd` |
| `mermaid/_class_diagram.mmd` | `material_collector_class_diagram.mmd` |
| `mermaid/_db_schema.mmd` | `material_collector_db_schema.mmd` |
| `mermaid/_api_diagram.mmd` | `material_collector_api.mmd` |
| `mermaid/_page_flow.mmd` | `ui_page_design/material_collector_page_flow.mmd` |
| `ai_dev/01_DocReviewRefine/` | `ai_dev_history/review/docs_review_*.md`、`tech_design_review_*.md`、`FE_BE_review_*.md`、`html_review_*.md`、`uiux_design_summary_*.md` |
| `ai_dev/02_DevPlanAndReport/` | `ai_dev_history/dev_plan/BE_dev_plan_*.md`、`FE_dev_plan_*.md`、`*_implementation_*.md`、`dev_progress_review_*.md`、`*_improvement_plan_*.md`、`naming_convention_rename_plan_*.md`、`docs_reorganize_plan_*.md` |
| `ai_dev/03_CodeReviewRefine/` | `ai_dev_history/review/FE_review_*.md` |
| `ai_dev/04_DebugFix/` | `ai_dev_history/review/debug_*.md` |
| `ai_dev/99_Prompts/` | `docs/templates_prompt/uiux_prompts.md` |
| `ai_dev/_prompt_history.md` | `{component}/ai_dev_history/{component}_raw_prompts.md` |

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| 1.0.0 | 2026-08-23 | 首版：从 `docs/` 下真实文档反向总结，建立平台级 / 组件级 / mermaid / ai_dev 四类模板 |
