<!--
模板说明（使用时删除本注释块）

【文档层级】AI 开发过程文档。`01_DocReviewRefine` = **文档级** review 与 refine，
       与 `03_CodeReviewRefine` 的**代码级** review 相区分。
       本文件属于其中的 "refine" 一侧：不是找问题，而是把组件级设计沉淀为平台级规范。

【用途】从**单个组件**的 UI/UX 设计中提炼可复用的通用设计模式，
       供工程师决定是否上升为**平台级**规范写入 `docs/uiux_design_specification.md`。
       这是组件级设计 → 平台级规范的唯一正式通道。

【为什么需要】第一个做完 UI/UX 的组件会自然产生大量设计决策（控件样式、列表交互、
       配色规则、状态表现）。这些决策若不提炼，后续组件会各写各的，规范失效。

【本目录收什么】设计文档一致性审查、技术设计审查、前后端设计完备性审查、
       UI/UX HTML 稿审查、UI/UX 通用模式提炼。
       判定标准：审查对象是 **.md / .mmd / .html 设计稿** → 放本目录；
                 审查对象是**源代码** → 放 `03_CodeReviewRefine/`。

【落盘目录】
  - 平台级：docs/ai_dev_history/01_DocReviewRefine/
  - 组件级：docs/components/{component_code_name}/ai_dev_history/01_DocReviewRefine/
       （文档在组件目录下，但结论落到平台级 docs/uiux_design_specification.md）

【文件命名】`{类型}_{YYYYMMDD}[-r{轮次}].md`，轮次跨日期**不重置**
       （例：docs_review_20260310-r6.md → docs_review_20260314-r7.md）。
  本目录各类型命名：
    docs_review_{YYYYMMDD}-r{n}.md                — 跨文档一致性审查
    tech_design_review_{YYYYMMDD}_r{n}.md         — 单文档深度审查
    design_completeness_review_{YYYYMMDD}-r{n}.md — 前后端设计完备性审查
    html_review_{YYYYMMDD}-r{n}.md                — HTML 稿对照 uiux 文档审查
    uiux_pattern_summary_{YYYYMMDD}-v{n}.md       — 通用 UI/UX 模式提炼提案  ← **本文件**

【模板文件名】前导 `_` 只是模板标记。复制到 ai_dev_history/ 时按上面的规则重新命名，
       不保留下划线。

【Review 迭代协议】见 templates/ai_dev/readme.md：
  - 「工程师的答复」列 AI 生成时一律留空，
    由工程师逐条决定"加入 / 不加入 / 再议"
  - 第 N 轮报告首章为上轮落实确认表，不是新问题
  - 已修复问题不再复述，且不区分新老问题
  - 问题 ID 全局唯一、永不复用
  - 架构分歧必须给出方案 A/B 并标注推荐项
-->

# {ComponentDisplayName} UiUx 通用设计模式总结

**日期**：{YYYY-MM-DD}
**来源**：`{component}_uiux.md` v{x.y.z}
**目的**：从 `{component}` 模块中提取可能适用于其他模块的通用 UI/UX 设计模式，供讨论后决定是否纳入 `uiux_design_specification.md`。
**当前平台规范版本**：`uiux_design_specification.md` v{x.y.z}

---

## 已纳入 uiux_design_specification.md 的设计模式

以下设计模式已在本次更新中写入全局设计规范（v{x.y.z}），不再列入待讨论范围：

1. **{模式名称1}** → §{n.n.n}
2. **{模式名称2}** → §{n.n.n}
3. **{模式名称3}** → §{n.n.n}
4. **{模式名称4}** → §{n.n.n}
5. **{模式名称5}** → §{n.n.n}

> 已纳入的模式，组件级 uiux.md 中应改为引用平台规范章节号，删除重复描述。

---

## 待讨论的通用设计模式

以下设计模式在 `{component}` 模块中已经使用，具有一定通用性，建议讨论是否纳入全局规范。

| # | 模式名称 | 出处 | 描述 | 通用性评估 | 工程师的答复 |
|---|---------|------|------|-----------|------------|
| 1 | **{模式名称}** | {P02/P04/P06 的 {字段名} 字段} | {模式的完整描述：控件长什么样、交互怎么触发、只读态如何表现、适用于什么类型的字段} | {高——任何有范围约束的数值字段均可使用} | |
| 2 | **{模式名称}** | {P01/P03/P05 全局规则} | {描述} | {高——适用于所有列表页} | |
| 3 | **{模式名称}** | {全局规则} | {描述} | {中——只适用于列表类页面} | |
| 4 | **{模式名称}** | {P07} | {描述} | {低——与本模块业务强绑定，建议保留在组件级} | |

> 「工程师的答复」列由工程师人工填写，AI 生成时一律留空。
> 答复取值示例："加入到 uiux_design_specification.md" / "保留在组件级，不上升" / "再议"。
> 通用性评估取值：**高**（多数模块适用）/ **中**（同类页面适用）/ **低**（本模块专用）。

---

## 建议的落位章节

对评估为"高"的模式，预先规划在 `uiux_design_specification.md` 中的落位，便于工程师判断规范结构是否合理：

| 模式 | 建议落位 | 归属大章 |
|------|---------|---------|
| {模式名称1} | §{n.n} {章节标题} | {§2 功能页设计模式 / §3 常用 UI 控件和样式 / §5 前端配色} |
| {模式名称2} | §{n.n} {章节标题} | {大章} |

---

## 说明

1. 本文档只做**提炼与建议**，不直接修改 `uiux_design_specification.md`。工程师答复后，由 AI 按答复执行写入，并升级平台规范的 Change Log 版本号。
2. 模式被纳入平台规范后，来源组件的 `{component}_uiux.md` 必须同步改为引用规范章节号（形如"遵循 [uiux_design_specification.md](../../uiux_design_specification.md) §{n.n.n}"），删除本地重复描述，避免两处定义漂移。
3. 评估为"低"的模式保留在组件级文档中，但仍应记录在本文，供后续组件出现相似需求时检索复用。
4. 本文档每个组件完成 UI/UX 设计后产出一次；后续组件产出时，应先检索已有的 pattern summary，避免重复提炼同一模式。
