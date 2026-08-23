<!--
模板说明（使用时删除本注释块）

【文档层级】`99_Prompts` —— 可复用 Prompt 库。
  归档本项目中已验证有效的标准 Prompt，每个组件走同一套流程时直接复制使用，
  保证不同组件、不同 session 的产出格式一致。

【落盘目录】平台级 docs/ai_dev_history/99_Prompts/prompt_library.md
  组件通常**不单独维护一份**，直接引用平台级的这一份。
  只有当某组件确有大量专属流程时，才在
  docs/components/{component}/ai_dev_history/99_Prompts/ 下补一份**增量**文件，
  并在开头声明"本文件只列本组件专属 prompt，通用 prompt 见平台级 prompt_library.md"。

【模板文件名】前导 `_` 只是模板标记，复制时去掉 `_` → `prompt_library.md`。

【编号 99 的含义】它**不属于开发生命周期的任何阶段**，而是横跨全程的工具箱，
  因此排在最后，与 01–04 的阶段目录区分开。

【与 ai_dev_history/prompt_history.md 的区别】务必分清，两者不可互相替代：

  | 维度 | 本文件 prompt_library.md | prompt_history.md |
  |------|-------------------------|-------------------|
  | 性质 | **可复用模板** | **历史归档** |
  | 内容 | 抽象过的标准 prompt，去掉了一次性上下文 | 真实发生过的原始 prompt，逐字原样 |
  | 时间 | 无时间戳，按用途分类 | 带时间戳，倒序累积 |
  | 数量 | 全平台一份 | 每组件一份 |
  | 用途 | 回答"下次做同类事该发什么 prompt" | 回答"这份文档为什么长这样" |
  | 变更 | 产出不理想就**修订**该条 | 只追加，历史条目**永不修改** |

  流转关系：prompt_history.md 中一条 prompt 被反复使用、证明有效后，
  应**抽象后补进本文件**；本文件的条目不回写 prompt_history.md。

【维护约定】某条 prompt 用过后发现产出不理想 → **修订本文件**，而不是下次临场重写。
  这是本文件存在的全部意义。
-->

# 可复用 Prompt 库

**Document Version**: {x.y.z}
**适用范围**：{平台名称} 全部产品组件
**维护约定**：某条 Prompt 用过后产出不理想，应**修订本文件**，而非下次临场重写。

---

## 索引

按 `ai_dev_history/` 的阶段目录归组：

| # | 用途 | 输入 | 产出 | 落盘阶段目录 |
|---|------|------|------|-------------|
| 1 | [由业务描述生成 UI/UX 需求文档](#1-由业务描述生成-uiux-需求文档) | `{component}_business_desc.md` | `{component}_uiux.md` | —（组件文档根目录） |
| 2 | [用 Google Stitch 生成页面设计稿](#2-用-google-stitch-生成页面设计稿) | 页面需求描述 | `ui_page_design/P{nn}  {页面名}/code.html` | —（组件文档根目录） |
| 3 | [Review UI/UX 需求文档](#3-review-uiux-需求文档) | `{component}_uiux.md` | 修订后的 uiux.md | `01_DocReviewRefine/` |
| 4 | [回复 Review 的三种处理方式](#4-回复-review-的三种处理方式) | review 报告 + 工程师答复 | 修订后的文档或 HTML + 新一轮 review | `01_DocReviewRefine/` |
| 5 | [生成开发计划](#5-生成开发计划) | 设计文档 + version_plan | `v{版本}_{BE\|FE}_DevPlan.md` | `02_DevPlanAndReport/` |
| 6 | [跨文档一致性审查](#6-跨文档一致性审查) | 组件全部文档 | `docs_review_{YYYYMMDD}-r{n}.md` | `01_DocReviewRefine/` |
| 7 | [保存现场与 AI 交接](#7-保存现场与-ai-交接) | 当前 session 的全部上下文 | `v{版本}_Snapshot.md`、`v{版本}_Handover.md` | `02_DevPlanAndReport/` |
| 8 | [代码审查](#8-代码审查) | 源代码 + 设计文档 | `{BE\|FE}_code_review_{YYYYMMDD}-r{n}.md` | `03_CodeReviewRefine/` |
| 9 | [缺陷修复与回归](#9-缺陷修复与回归) | review 报告 + 工程师答复 | `fix_report_{YYYYMMDD}-r{n}.md` | `04_DebugFix/` |
| 10 | [开发进度审查](#10-开发进度审查) | 代码 + version_plan | `v{版本}_DevReport.md` | `02_DevPlanAndReport/` |
| 11 | [绘制 Mermaid 图表](#11-绘制-mermaid-图表) | 文档内容 | `*.mmd` + `*.png` | —（与所属文档同目录） |

> **路径约定**：下文中的 `{ai_dev}` 代表
> `docs/components/{component}/ai_dev_history/`（组件级）或
> `docs/ai_dev_history/`（平台级）。

---

## 1 由业务描述生成 UI/UX 需求文档

**使用场景**：`{component}_business_desc.md` 已完成，需要产出该组件的 UI/UX 需求文档。

**产出文件**：`docs/components/{component}/{component}_uiux.md`

**Prompt**：

```
根据 docs/components/{component}/{component}_business_desc.md 生成该组件的 UI/UX 需求文档，
输出到 docs/components/{component}/{component}_uiux.md。

要求：
1. 严格遵循 templates/component/_uiux.md 的模板结构。
2. 页面设计模式必须引用 docs/uiux_design_specification.md 的既有章节
   （单列表页 / 双列表页 / 树状列表页 / 业务对象创建页 / 只读页 / 编辑页），
   在每个页面小节开头用 blockquote 注明"遵循 uiux_design_specification.md §x.x.x"。
   不要重复描述规范中已有的通用规则。
3. §2.1 Page List 必须包含 Description 列。
4. §2.2 Navigation Menu 用代码块画出该组件在 unified_portal 侧边栏中的菜单树，
   并注明默认进入哪个页面。
5. §2.3 Page-Object-API Mapping 的 Dependent APIs 一律使用 REST 端点路径
   （形如 GET /api/v1/xxx），不要用逻辑名称（形如 Xxx.Search）。
   端点必须与 {component}_tech_design.md §5 完全一致。
6. §3 每个页面必须包含四个子节：UI Design / Page Business Functions /
   Page Layout and Elements / Page Behaviors。
7. Page Layout and Elements 按区域分组（A. 左侧面板、B. 右侧面板…），
   每个区域再分：查询条件区域、功能按钮区域、列表区域、列表行交互。
   每个控件给出 Element / Control Type / Default Value / Description。
   列表区域给出每列的 Column Header / Data Source / Width / Style / Sortable。
8. Page Behaviors 用编号表格：Trigger / Action / Result，Action 中写明具体调用的 REST 端点。
9. 文档末尾包含 §4 路由定义和 Change Log。
10. 首章为 Table of Contents，所有章节使用数字编号。

不要生成任何页面 HTML，本步骤只产出需求文档。
```

---

## 2 用 Google Stitch 生成页面设计稿

**使用场景**：UI/UX 需求文档已完成，需要为每个页面生成 HTML 设计稿。

**产出文件**：`docs/components/{component}/ui_page_design/P{nn}  {页面中文名}/code.html` + `screen.png`

**Stitch Prompt（基础模板）**：

```
create a new page with light style. Without portal framework and main menu.
```

**Stitch Prompt（完整模板）**：

```
Create a new page with light style. Without portal framework and main menu.

Page: {页面中文名}
Layout: {单列表页 / 双列表页 / 详情页 / Dashboard / 双栏调试台}

{粘贴 {component}_uiux.md 中该页面的 §3.x.3 Page Layout and Elements 全文}

Requirements:
- Light theme, white background.
- Desktop only, no responsive breakpoints.
- Use realistic sample data consistent with the field descriptions above.
- Table columns must match the column list exactly, in the same order.
- {其他该页面特有的要求}
```

**使用说明**：

| # | 说明 |
|---|------|
| 1 | Stitch 产出的 HTML 存为 `code.html`，渲染截图存为 `screen.png`，放在同一个 `P{nn}  {页面中文名}/` 目录下 |
| 2 | 目录名格式：`P{两位编号}{两个空格}{页面中文名}`，与 uiux.md §2.1 的 Page ID 和 Page Name 一致。**两个空格是硬性约定**，不是笔误 |
| 3 | HTML 稿生成后必须走 [§4](#4-回复-review-的三种处理方式) 的 review 流程与 uiux.md 对齐 |
| 4 | HTML 中的示例数据是后续 v0.1 纯前端版本的 **mock 数据来源**，务必真实合理、跨页面一致 |

---

## 3 Review UI/UX 需求文档

**使用场景**：工程师给出一大段 UI/UX 需求描述后，确认 AI 已将全部细节写入文档。

**Prompt**：

```
改进 docs/components/{component}/{component}_uiux.md。

完成后 review {component}_uiux.md，确保我上面 prompt 中的每个细节都已经被写入了文档，
没有丢失或简化。逐条列出我提到的要求以及它在文档中的落位章节，
若有未覆盖的要求，明确指出并补充。

这条 prompt 需要添加到 docs/components/{component}/ai_dev_history/prompt_history.md。
```

**使用说明**：

| # | 说明 |
|---|------|
| 1 | 这是**自检式 review**：让 AI 反向核对自己是否遗漏了需求细节 |
| 2 | 结尾的"添加到 prompt_history.md"是固定动作，确保大段需求描述被归档到 `{ai_dev}` 根目录的累积文件中 |
| 3 | 产出是**修订后的 uiux.md + 一份覆盖度核对清单**，不单独出 review 文件 |

---

## 4 回复 Review 的三种处理方式

**使用场景**：HTML review 报告已产出，工程师在「工程师的答复」列填完，需要 AI 执行。

### 4.a 以 HTML 为准，修改 UiUx.md

```
以 HTML 为准，修改 docs/components/{component}/{component}_uiux.md。

参照 docs/components/{component}/ai_dev_history/01_DocReviewRefine/html_review_{YYYYMMDD}-r{n}.md
中标记为"以 HTML 为准"的条目，将 uiux.md 中对应的规格描述改为与 HTML 实际实现一致。
修改后更新 uiux.md 的 Change Log。
```

### 4.b 以 UiUx.md 为准，修改 HTML

```
以 docs/components/{component}/{component}_uiux.md 为准，修改 HTML。

参照 01_DocReviewRefine/html_review_{YYYYMMDD}-r{n}.md 中标记为"以 UiUx.md 为准"的条目，
逐页修改 ui_page_design/P{nn}  {页面名}/code.html，使其符合 uiux.md 的规格。
只改被指出的问题点，不要顺手改动其他部分。
```

### 4.c 按工程师答复逐页修复并重新 review

```
根据 docs/components/{component}/ai_dev_history/01_DocReviewRefine/html_review_{YYYYMMDD}-r{n}.md
中的「工程师的答复」，逐页修复对应的审查基准（uiux.md）或审查对象（code.html）。

1) 修复完毕后再次进行 review。review 报告不需要包含已经修复的内容，
   只需要包含依然存在的问题，或新发现的问题。
   不需要区分这两种状态，因为工程师不关心到底是老问题还是新问题，
   总之他必须完全解决或答复这些问题。

2) review 报告的格式：
   最上面给出审查的基本信息："文档标题、审查日期、审查基准、审查对象、上次审查文件名"。
   它的下面以页面为单位输出。每个页面先显示页面 ID 和标题，下面是问题列表：
   问题 ID、基准描述、被审查对象情况、问题严重度（用颜色小球：
   🔴 红色致命问题、🟠 橙色高、🟡 黄色中、🔵 蓝色低）、
   工程师的回复（内容为空，之后工程师人工填写）。
   最后给出汇总表（页面 × 严重度）和共性问题。

3) 将 review 结果保存到
   docs/components/{component}/ai_dev_history/01_DocReviewRefine/ 目录中，
   文件名格式为 html_review_{yyyyMMdd}-r{review times}.md。
```

**使用说明**：

| # | 说明 |
|---|------|
| 1 | 4.c 是**主流程**，4.a/4.b 用于工程师已统一决定以某一方为准的批量场景 |
| 2 | 轮次 `r{n}` 跨日期不重置 |
| 3 | 「工程师的回复」列在新报告中一律留空 |

---

## 5 生成开发计划

**使用场景**：设计文档审查通过，进入某个版本的开发前。

**产出文件**：`{ai_dev}/02_DevPlanAndReport/v{版本}_{BE|FE}_DevPlan.md`
（不分前后端的总纲用 `v{版本}_DevPlan.md`）

**Prompt**：

```
为 {component} 组件的 v{目标版本} 生成{后端/前端}开发计划，
输出到 docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{目标版本}_{BE|FE}_DevPlan.md。

严格遵循 templates/ai_dev/02_DevPlanAndReport/_{BE|FE}_DevPlan.md 的模板结构。

要求：
1. §0 关联设计文档索引必须精确到"文件路径 + 必读章节"，
   分为：本组件设计文档 / 平台级规范文档 / 公共库参考文档 / 上一版本代码参考 四组。
   这一章的作用是让新 session 的 AI 冷启动时知道该读什么，务必完整。
2. §1.2 必须明确写出"本版本不包含的内容"及其计划版本，防止超范围实现。
3. §2 给出本版本完成后的目标目录树，本版本新增/改造的部分用加粗标注。
4. §4 开发任务按 Phase 分组，Phase 之间串行、Phase 内可并行。
   每个任务给出：任务描述 / 产出文件路径 / 实现要点 / 状态列（留空）。
5. §5 API 端点路由表必须与 {component}_tech_design.md §5 的端点逐一对齐，
   并标注每个端点对应的 tech_design 章节号。
6. §6 开发注意事项要沉淀上一版本踩过的坑
   （查阅 ai_dev_history/04_DebugFix/ 下的 debug_*.md、fix_report_*.md
   和 03_CodeReviewRefine/ 下的 *_code_review_*.md）。
7. 版本号、依赖版本必须与 docs/technical_overview.md §5 的锁定表一致，不得自行升级。

基准文档：{component}_version_plan.md §{n}（v{目标版本} 的功能范围定义）。
```

---

## 6 跨文档一致性审查

**使用场景**：组件的多份设计文档都有修改后，检查彼此是否仍然自洽。

**产出文件**：`{ai_dev}/01_DocReviewRefine/docs_review_{YYYYMMDD}-r{n}.md`

**Prompt**：

```
对 docs/components/{component}/ 目录下所有 md 和 mmd 文件做跨文档一致性审查，
并与 docs/technical_overview.md、docs/naming_convention.md、
docs/uiux_design_specification.md 交叉校验。

输出到
docs/components/{component}/ai_dev_history/01_DocReviewRefine/docs_review_{YYYYMMDD}-r{n}.md，
严格遵循 templates/ai_dev/01_DocReviewRefine/_docs_review.md 的模板结构。

要求：
1. 第一章是上一轮（r{n-1}）问题的落实确认表，不是新问题。
   逐条核对 r{n-1} 报告中每个问题的工程师答复是否已落实，结果只能是
   ✅ 已完成 / 🟡 部分完成 / 🔴 未落实。
2. 已修复并验证通过的问题不再出现在新问题列表中。
3. 本报告仅报告 🔴 高 和 🟡 中级别的问题，忽略所有 🔵 低级别问题。
4. 问题 ID 用 R{n}-{两位序号}，全局唯一不复用。
5. 每个问题先在「问题汇总」表中一行简述，再用独立小节详述：
   严重程度 / 涉及文件 / 问题描述 / 影响 / 需要工程师决定的方案 A 与方案 B（标注推荐项）/
   工程师的回复（留空）。
6. 最后给出「跨文档一致性最终验证」勾选表，至少覆盖：
   端点总数与路径一致性、Page Behaviors 中的 API 引用、class_diagram 与 tech_design 字段名、
   db_schema 与表定义列名、business_desc 业务对象与类成员映射、枚举唯一权威来源、
   软删除等平台字段约定、命名规范、章节交叉引用有效性、无残留 *(待补充)* 标记。

重点检查：两份文档对同一事物给出互斥定义的情况——这是最高优先级问题，
因为工程师无法判断以哪一份为准。
```

---

## 7 保存现场与 AI 交接

**使用场景**：两类，务必分清——

| 场景 | 用哪条 | 接手方 | 核心问题 |
|------|-------|-------|---------|
| 当前 session 上下文即将耗尽，同一条工作线要继续 | [7.a 保存现场](#7a-保存现场snapshot) | **同一个** AI 的新 session | "我刚才干到哪了" |
| 一个阶段做完，移交给另一个 AI / 另一个人接手 | [7.b AI 交接](#7b-ai-交接handover) | **另一个** AI 或工程师 | "你需要知道什么才能接手" |

**产出文件**：`{ai_dev}/02_DevPlanAndReport/v{版本}_Snapshot.md`、`v{版本}_Handover.md`
（同一版本内多次保存现场时加日期时间后缀：`v0.3_Snapshot_20260823-1430.md`）

### 7.a 保存现场（Snapshot）

```
我们的上下文快用完了。在结束前，把当前工作现场完整保存下来，
输出到 docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{当前版本}_Snapshot.md。

严格遵循 templates/ai_dev/02_DevPlanAndReport/_Snapshot.md 的模板结构。

必须写清以下内容，**假设读者是失忆后的你自己**：
1. 当前任务是什么、基准文档是哪几份（精确到文件路径 + 章节号）。
2. 已完成了什么：逐条列出，每条对应到具体文件路径和改动要点。
   已经验证通过的要标注验证方式（跑了什么命令、看了什么输出）。
3. 正在做什么：当前卡在哪一步、下一个动作是什么。
   如果有改到一半的文件，明确指出文件路径和未完成的部分。
4. 还没做什么：按原计划剩余的任务列表，保持原有的 Phase / 任务编号。
5. **隐性知识**——这是最重要的一节。写下那些从代码和文档里看不出来、
   只存在于本次对话中的信息：
   - 工程师在对话中做过的决策及其理由（尤其是"不这么做"的决策）；
   - 试过但失败的方案，以及失败原因（防止接手方重蹈覆辙）；
   - 踩过的环境坑、工具坑（命令参数、版本问题、路径问题）；
   - 对某段代码或文档的理解修正（"一开始以为 X，实际是 Y"）。
6. 恢复现场的第一步动作：新 session 打开后应该先读哪几个文件、先跑哪条命令。

不要写客套话和总结性套话，只写接手时真正用得上的事实。
```

### 7.b AI 交接（Handover）

```
本阶段工作已完成，需要移交给另一个 AI 接手后续工作。
生成交接文档，输出到
docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{当前版本}_Handover.md。

严格遵循 templates/ai_dev/02_DevPlanAndReport/_Handover.md 的模板结构。

必须写清以下内容，**假设接手方对本项目一无所知**：
1. 交接范围：移交什么、不移交什么（明确边界，防止接手方越界）。
2. 冷启动阅读清单：接手方开工前必须读的文件，按顺序列出，
   每条标注"文件路径 + 必读章节 + 为什么要读"，并标 🔴必读 / 🟠重要 / 🟡参考。
3. 当前状态：本阶段交付了什么，逐条对应到文件路径；
   哪些已验证、验证方式是什么；哪些未验证。
4. **接手方无法从代码看出来的隐性知识**——这是交接文档的核心价值，必须写：
   - 为什么这样设计（尤其是看起来绕的地方）；
   - 有意为之的取舍（"这里没做缓存是因为…"）；
   - 与设计文档不一致的地方及其原因（是文档待改，还是实现待改）；
   - 依赖的外部前提（环境变量、第三方账号、别的组件必须先跑起来）。
5. **已被否决的方案及原因**——单列一节。逐条写：方案是什么、为什么否决、
   否决人是谁（工程师 / AI 自行判断）。
   目的是防止接手方"重新发明"一个已经被否决过的方案，再浪费一轮讨论。
6. 已知问题与遗留项：分 P1 阻塞 / P2 重要 / P3 维护 三档，每条给出建议动作。
7. 下一步建议：接手方应该先做什么，产出哪份文档。

不要写"祝好运"之类的内容，只写事实和判断依据。
```

**使用说明**：

| # | 说明 |
|---|------|
| 1 | Snapshot 是**对内**的（给未来的自己），Handover 是**对外**的（给别人），详略取舍不同：Snapshot 可省略项目背景，Handover 必须补全 |
| 2 | 两者都必须写「隐性知识」和「已否决方案」——这是唯一无法从仓库里重新读出来的信息，丢了就真丢了 |
| 3 | 文件名以版本号开头（`v0.3_Snapshot.md`），与同目录的 DevPlan / DevReport 天然按版本聚在一起 |
| 4 | 交接完成后，接手方的第一条 prompt 应当是"读 v{版本}_Handover.md 并确认理解，列出你的疑问"，而不是直接开工 |

---

## 8 代码审查

**使用场景**：某版本代码写完，需要以设计文档为基准审查实现是否符合规格。

**产出文件**：`{ai_dev}/03_CodeReviewRefine/{BE|FE}_code_review_{YYYYMMDD}-r{n}.md`

**Prompt**：

```
以设计文档为基准，审查 {component} 的{后端/前端}源代码，输出到
docs/components/{component}/ai_dev_history/03_CodeReviewRefine/{BE|FE}_code_review_{YYYYMMDD}-r{n}.md。

严格遵循 templates/ai_dev/03_CodeReviewRefine/_CodeReview.md 的模板结构。

审查基准（后端）：
- docs/components/{component}/{component}_tech_design.md
  §2 类定义 / §4 数据库定义 / §5 API 定义 / §10 源码目录布局
- docs/technical_overview.md §4.1 API 规范、§4.2 数据库规范、§4.4 后端规范、§4.5 日志规范
- docs/naming_convention.md 全文

审查基准（前端）：
- docs/components/{component}/{component}_uiux.md §2 页面总览 / §3 逐页规格 / §4 路由定义
- docs/uiux_design_specification.md 全文
- docs/naming_convention.md 全文

审查对象：
- 后端：src/backend/{component}/
- 前端：src/frontend/packages/{component-kebab}/

要求：
1. 第一章是上一轮（r{n-1}）问题的落实确认表；首轮则写"首次审查，无上轮问题"。
   已修复并验证通过的问题不再出现在新问题列表中。
2. **必须区分"代码错"与"文档错"**，这是本次审查最重要的判断：
   - **代码错**：文档规格合理，实现没做到 → 列入本报告的「问题列表」，由开发修复。
   - **文档错**：实现是合理的，文档写错了或已过时 → **不要在本报告中要求改代码**，
     而是单列一节「应转 01_DocReviewRefine 的条目」，说明应该怎么改文档，
     后续走 docs_review 流程处理。
   - **两者都错 / 无法判断哪个对**：列入「需要工程师决定」，给出方案 A/B 并标注推荐项。
3. 每个问题给出：问题 ID（CR-{两位序号}，全局唯一不复用）/ 严重度
   （🔴 致命 · 🟠 高 · 🟡 中 · 🔵 低）/ 文件路径:行号 / 现状 / 文档规格 /
   修改建议 / 工程师回复（留空）。
4. 重点检查项：
   - API 端点路径、方法、请求参数、响应结构是否与 tech_design §5 逐字一致；
   - 数据库列名、类型、索引是否与 tech_design §4 一致；
   - 页面元素、列表列、按钮、交互行为是否与 uiux §3 一致；
   - 命名是否符合 naming_convention（C/E/I 前缀、snake_case 列名、kebab-case 前端目录）；
   - 是否有超出本版本 version_plan 范围的实现（超前实现同样是问题）；
   - 分层是否被破坏（handler 直接访问数据库、service 拼 SQL 等）。
5. 只报 🔴 和 🟠 级别的问题；🟡 汇总成一节列表即可；🔵 忽略。
6. 结尾给出「与设计文档的偏差」汇总表和统计表。

不要修改任何代码，本步骤只产出审查报告。
```

**使用说明**：

| # | 说明 |
|---|------|
| 1 | 「代码错 vs 文档错」的分流是本条 prompt 的核心。混在一起会导致工程师无法逐条答复，也会让文档缺陷被当成代码缺陷"修"掉，越修越偏 |
| 2 | 转到 `01_DocReviewRefine` 的条目要保留原 `CR-{n}` 编号，便于两边对账 |
| 3 | 轮次 `r{n}` 跨日期不重置；前后端各自独立编号 |
| 4 | 修复阶段走 [§9](#9-缺陷修复与回归)，产出落在 `04_DebugFix/` |

---

## 9 缺陷修复与回归

**使用场景**：code review 报告或 debug 记录中的问题，工程师已在「工程师回复」列填完答复，需要 AI 逐条执行修复。

**产出文件**：`{ai_dev}/04_DebugFix/fix_report_{YYYYMMDD}-r{n}.md`

**Prompt**：

```
根据 docs/components/{component}/ai_dev_history/03_CodeReviewRefine/{BE|FE}_code_review_{YYYYMMDD}-r{n}.md
中的「工程师回复」，逐条执行修复，并输出修复报告到
docs/components/{component}/ai_dev_history/04_DebugFix/fix_report_{YYYYMMDD}-r{n}.md。

要求：
1. **逐条对应**：修复报告的条目编号必须与来源报告的问题 ID（CR-xx）一一对应，
   不合并、不拆分、不遗漏。每条给出：
   问题 ID / 工程师答复 / 实际改动（文件路径 + 改动要点）/ 处理结果。
   处理结果只能是：✅ 已修复 / 🟡 部分修复（说明剩余部分）/ ⏭️ 按答复不修改（说明理由）/
   🔴 无法修复（说明阻塞原因）。
2. **严格按工程师的答复执行**，不要自行扩大修改范围。
   若答复是"按你的建议改"，就按来源报告中你自己给的建议改，不要临时换方案。
   若在修复过程中发现答复本身有问题，**先停下**，在报告中单列「需要工程师二次确认」一节，
   不要擅自变通。
3. **必须列出「本次修复可能影响到的既有功能」** —— 单列一节，逐条写：
   - 改动了哪个文件的哪个函数 / 哪个页面的哪个区域；
   - 哪些既有功能会经过这段代码（调用方、复用该组件的其他页面、依赖该字段的其他 API）；
   - 影响判断：🔴 很可能受影响 / 🟡 可能受影响 / 🟢 理论上不受影响。
   判断依据要写出来（"grep 到 3 处调用"比"应该没影响"有用得多）。
4. **对上一条中标为 🔴/🟡 的每一项做回归验证**，在报告中单列「回归验证」一节：
   - 验证方式（跑了什么命令 / 打开了哪个页面 / 调了哪个端点）；
   - 实际结果（贴关键输出，不要只写"正常"）；
   - 结论：✅ 通过 / 🔴 发现新问题（新问题另起编号 FIX-{n}，列入遗留项）。
   无法自动验证的项，明确写"需人工验证"并给出人工验证步骤。
5. 结尾给出：修复统计表（按处理结果分类计数）、遗留项清单、
   以及是否需要触发新一轮 code review 的建议。

不要顺手做与本次修复无关的改动（包括格式化、重命名、"顺便优化"）。
```

**使用说明**：

| # | 说明 |
|---|------|
| 1 | 第 3、4 条是本条 prompt 的核心：**改动影响面 + 回归验证**。缺了这两步，修复报告只是"我改了什么"，无法回答"改坏了什么" |
| 2 | 影响判断必须写依据，禁止写"应该没影响"这类无依据结论 |
| 3 | 回归中发现的新问题另起 `FIX-{n}` 编号，不复用 `CR-{n}`，避免与来源报告串号 |
| 4 | 修复报告与来源 review 报告用相同的 `{YYYYMMDD}-r{n}` 后缀，便于配对查阅 |
| 5 | 联调型排查（现象不明、需要先定位根因）用 `04_DebugFix/_DebugRecord.md` 模板，不用本条 |

---

## 10 开发进度审查

**使用场景**：怀疑代码实现与版本计划脱节时，做一次全面盘点。

**产出文件**：`{ai_dev}/02_DevPlanAndReport/v{版本}_DevReport.md`

**Prompt**：

```
以 docs/components/{component}/{component}_version_plan.md 为基准，
审查 {component} 的实际开发进度，输出到
docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{当前版本}_DevReport.md。

严格遵循 templates/ai_dev/02_DevPlanAndReport/_DevReport.md 的模板结构。

代码路径：
- 前端：src/frontend/packages/{component-kebab}/
- 后端：src/backend/{component}/

要求：
1. **只看代码里实际有什么**，不看文档写了什么。每条结论必须对应到具体文件路径。
2. 用四色标：🟢 已完成 / 🟡 部分完成 / 🔴 未实现 / ⚪ 计划外。
3. 按"层"组织而非按版本：前端页面 / API 端点 / 后端各层（model/enum/service/
   repository/handler/pkg/config/cmd）/ 数据库 / daemon / 数据迁移。
   断层通常出现在层与层之间。
4. 「总体版本进度摘要」后必须给出一段「核心发现」，点明最关键的断层。
5. 最后给出遗留问题清单，分 P1 阻塞 / P2 重要 / P3 维护 三档，
   每条给出建议动作和「工程师回复」列（留空）。
6. 结尾给出下一步行动建议，指明应该产出哪份 DevPlan。
```

---

## 11 绘制 Mermaid 图表

**使用场景**：需要为文档配套业务流程图、类图、ER 图、页面流转图、API 关系图。

**产出文件**：`{对应前缀}_{图表类型}.mmd` + 同名 `.png`（与所属文档同目录）

**Prompt**：

```
为 {component} 生成 {业务流程图 / 类图 / 数据库 ER 图 / 页面流转图 / API 关系图}，
输出到 {路径}/{component}_{business_process|class_diagram|db_schema|page_flow|api}.mmd。

严格遵循 templates/mermaid/ 下对应模板的样式规范：
1. 画布背景必须是白色（themeVariables 中 background: "#FFFFFF"）。
2. 所有文字颜色必须是黑色（textColor / primaryTextColor / classText 均为 "#000000"）。
3. 所有方框按**类型**归类，每个类型赋予不同的**浅色系背景色**，
   边框用同色系深色，字色统一 #000000。
   - flowchart / graph：用 classDef 定义分类，节点用 :::className 挂载。
   - classDiagram：**classDef / cssClass / ::: 全部无效**（不报错但被静默忽略），
     必须每个类写一条 `style {类名} fill:...,stroke:...,color:#000000`。
4. 图中必须包含图例（legend）子图说明每种颜色代表的类型；
   classDiagram 不支持 subgraph，改用文件顶部的配色注释块。
5. 内容必须与 {source_doc}.md §{n} 完全一致，不得自行增删节点。
6. 不要出现空的 `%%` 注释行（会导致解析失败）；
   YAML front-matter 中的 title 若含花括号必须加引号。

生成后同时导出 PNG 到同目录同名文件：
mmdc -i {文件}.mmd -o {文件}.png -b white -s 3
```

**使用说明**：

| # | 说明 |
|---|------|
| 1 | 具体的 init 配置、classDef 配色表与发布前检查清单见 `templates/mermaid/mmd_style_guide.md` |
| 2 | `.mmd` 是源文件，`.png` 是文档中引用的渲染结果，两者必须同步更新 |
| 3 | 文档中引用格式：`![xxx](./xxx.png)` + blockquote 注明 `> Source: [xxx.mmd](./xxx.mmd)` |
| 4 | 渲染务必带 `-s 3`：默认分辨率下窄框内的长标识符会被裁切（如 `CANCELED` 显示成 `CANCELE`） |

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {变更说明} |
