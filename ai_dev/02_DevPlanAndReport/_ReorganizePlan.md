<!--
模板说明（使用时删除本注释块）

【文档层级】AI 开发过程文档 —— `02_DevPlanAndReport`

【本目录收什么】开发计划、开发报告、保存现场、AI handover、改进计划、
       命名规范化与目录整理计划。即"计划做什么"和"实际做成什么"这两类文档。

【落盘目录】
  - 平台级：docs/ai_dev_history/02_DevPlanAndReport/
  - 组件级：docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/

【文件命名 —— 本目录的硬规则】所有文档一律以**所在版本号开头**：
       `v{版本号}_{DocType}[_{YYYYMMDD}].md`
  - v0.3_DevPlan.md / v0.3_BE_DevPlan.md / v0.3_FE_DevPlan.md / v0.3_DevReport.md
  - v0.3_Snapshot.md / v0.3_Handover.md / v0.3_ImprovePlan.md
  - v0.3_RenamePlan.md / v0.3_ReorganizePlan.md
  - 同一版本内同类型有多份时追加日期后缀：v0.3_ReorganizePlan_20260326.md
  - 版本号取自 {component}_version_plan.md；本文这类跨版本的整理计划取**发起时的
    当前版本号**

【本文件的定位】目录整理计划。当某个目录（典型是 ai_dev_history/）文件平铺过多、
       查找困难时，按文档类型建立子目录并批量归档。

【与同目录其它文档的区别】
  - v{x}_RenamePlan.md   —— 只改**文件/目录名**，不换所在层级。
  - 本文件（ReorganizePlan）—— 只**移动位置**、建子目录，不改文件名。
    两者常配合使用，但应分成两份文档、分两步执行，便于回滚与 Git rename 识别。
  - v{x}_DevPlan.md —— 改功能代码，与本文无关。

【触发条件】单目录文件数 > 20 且存在明显的类型分组。

【特点】这类计划通常是**边做边写**（执行完立刻把状态回填为已完成），
       文档本身就是执行记录，不需要工程师答复列。

【模板文件名】前导 `_` 只是模板标记，复制时按上面的命名规则重命名。
-->

# v{版本号} {目标目录} 目录整理计划

**Document**: v{版本号}_ReorganizePlan[_{YYYYMMDD}].md
**版本**: v{版本号}
**Date**: {YYYY-MM-DD}
**Component**: {component} / {目标目录}
**Target**: {将 {目标目录} 下的历史文档按类别归入子目录，提升可维护性}
**Developer**: AI Agent
**Status**: {🟢 已完成 / 🟡 执行中}

---

## 1 背景与动机

{`{目标目录}/` 目录随着开发迭代不断积累文档（{文档类型枚举}），所有文件平铺在同一层级，
 文件数量已达 {n}+，查找和管理效率下降。需要按文档类型建立子目录分类存放。}

**整理前的目录状态**：

| 项目 | 值 |
|------|-----|
| 文件总数 | {n} |
| 目录层级 | {全部平铺在根目录} |
| 主要文档类型 | {类型枚举} |
| 最早文件日期 | {YYYY-MM-DD} |
| 最新文件日期 | {YYYY-MM-DD} |

---

## 2 目录结构设计

> 标准结构见 `templates/ai_dev/readme.md`。整理目标一律向标准结构对齐，
> 不要自造新的分类体系。

```
{目标目录}/                            # ai_dev_history/
├── prompt_history.md                  # 原始 Prompt 累积记录（保留在根目录）
├── 01_DocReviewRefine/                # 文档级 review 与 refine
│   ├── docs_review_*.md               # 跨文档一致性审查
│   ├── tech_design_review_*.md        # 技术设计深度审查
│   ├── design_completeness_review_*.md # 设计完备性审查
│   ├── html_review_*.md               # HTML 设计稿审查
│   └── uiux_pattern_summary_*.md      # UI/UX 通用模式提炼
├── 02_DevPlanAndReport/               # 计划 / 报告 / 现场 / 交接（文件名以版本号开头）
│   ├── v{x}_DevPlan.md                # 版本开发计划
│   ├── v{x}_BE_DevPlan.md             # 后端开发计划
│   ├── v{x}_FE_DevPlan.md             # 前端开发计划
│   ├── v{x}_DevReport.md              # 版本开发报告
│   ├── v{x}_Snapshot.md               # 保存现场
│   ├── v{x}_Handover.md               # AI 之间交接
│   ├── v{x}_ImprovePlan.md            # 继续改进计划
│   ├── v{x}_RenamePlan.md             # 命名规范化计划
│   └── v{x}_ReorganizePlan.md         # 目录整理计划（本文件）
├── 03_CodeReviewRefine/               # 代码级 review 与 refine
│   └── {FE|BE}_code_review_*.md
├── 04_DebugFix/                       # 调试与缺陷修复
│   ├── debug_*.md
│   └── fix_report_*.md
└── 99_Prompts/                        # 可复用 Prompt 库
    └── prompt_library.md
```

**设计原则**：

| # | 原则 | 说明 |
|---|------|------|
| 1 | {只分两层} | {不建三级子目录，避免过度嵌套} |
| 2 | {按开发生命周期阶段分类} | {编号目录 01→99 对应文档审查→开发→代码审查→调试→Prompt，而非按年月} |
| 3 | {prompt_history.md 保留根目录} | {单文件持续追加，是该目录的入口文档，不归入任何编号子目录} |
| 4 | {编号目录名保持与 templates/ai_dev/ 一致} | {模板目录与落盘目录同名，便于对照取模板} |
| 5 | {02 目录内文件名以版本号开头} | {便于按版本聚类，`ls` 即按版本排序} |

---

## 3 分类规则

| 子目录 | 匹配规则 | 文件数 |
|--------|---------|--------|
| `01_DocReviewRefine/` | {文件名含 `docs_review`、`tech_design_review`、`design_completeness_review`、`html_review`、`uiux_pattern_summary`} | {n} |
| `02_DevPlanAndReport/` | {文件名含 `_dev_plan_`、`_implementation_`、`_progress_review_`、`_improvement_plan_`、`_rename_plan_`、`_reorganize_plan_`} | {n} → {n}（含本文件） |
| `03_CodeReviewRefine/` | {文件名含 `FE_review`、`BE_review`、`code_review`} | {n} |
| `04_DebugFix/` | {文件名以 `debug_` 开头，或含 `fix_report`} | {n} |
| `99_Prompts/` | {`prompt_library.md` 及其它可复用 prompt 集} | {n} |
| 根目录保留 | `prompt_history.md`{（原 `{component}_raw_prompts.md`）} | {1} |

**边界情况处理**：

| 文件 | 归类 | 理由 |
|------|------|------|
| `{文件名}` | `{子目录}` | {理由} |
| `{文件名}` | {根目录} | {理由} |

> 归入 `02_DevPlanAndReport/` 的旧文件若文件名不是以版本号开头，
> 需一并按 `v{版本号}_{DocType}.md` 重命名；重命名条目单独列在
> `v{版本号}_RenamePlan.md` 中，本文只负责移动。

---

## 4 执行步骤

| # | 操作 | 命令/说明 | 状态 |
|---|------|----------|------|
| 1 | 创建子目录 | `mkdir -p 01_DocReviewRefine 02_DevPlanAndReport 03_CodeReviewRefine 04_DebugFix 99_Prompts` | {🟢 已完成} |
| 2 | 移动文档审查类 | `git mv docs_review_*.md tech_design_review_*.md design_completeness_review_*.md html_review_*.md uiux_pattern_summary_*.md 01_DocReviewRefine/` | {🟢 已完成} |
| 3 | 移动计划与报告类 | `git mv *_dev_plan_*.md *_implementation_*.md *_progress_review_*.md *_improvement_plan_*.md *_rename_plan_*.md *_reorganize_plan_*.md 02_DevPlanAndReport/` | {🟢 已完成} |
| 4 | 移动代码审查类 | `git mv FE_review_*.md BE_review_*.md code_review_*.md 03_CodeReviewRefine/` | {🟢 已完成} |
| 5 | 移动调试类 | `git mv debug_*.md fix_report_*.md 04_DebugFix/` | {🟢 已完成} |
| 6 | 移动 Prompt 库 | `git mv prompt_library.md 99_Prompts/` | {🟢 已完成} |
| 7 | 验证结果 | {确认根目录仅剩 `prompt_history.md` + 五个编号子目录} | {🟢 已完成} |
| 8 | 更新交叉引用 | {扫描并更新指向被移动文件的链接，见 §5} | {🟢 已完成} |

> 使用 `git mv` 而非 `mv`，可让 Git 更准确地识别为 rename。

---

## 5 影响范围

- **无代码影响**：{仅涉及 `docs/` 目录下的文档文件移动，不影响任何源代码或构建流程。}
- **交叉引用**：{这些历史文档{不被 / 被} 代码或 CI 引用。}
  - {若被引用，列出需更新的引用点：}

| # | 引用所在文件 | 旧路径 | 新路径 | 状态 |
|---|-------------|--------|--------|------|
| 1 | `{文件}` | `{ai_dev_history/xxx.md}` | `{ai_dev_history/01_DocReviewRefine/xxx.md}` | {🟢} |
| 2 | `{文件}` | `{ai_dev_history/dev_plan/xxx.md}` | `{ai_dev_history/02_DevPlanAndReport/xxx.md}` | {🟢} |

- **Git 追踪**：{Git 会自动识别为 rename，历史记录保留完整。}
- **后续约定**：{新产出的文档按 §3 分类规则直接放入对应编号子目录，不再平铺到根目录。}

---

## 6 整理后状态

| 项目 | 整理前 | 整理后 |
|------|-------|--------|
| 根目录文件数 | {n} | {1} |
| `01_DocReviewRefine/` 文件数 | — | {n} |
| `02_DevPlanAndReport/` 文件数 | — | {n} |
| `03_CodeReviewRefine/` 文件数 | — | {n} |
| `04_DebugFix/` 文件数 | — | {n} |
| `99_Prompts/` 文件数 | — | {n} |
| 最大目录深度 | {1} | {2} |

**验证**：

```bash
# 确认根目录只剩 prompt_history.md + 五个编号子目录
ls -la {目标目录}/

# 确认文件总数未变
find {目标目录} -name "*.md" | wc -l
```
