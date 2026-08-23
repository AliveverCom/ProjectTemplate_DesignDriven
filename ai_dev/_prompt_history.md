<!--
模板说明（使用时删除本注释块）

【文档层级】AI 开发过程文档。**放在 `ai_dev_history/` 根目录，不进任何编号子目录。**

【落盘路径】
  - 平台级：docs/ai_dev_history/prompt_history.md
  - 组件级：docs/components/{component_code_name}/ai_dev_history/prompt_history.md
  - 公共库：docs/common_lib/ai_dev_history/prompt_history.md

【模板文件名】前导 `_` 只是模板标记，复制时**去掉** `_`。
  落盘就叫 `prompt_history.md`，**不加组件名前缀**
  （它已经位于组件自己的 ai_dev_history/ 下，再加前缀是冗余）。

【为什么在根目录，而不进 01_/02_/03_/04_ 任何一个】
  编号子目录对应开发生命周期的**各个阶段**，每个阶段会产出多份带日期或版本号的文档。
  本文件不属于任何单一阶段——它是**贯穿整个开发周期的单一累积文件**：
  每个组件（或平台）**各一份，只追加、不新建、不按阶段拆分**。
  把它放在根目录，是为了让任何阶段的 AI 都能一眼找到"工程师到底原话说了什么"。

【与 99_Prompts/_PromptLibrary.md 的区别】务必分清，两者不可互相替代：

  | 维度 | 本文件 prompt_history.md | 99_Prompts/prompt_library.md |
  |------|-------------------------|------------------------------|
  | 性质 | **历史归档** | **可复用模板** |
  | 内容 | 真实发生过的原始 prompt，逐字原样 | 抽象过的标准 prompt，去掉了一次性上下文 |
  | 时间 | 带时间戳，倒序累积 | 无时间戳，按用途分类 |
  | 数量 | 每组件一份 | 全平台一份 |
  | 用途 | 回答"这份文档为什么长这样" | 回答"下次做同类事该发什么 prompt" |
  | 变更 | 只追加，历史条目**永不修改** | 产出不理想就**修订**该条 |

  流转关系：一条历史 prompt 被反复使用、证明有效后，应**抽象后补进 PromptLibrary**；
  PromptLibrary 中的条目不回写本文件。

【记什么 —— 不必记录每一条 prompt】只收录以下三类：
  1. 用户为某份文档提供了**大段实质性描述**（数行以上的规格描述）；
  2. 用户**明确要求**归档的 prompt；
  3. **改变了设计方向或推翻既有决策**的 prompt。
  日常的"改一下这里""继续""按你的建议改"之类的对话**不收录**。

【Content Type 取值】见正文中的取值表，只能从中选取。

【排序】**倒序**（最新在最上），因为最近的意图查阅频率最高。
-->

# {ComponentName} Prompt History

<!-- 平台级使用时，标题写作：# Prompt History -->

**Document Version**: {x.y.z}

> This file preserves raw prompts provided by developers during the design and development
> process, making the development process traceable. It does NOT need to contain every prompt —
> only those where a user explicitly provides a substantial description for a specific document,
> or when the user explicitly requests it, or when the prompt changed a design decision.
>
> 本文件保存开发过程中工程师提供的**原始 Prompt 原文**，使"文档为什么长这样"可追溯。
> 文档正文是 AI 加工后的结果，原始意图只保存在这里。
> 不需要收录每一条 Prompt——只收录工程师针对某份文档给出大段描述性需求的、
> 工程师明确要求记录的、或改变了设计方向的 Prompt。

---

## Prompt Index

| # | DateTime | User | Content Type | Title | 关联文档 |
|---|----------|------|--------------|-------|---------|
| {3} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title} | {该 prompt 产出或修改的文件路径} |
| {2} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title} | {该 prompt 产出或修改的文件路径} |
| {1} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title} | {该 prompt 产出或修改的文件路径} |

> **关联文档** 列填该 prompt 直接**产出或修改**的文件（可多个，用 `、` 分隔），
> 使用相对本文件的路径或相对仓库根的路径均可，但同一文件内保持一致。
> 例：`../{component}_uiux.md`、`02_DevPlanAndReport/v0.3_BE_DevPlan.md`。
> 若该 prompt 未直接产出文件（如仅确认了一个决策），填 `—` 并在 Title 中说明。

**Content Type 取值**（只能从下表中选取）：

| 取值 | 适用场景 | 典型关联文档 |
|------|---------|-------------|
| `business_desc` | 业务描述文档相关的需求描述 | `{component}_business_desc.md` |
| `tech_design` | 技术设计文档相关的需求描述 | `{component}_tech_design.md` |
| `uiux` | UI/UX 需求文档相关的需求描述 | `{component}_uiux.md` |
| `ui_page_design` | 页面设计稿（HTML / Stitch）相关的描述 | `ui_page_design/P{nn}  {页面名}/code.html` |
| `version_plan` | 版本规划、版本范围划分相关的描述 | `{component}_version_plan.md` |
| `dev_plan` | 开发计划、实现方案相关的描述 | `02_DevPlanAndReport/v{版本}_{BE\|FE}_DevPlan.md` |
| `dev_report` | 开发报告、进度审查相关的描述 | `02_DevPlanAndReport/v{版本}_DevReport.md` |
| `doc_review` | 文档审查要求、审查报告格式、对文档审查结论的答复 | `01_DocReviewRefine/docs_review_{YYYYMMDD}-r{n}.md` |
| `code_review` | 代码审查要求、对代码审查结论的答复 | `03_CodeReviewRefine/{BE\|FE}_code_review_{YYYYMMDD}-r{n}.md` |
| `debug` | 缺陷排查、联调、修复相关的描述 | `04_DebugFix/debug_{主题}_{YYYYMMDD}-r{n}.md` |
| `handover` | 保存现场、AI 之间交接相关的指令 | `02_DevPlanAndReport/v{版本}_Handover.md`、`v{版本}_Snapshot.md` |
| `other` | 以上都不属于的其他内容（如命名规范决策、图表绘制要求、目录整理） | {视情况填写} |

---

## Prompts

### # {3} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title}

**关联文档**：{该 prompt 产出或修改的文件路径}

{Paste the original raw prompt content here. 原样粘贴，不做任何改写、精简或格式化。}

---

### # {2} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title}

**关联文档**：{该 prompt 产出或修改的文件路径}

{Paste the original raw prompt content here.}

---

### # {1} | {YYYY-MM-DD HH:MM} | {UserName} | {content_type} | {Short title}

**关联文档**：{该 prompt 产出或修改的文件路径}

{Paste the original raw prompt content here.}

---

> Prompts are listed in **reverse chronological order** (newest first).
> Prompt 按**倒序**排列（最新的在最上面）。
>
> To add a new prompt / 追加新 Prompt 的步骤：
>
> 1. **在 Prompt Index 表的最上方插入一行**，`#` 取当前最大值 +1，并填写「关联文档」列。
>    Insert a new row at the **top** of the Prompt Index table; `#` = current max + 1.
>    编号**只增不减**：即使中间某条被判定为不必保留，也不回收其编号、不重排既有编号。
> 2. **在 `## Prompts` 标题的正下方插入新的 Prompt 小节**（即新条目排在最前面）。
>    Add a new section at the **top** of the Prompts area, directly below the `## Prompts` heading.
> 3. 每个 Prompt 小节遵循以下格式 / Each prompt section follows this format：
>    - **首行**：`### # {n} | {DateTime} | {User} | {Content Type} | {Short title}` —— 与索引表行完全一致。
>      **First line**: matching the index row exactly.
>    - **第二行**：`**关联文档**：{路径}`。
>    - **正文**：原始 Prompt 内容，**逐字原样**。不做摘要、不重排版、不改错别字、不翻译。
>      **Body**: the raw prompt content, **verbatim**. Do not summarize, reformat, or fix typos.
>    - **结尾**：一条分隔线 `---` 加空行，然后是下一条 Prompt。
>      **End**: a horizontal rule (`---`) followed by a blank line, then the next prompt.
> 4. **既有条目一律不修改。** 若某条 prompt 的决策后来被推翻，不要改动原条目，
>    而是把推翻它的新 prompt 作为新条目追加在最前面，并在新条目的 Title 中注明
>    "推翻 #{n}"。历史的价值在于它是历史。
> 5. 追加后更新文件顶部的 `**Document Version**`。
>    Update `**Document Version**` at the top when adding prompts.
