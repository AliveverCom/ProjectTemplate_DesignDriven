# AI 开发过程文档模板（ai_dev）

本目录是 **AI 开发过程文档**（`ai_dev_history/`）的模板集合。
编号目录代表 **AI 开发生命周期的阶段**，落盘时按同名编号目录建在 `ai_dev_history/` 下。

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 目录结构与收录边界](#2-目录结构与收录边界)
- [3 落盘位置](#3-落盘位置)
- [4 文件命名规则](#4-文件命名规则)
- [5 统一色标](#5-统一色标)
- [6 Review 迭代协议](#6-review-迭代协议)
- [7 模板清单](#7-模板清单)
- [8 阶段流转](#8-阶段流转)

---

## 2 目录结构与收录边界

```
ai_dev/
├── readme.md                   # 本文件
├── _prompt_history.md          # → ai_dev_history/prompt_history.md（根目录，不进子目录）
│
├── 01_DocReviewRefine/         # 文档级 review 与 refine
├── 02_DevPlanAndReport/        # 开发计划 / 开发报告 / 保存现场 / AI 交接 / 改进计划
├── 03_CodeReviewRefine/        # 代码级 review 与 refine
├── 04_DebugFix/                # 调试与缺陷修复
└── 99_Prompts/                 # 可复用 Prompt 库
```

**收录边界**（决定一份新文档该放哪个目录的唯一判据）：

| 目录 | 审查/工作对象 | 收录什么 | 不收什么 |
|------|-------------|---------|---------|
| `01_DocReviewRefine` | **设计文档**（`.md` / `.mmd` / HTML 设计稿） | 跨文档一致性审查、技术设计审查、前后端设计完备性审查、HTML 稿对照 uiux 审查、通用 UI/UX 模式提炼 | 源代码审查 → `03` |
| `02_DevPlanAndReport` | **一个版本的交付过程** | 开发计划（总纲 / BE / FE）、开发报告、保存现场、AI 交接、改进计划、命名规范化与目录整理计划 | 审查报告 → `01`/`03` |
| `03_CodeReviewRefine` | **源代码** | 前端/后端代码审查、代码与设计文档的偏差审查、重构建议 | 单个缺陷的排查过程 → `04` |
| `04_DebugFix` | **单个/一批缺陷** | 缺陷排查记录、联调故障记录、批量修复汇总报告 | 成体系的代码审查 → `03` |
| `99_Prompts` | **Prompt 本身** | 可复用的标准 Prompt 模板 | 真实发生过的原始 prompt → `prompt_history.md` |

`99_Prompts` 编号为 99 是因为它不属于生命周期的任何阶段，是横跨全程的工具箱。

`prompt_history.md` 放在 `ai_dev_history/` **根目录**，不进任何编号子目录——
它是贯穿整个开发周期的单一累积文件，每个组件（及平台）各一份，只追加不新建。

---

## 3 落盘位置

模板复制到实际项目时，编号目录名**原样保留**：

| 层级 | 落盘路径 |
|------|---------|
| 平台级 | `docs/ai_dev_history/{编号目录}/` |
| 组件级 | `docs/components/{component_code_name}/ai_dev_history/{编号目录}/` |

```
docs/components/{component_code_name}/ai_dev_history/
├── prompt_history.md
├── 01_DocReviewRefine/
├── 02_DevPlanAndReport/
├── 03_CodeReviewRefine/
├── 04_DebugFix/
└── 99_Prompts/                 # 组件通常不建，直接用平台级的
```

---

## 4 文件命名规则

模板文件名的前导 `_` 只是**模板标记**，复制时按下表重新命名。

### 4.1 `02_DevPlanAndReport/` —— 版本号开头（硬规则）

**本目录所有文档一律以所在版本号开头**：

```
v{版本号}_{DocType}[_{YYYYMMDD}].md
```

| 文档类型 | 文件名示例 |
|---------|-----------|
| 版本总体开发计划 | `v0.3_DevPlan.md` |
| 后端开发计划 | `v0.3_BE_DevPlan.md` |
| 前端开发计划 | `v0.3_FE_DevPlan.md` |
| 开发报告 / 进度审查 | `v0.3_DevReport.md` |
| 保存现场 | `v0.3_Snapshot.md` |
| AI 之间交接 | `v0.3_Handover.md` |
| 改进计划 | `v0.3_ImprovePlan.md` |
| 命名规范化计划 | `v0.3_RenamePlan.md` |
| 目录整理计划 | `v0.3_ReorganizePlan.md` |

- 同一版本内同类型有多份时，追加日期后缀：`v0.3_Snapshot_20260320.md`。
- 版本号取自组件的 `{component}_version_plan.md`；跨版本的整理类计划取**发起时的当前版本号**。
- 版本号写到实际使用的段数即可（`v0.3` / `v1.2.3` / `v1.2.3.4` 都合法）。

### 4.2 其它目录 —— 类型 + 日期 + 轮次

```
{类型}_{YYYYMMDD}[-r{轮次}].md
```

| 目录 | 文件名示例 |
|------|-----------|
| `01_DocReviewRefine` | `docs_review_20260314-r10.md`<br>`tech_design_review_20260315_r3.md`<br>`design_completeness_review_20260308-r1.md`<br>`html_review_20260308-r2.md`<br>`uiux_pattern_summary_20260309-v1.md` |
| `03_CodeReviewRefine` | `FE_code_review_20260314-r1.md`<br>`BE_code_review_20260320-r2.md`<br>`code_review_20260314-r1.md`（前后端合并审查） |
| `04_DebugFix` | `debug_FE_API_20260316-r1.md`<br>`debug_daemon_crash_20260402.md`<br>`fix_report_20260318-r1.md` |
| `99_Prompts` | `prompt_library.md`（单一文件，只追加） |
| 根目录 | `prompt_history.md`（单一文件，只追加） |

> **轮次跨日期不重置。** 例：`docs_review_20260310-r6.md` → `docs_review_20260314-r7.md`。
> 轮次反映的是**同一审查线索的第几轮**，不是当天的第几次。

---

## 5 统一色标

| 类别 | 色标 |
|------|------|
| **严重度** | 🔴 致命 / 高 · 🟠 高 / 中 · 🟡 中 · 🔵 低 |
| **进度** | 🟢 已完成 · 🟡 部分完成 · 🔴 未实现 · ⚪ 计划外 |
| **验证** | ✅ 通过 · ❌ 不通过 |
| **阅读优先级** | 🔴 必读 · 🟠 重要 · 🟡 参考 |
| **遗留问题分档** | P1 阻塞性 · P2 重要改进 · P3 维护建议 |

---

## 6 Review 迭代协议

这是本项目最核心的人机协作方式，`01_DocReviewRefine` 与 `03_CodeReviewRefine` 的所有报告都遵守。

### 6.1 「工程师的答复」列是协议核心

每份审查报告的问题表**最后一列固定为「工程师的答复 / 工程师回复」**。
AI 生成报告时该列**一律留空**，不得代填、不得推测、不得写"待确认"之类的占位文字。
由工程师逐行人工填写，典型回复形如：

- `按你的建议改`
- `不予修改，理由：禁止纠结变量命名，只需属性自身命名一致即可`
- `按方案 A 修改`
- `明确注明。这两个字段以后会由其它 daemon 定期刷新，在此之前所有 review 忽略它们`

### 6.2 第 N 轮报告的第一章是「上轮落实确认表」

不是新问题。验证结果只有三种取值：

| 取值 | 含义 | 后续动作 |
|------|------|---------|
| ✅ 已完成 | 上轮问题已按答复修复并验证 | 不再出现在后续报告 |
| 🟡 部分完成 | 修了一部分 | **保留原 ID** 在本轮问题列表中重新列出 |
| 🔴 未落实 | 没改 | **保留原 ID** 在本轮问题列表中重新列出 |

### 6.3 已修复的问题不再复述，且不区分新老问题

> 工程师不关心到底是老问题还是新问题，总之他必须完全解决或答复这些问题。

报告头部的「说明」blockquote 必须显式声明本轮的**过滤规则**，例如：

> 本报告仅报告 🔴 高 和 🟡 中 级别的问题，忽略所有 🔵 低级别问题。
> 本报告仅列出 R1/R2 未覆盖的**新发现**问题。

### 6.4 问题 ID 全局唯一、永不复用

两种编号体系，一份报告内只用一种：

| 体系 | 规则 | 适用 |
|------|------|------|
| `R{轮次}-{序号}` | 每轮内从 01 重计，靠轮次号保证全局唯一 | 跨文档一致性审查、HTML 审查 |
| `{前缀}-{序号}` | 跨轮次连续递增（r2 止于 TD-18，r3 从 TD-19 起） | 单文档深度审查（`TD-` 技术设计、`UP-` 某组件专项） |

问题 ID 会被**写回设计文档正文**作为可追溯标注，例如：

```markdown
> **⚠️ 待完善（R7-04）**：本页面的完整 UI/UX 设计规格将由工程师在对应版本开发前补充完善。
```

因此 ID 一旦发出即冻结，不得复用或重排。

### 6.5 架构分歧必须给方案 A/B 并标注推荐项

对于需要工程师做决策（而非单纯改错）的问题，报告中必须写成：

> **方案 A**（推荐）：{做法}，{代价}
> **方案 B**：{做法}，{代价}

让工程师回一句「按方案 A」即可闭环，而不是只描述问题让工程师自己想办法。

---

## 7 模板清单

| 目录 | 模板文件 | 用途 |
|------|---------|------|
| 根 | `_prompt_history.md` | 原始 prompt 历史归档（倒序累积，单一文件） |
| `01_DocReviewRefine` | `_docs_review.md` | 跨文档一致性审查报告 |
| | `_tech_design_review.md` | 单文档深度审查报告 |
| | `_design_completeness_review.md` | 前后端设计文档完备性审查（能否仅凭文档独立开发） |
| | `_html_review.md` | HTML 设计稿对照 uiux 文档的逐页审查 |
| | `_uiux_pattern_summary.md` | 通用 UI/UX 模式提炼提案（是否上升为平台规范） |
| `02_DevPlanAndReport` | `_DevPlan.md` | 版本总体开发计划 |
| | `_BE_DevPlan.md` | 后端开发计划 |
| | `_FE_DevPlan.md` | 前端开发计划 |
| | `_DevReport.md` | 开发报告 / 进度审查 |
| | `_Snapshot.md` | 保存现场（session 被动中断时的快照） |
| | `_Handover.md` | AI 之间交接（主动移交时的任务说明书） |
| | `_ImprovePlan.md` | 改进计划（待工程师确认或补充的清单） |
| | `_RenamePlan.md` | 目录与文件命名规范化执行计划 |
| | `_ReorganizePlan.md` | 目录整理计划 |
| `03_CodeReviewRefine` | `_CodeReview.md` | 前端/后端代码审查报告 |
| `04_DebugFix` | `_DebugRecord.md` | 单个缺陷的排查与修复记录 |
| | `_FixReport.md` | 一批缺陷的修复汇总报告 |
| `99_Prompts` | `_PromptLibrary.md` | 可复用标准 Prompt 库 |

---

## 8 阶段流转

```
       {component}_business_desc / tech_design / uiux 写成
                        │
                        ▼
        ① 01_DocReviewRefine   ──►  文档 review → 工程师答复 → refine → 再 review
                        │            （直到 design_completeness_review 判定"可独立开发"）
                        ▼
        ② 02_DevPlanAndReport  ──►  v{x}_DevPlan / BE / FE
                        │            ├─ 中途中断 → v{x}_Snapshot
                        │            └─ 移交他人 → v{x}_Handover
                        ▼
                     编码实现
                        │
                        ▼
        ③ 03_CodeReviewRefine  ──►  代码 review → 工程师答复 → 修改
                        │            （若判定为"文档错" → 退回 ①）
                        ▼
        ④ 04_DebugFix          ──►  debug_*.md 排查 → fix_report_*.md 汇总
                        │
                        ▼
        ② 02_DevPlanAndReport  ──►  v{x}_DevReport 回填交付结论
                        │            └─ 未尽事项 → v{x}_ImprovePlan
                        ▼
                   进入下一版本
```

全程：实质性 prompt → `prompt_history.md`；沉淀出的可复用 prompt → `99_Prompts/_PromptLibrary.md`。

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| 1.0.0 | 2026-08-23 | 首版：5 个编号目录（01/02/03/04/99）+ 根目录 `prompt_history.md`，共 18 份模板 |
