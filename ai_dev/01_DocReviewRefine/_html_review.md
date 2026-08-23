<!--
模板说明（使用时删除本注释块）

【文档层级】AI 开发过程文档。`01_DocReviewRefine` = **文档级** review 与 refine，
       与 `03_CodeReviewRefine` 的**代码级** review 相区分。
       注意：HTML 设计稿属于**设计文档**（不是源代码），因此审查报告归入本目录。

【用途】HTML UI 设计稿与 UI/UX 需求文档的双向对齐审查。
       逐页比对 `ui_page_design/P{nn}  {页面名}/code.html` 与 `{component}_uiux.md`
       的规格差异，产出按页面组织的问题清单，由工程师逐条决定"以谁为准"。

【本目录收什么】设计文档一致性审查、技术设计审查、前后端设计完备性审查、
       UI/UX HTML 稿审查、UI/UX 通用模式提炼。
       判定标准：审查对象是 **.md / .mmd / .html 设计稿** → 放本目录；
                 审查对象是**源代码** → 放 `03_CodeReviewRefine/`。

【落盘目录】
  - 平台级：docs/ai_dev_history/01_DocReviewRefine/
  - 组件级：docs/components/{component_code_name}/ai_dev_history/01_DocReviewRefine/

【文件命名】`{类型}_{YYYYMMDD}[-r{轮次}].md`，轮次跨日期**不重置**
       （例：docs_review_20260310-r6.md → docs_review_20260314-r7.md）。
  本目录各类型命名：
    docs_review_{YYYYMMDD}-r{n}.md                — 跨文档一致性审查
    tech_design_review_{YYYYMMDD}_r{n}.md         — 单文档深度审查
    design_completeness_review_{YYYYMMDD}-r{n}.md — 前后端设计完备性审查
    html_review_{YYYYMMDD}-r{n}.md                — HTML 稿对照 uiux 文档审查  ← **本文件**
    uiux_pattern_summary_{YYYYMMDD}-v{n}.md       — 通用 UI/UX 模式提炼提案

【工程师的三种处理方式】（见 templates/ai_dev/99_Prompts/_PromptLibrary.md §4）
  a. 以 HTML 为准，修改 uiux.md
  b. 以 uiux.md 为准，修改 HTML
  c. 按本报告中「工程师的答复」逐页修复对应的审查基准或审查对象，然后重新 review

【模板文件名】前导 `_` 只是模板标记。复制到 ai_dev_history/ 时按上面的规则重新命名，
       不保留下划线。

【Review 迭代协议】见 templates/ai_dev/readme.md：
  - 「工程师的答复」列 AI 生成时一律留空
  - 第 N 轮报告首章为上轮落实确认表，不是新问题
  - 已修复问题不再复述，且**不需要区分**问题是老的还是新的
    —— 工程师只关心还剩什么要处理
  - 问题 ID 全局唯一、永不复用
  - 架构分歧必须给出方案 A/B 并标注推荐项
-->

# {ComponentDisplayName} HTML Review 报告

> **文档标题**：{组件中文名}（{component}）HTML Review 报告
> **审查日期**：{YYYY-MM-DD}
> **审查基准**：`{component}_uiux.md` v{X.Y.Z}
> **审查对象**：`ui_page_design/P01–P{nn}` 目录下的 `code.html` 文件
> **上次审查文件名**：{上次 review 文件名，首次审查时填"无"}

---

注：工程师的答复中，UiUx.md 文件指的是审查基准 `{component}_uiux.md`，HTML 指的是审查对象。

---

## 审查范围

| Page ID | Page Name | HTML 路径 | 本轮是否审查 |
|---------|-----------|----------|-------------|
| {P01} | {页面中文名} | `ui_page_design/P01  {页面中文名}/code.html` | ✅ |
| {P02} | {页面中文名} | `ui_page_design/P02  {页面中文名}/code.html` | ✅ |
| {P03} | {页面中文名} | — | ⚪ {HTML 稿尚未产出} |

---

## {P01} {PageName}

| # | 问题 | UiUx.md 规范 | HTML 实际 | 严重性 | 工程师的答复 |
|---|------|-------------|----------|-------|------------|
| 1 | **{问题简述}** | {UiUx.md §3.x.3 中的规范描述} | {HTML 中的实际实现情况} | 🔴 | |
| 2 | **{问题简述}** | {规范描述} | {实际情况} | 🟠 | |
| 3 | **{问题简述}** | {规范描述} | {实际情况} | 🟡 | |
| 4 | **{问题简述}** | {规范描述} | {实际情况} | 🔵 | |

> 严重性说明：🔴 致命问题 | 🟠 高 | 🟡 中 | 🔵 低
> 「工程师的答复」列由工程师人工填写，AI 生成时一律留空。

---

## {P02} {PageName}

| # | 问题 | UiUx.md 规范 | HTML 实际 | 严重性 | 工程师的答复 |
|---|------|-------------|----------|-------|------------|
| 1 | **{问题简述}** | {规范描述} | {实际情况} | 🔴 | |
| 2 | **{问题简述}** | {规范描述} | {实际情况} | 🟡 | |

---

{按页面重复上述 "## {PageId} {PageName}" + 表格结构}

---

## 汇总

| 页面 | 🔴 致命 | 🟠 高 | 🟡 中 | 🔵 低 | 合计 |
|------|--------|-------|-------|-------|------|
| {P01} | {n} | {n} | {n} | {n} | {n} |
| {P02} | {n} | {n} | {n} | {n} | {n} |
| {P03} | {n} | {n} | {n} | {n} | {n} |
| **总计** | **{N}** | **{N}** | **{N}** | **{N}** | **{N}** |

### 共性问题

{列出跨页面重复出现的问题，这类问题通常意味着 uiux.md 缺少一条全局规则，
 或 HTML 生成时缺少统一的设计约束。每条注明涉及哪些页面。没有则删除本节。}

| # | 共性问题 | 涉及页面 | 建议处理 | 工程师的答复 |
|---|---------|---------|---------|-------------|
| C-01 | {问题描述} | {P01, P03, P05} | {在 uiux.md 增加全局规则章节 / 在 uiux_design_specification.md 增加平台级规范} | |
| C-02 | {问题描述} | {P02, P04} | {处理建议} | |

### 建议上升为平台级规范的模式

{若共性问题反映出的是可复用的通用设计模式，记录在此，并转入
 templates/ai_dev/01_DocReviewRefine/_uiux_pattern_summary.md 走正式的模式提炼流程。
 没有则删除本节。}

| # | 模式名称 | 出处页面 | 建议落位章节 |
|---|---------|---------|-------------|
| 1 | {模式名} | {P01/P03} | `uiux_design_specification.md` §{n} |
