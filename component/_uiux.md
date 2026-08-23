<!--
模板说明（使用时删除本注释块）

【文档层级】组件级（Component-level）UI/UX 设计文档。

【存放位置】docs/components/{component_code_name}/{component_code_name}_uiux.md
  - 文件名把前导 `_` 换成组件代码名，例如 `material_collector_uiux.md`。
  - 配套目录：docs/components/{component_code_name}/ui_page_design/
      ├── {component_code_name}_page_flow.mmd     # 页面流转图源文件
      ├── {component_code_name}_page_flow.png     # 页面流转图渲染结果
      ├── P01  {页面中文名}/
      │   ├── code.html                            # UI 设计稿（Stitch / 手写 HTML）
      │   └── screen.png                           # 设计稿截图
      └── P02  {页面中文名}/ ...

【与平台级文档的边界 —— 非常重要】
  - 平台级 docs/uiux_design_specification.md 定义**设计模式与全局 UI 规范**：
    页面设计模式（单列表页/双列表页/树状列表页/业务对象详情页/弹出框…）、
    通用 UI 控件（Multi Tags、Slider+Number Input、Autocomplete、Tag-style Time Input…）、
    配色、状态管理、数据格式化、Loading/空态、实时刷新、错误处理、命名规则等。
  - 本组件级文档**只描述本组件自己的页面**，并通过 `§x.x.x` 章节号引用平台规范。
    **不得重复定义**任何通用控件规范、配色规范、交互总则。
    若发现某个模式具有跨组件通用性，走 ai_dev/01_DocReviewRefine 的
    _uiux_pattern_summary（UI/UX 通用设计模式提炼）流程，讨论后上收到平台规范，本文只留引用。
  - 业务对象的字段语义以 {component_code_name}_business_desc.md 为准；
    API 端点与请求/响应结构以 {component_code_name}_tech_design.md §5 为准。
    本文只写"页面用到了哪些字段、调用了哪些端点"，不重复定义字段类型与响应体。

【文档约定】
  - Page Flow Diagram 放在 TOC **之前**（读者先看全局页面关系，再看目录）。
  - 所有 Dependent APIs / Page Behaviors 中的 API 一律写**完整 REST 端点路径**，
    禁止使用 `Object.Method` 式逻辑名（历史遗留写法，已在 docs_review R10 统一清除）。
  - 页面 ID 使用 `P{nn}` 两位编号，一经分配不再复用；页面迁移到其他组件时
    编号规则见 uiux_design_specification.md §18.2。
-->

# {组件中文名}（{component_code_name}） UI/UX Design

**Document Version**: {x.y.z}

---

## Page Flow Diagram

![Page Flow Diagram](./ui_page_design/{component_code_name}_page_flow.png)

> Source: [{component_code_name}_page_flow.mmd](./ui_page_design/{component_code_name}_page_flow.mmd)
>
> 绘制规范见 [templates/mermaid/mmd_style_guide.md](../../../templates/mermaid/mmd_style_guide.md)：
> 白色画布、按页面类型分类着色的浅色系方框、全部文字为黑色。

---

## 1 Table of Contents

- [1 Table of Contents](#1-table-of-contents)
- [2 Page Overview](#2-page-overview)
  - [2.1 Page List](#21-page-list)
  - [2.2 Navigation Menu](#22-navigation-menu)
  - [2.3 Page-Object-API Mapping](#23-page-object-api-mapping)
- [3 Page Details](#3-page-details)
  - [3.1 P01 {页面名1}](#31-p01-页面名1)
    - [3.1.1 UI Design](#311-ui-design)
    - [3.1.2 Page Business Functions](#312-page-business-functions)
    - [3.1.3 Page Layout and Elements](#313-page-layout-and-elements)
    - [3.1.4 Page Behaviors](#314-page-behaviors)
  - [3.2 P02 {页面名2}](#32-p02-页面名2)
    - [3.2.1 UI Design](#321-ui-design)
    - [3.2.2 Page Business Functions](#322-page-business-functions)
    - [3.2.3 Page Layout and Elements](#323-page-layout-and-elements)
    - [3.2.4 Page Behaviors](#324-page-behaviors)
- [4 路由定义](#4-路由定义)
- [5 Frontend Source Code Directory](#5-frontend-source-code-directory)
- [Change Log](#change-log)

---

## 2 Page Overview

### 2.1 Page List

| Page ID | Page Name | Layout Mode | Description |
|---------|-----------|-------------|-------------|
| P01 | {页面名1} | 双列表页 | {一句话说明本页面让用户完成什么} |
| P02 | {页面名2} | 只读详情页 | {一句话说明} |
| P03 | {页面名3} | 单列表页 | {一句话说明} |
| P04 | {页面名4} | 业务对象详情页 | {一句话说明}（⚠️ 暂不实现） |
| P05 | {页面名5} | Dashboard | {一句话说明} |
| P06 | {页面名6} | 树状双列表页 | {一句话说明} |
| P07 | API 调试页 | 双栏布局 | 为开发和调试提供快速验证本模块所有 API 的交互控制台 |

> **Layout Mode 取值**必须取自 [uiux_design_specification.md](../../uiux_design_specification.md) 中已定义的设计模式名，
> 不得自造名称：
>
> | Layout Mode | 规范章节 |
> |-------------|---------|
> | 单列表页 | §2.1.1 |
> | 双列表页 | §2.1.2 |
> | 树状单列表页 | §2.1.3 |
> | 树状双列表页 | §2.1.4 |
> | 业务对象详情页（创建/编辑） | §2.2 / §2.2.1 / §2.2.3 |
> | 只读详情页（业务对象预览或只读页） | §2.2.2 |
> | Dashboard | {规范章节或"本组件专用，无通用规范"} |
> | 双栏布局 | {规范章节或"本组件专用，无通用规范"} |
>
> **页面来源标注**：若页面从其他组件迁移而来，在 Description 中标注来源与 review 条目 ID，
> 例如"（迁移自 channel_management P01，R4-01）"。

### 2.2 Navigation Menu

{component_code_name} 的页面作为 unified_portal 侧边栏的一个菜单组呈现，菜单结构如下：

```
{组件中文名}
├── {菜单项1}       → P05
├── {菜单项2}       → P01
├── {菜单项3}       → P03
├── {菜单项4}       → P06
└── API 调试        → P07
```

默认进入"{默认菜单项}"（P{nn}）页面。

> - 只有可从菜单直接进入的页面才出现在菜单树中；详情页（P02/P04/P07 之类）由列表页跳转进入，不出现在菜单里。
> - 菜单项文案与 unified_portal 的导航菜单 API 返回值必须一致，见
>   [unified_portal_tech_design.md](../unified_portal/unified_portal_tech_design.md) 的导航菜单章节。

### 2.3 Page-Object-API Mapping

| Page ID | Page Name | Primary Business Objects | Dependent APIs |
|---------|-----------|------------------------|----------------|
| P01 | {页面名1} | {CObject1}, {CObject2} | GET /api/v1/{resource-a}, DELETE /api/v1/{resource-a}/{id}, POST /api/v1/{resource-a}/batch-delete, GET /api/v1/{resource-b} |
| P02 | {页面名2} | {CObject1} | GET /api/v1/{resource-a}/{id}, DELETE /api/v1/{resource-a}/{id} |
| P03 | {页面名3} | {CObject3} | GET /api/v1/{resource-c}, POST /api/v1/{resource-c}/{id}/{action} |
| P04 | {页面名4}（⚠️ 暂不实现） | {CObject3} | GET /api/v1/{resource-c}/{id} |
| P05 | {页面名5} | — | GET /api/v1/{resource-d}/status, GET /api/v1/stats/summary |
| P06 | {页面名6} | {CObject2} | GET /api/v1/{resource-b}, POST /api/v1/{resource-b}, PUT /api/v1/{resource-b}/{id}, DELETE /api/v1/{resource-b}/{id} |
| P07 | API 调试页 | — | 本模块所有 API 端点 |

> **API 说明**：所有页面的 Dependent APIs 均使用**完整 REST 端点路径**，对应
> [{component_code_name}_tech_design.md](./{component_code_name}_tech_design.md) §5 中的完整定义。
> 禁止在本表以及 §3.x.4 Page Behaviors 中使用 `Object.Method` 式逻辑名。
>
> **跨模块调用说明（{Rxx-yy}）**：{若本模块的 API 被其他模块调用，或本模块页面调用了其他模块的 API，
> 必须在此明确说明。例如："本模块的 `GET /api/v1/{resource-b}` 也被 `{other_component}` 跨模块调用：
> {other_component} P0x 页面在展示 {xxx} 列表 Tab 时调用该接口。依据
> [technical_overview.md §4.3](../../technical_overview.md) 条目 {n}，这些端点在 {other_component} 的
> API 调试页中以跨模块调用分组展示。"若无跨模块调用则写"本模块 API 不被其他模块调用，本模块页面也不调用其他模块 API。"}

---

## 3 Page Details

---

### 3.1 P01 {页面名1}

> **设计模式**：遵循 [uiux_design_specification.md](../../uiux_design_specification.md) §2.1.2 双列表页。
>
> **⚠️ 待完善（{Rxx-yy}）**：本页面的完整 UI/UX 设计规格（Page Layout and Elements）将由工程师在对应版本开发前进一步补充完善。
>
> {以上两条 blockquote 注记的用法：
>  - 第一条 **设计模式** 为必填，用于把页面锚定到平台规范的具体章节，避免重复描述通用交互。
>  - 第二条 **⚠️ 待完善** 为可选，仅当页面规格尚未定稿时保留，括号内填对应的 review 条目 ID，
>    定稿后连同标注一起删除。
>  - 若页面存在版本范围限制，再加一条：`> **版本范围**：本页面属于 v0.x，v0.y 之前不实现。`}

#### 3.1.1 UI Design

![P01 {页面名1} UI](./ui_page_design/P01  {页面名1}/screen.png)

> Source: [ui_page_design/P01  {页面名1}/code.html](./ui_page_design/P01  {页面名1}/code.html)

> {若尚无设计稿，将上面两行替换为：}
> UI mockup 将在后续版本中提供。

#### 3.1.2 Page Business Functions

本页面供{目标角色}{完成什么业务目标}。

| # | Function | Description |
|---|----------|-------------|
| 1 | {功能1} | {左侧选择 {上层对象}，右侧展示该 {上层对象} 下的 {下层对象} 列表} |
| 2 | {功能2} | {支持关键字搜索和日期范围筛选} |
| 3 | {功能3} | {默认按 {字段} 倒序；支持表头点击排序和特殊排序下拉} |
| 4 | {功能4} | {双击或点击"查看"按钮进入 {下层对象} 只读详情页（P02）} |
| 5 | {功能5} | {选中一条或多条记录后点击"删除"进行批量删除} |

#### 3.1.3 Page Layout and Elements

页面整体布局：**标题区域** → **左右双面板区域**。左右面板各自独立滚动，无主视图滚动条。

> 布局总述的其他常见写法：
> - 单列表页：页面整体布局：**标题区域** → **查询条件区域** → **功能按钮区域** → **列表区域** → **底部统计与翻页区域**。整页随用户滚动，header/footer 不使用 sticky/fixed 定位。
> - 详情页：页面整体布局：**标题区域（含返回/保存/取消按钮）** → **属性分组区域（Tab 或分节）** → **底部操作区域**。
> - Dashboard：页面整体布局：**标题区域** → **状态卡片行** → **统计卡片网格** → **最近事件列表**。

##### A. 左侧面板 — {上层对象}列表

> 遵循双列表页"上层对象列表区域"规范（[uiux_design_specification.md](../../uiux_design_specification.md) §2.1.2）。
> {上层对象}数据来自本模块的 `GET /api/v1/{resource-b}` API（传入 `{query_param}={value}` 仅展示 {过滤条件说明}）。

**A-1. 查询条件区域**

| # | Element | Control Type | Default Value | Description |
|---|---------|-------------|---------------|-------------|
| 1 | {关键字搜索} | Text Input | 空 | 按 {字段1} 或 {字段2} 模糊搜索 |
| 2 | {类型筛选} | Dropdown | 全部 | 选项：全部 / {ENUM_A} / {ENUM_B} |

- 每个控件 blur 或值变更时自动触发筛选（不需要搜索按钮）。
- 页面初次加载时，所有条件均为默认值，即不带任何过滤地展示全部{上层对象}。

**A-2. 功能按钮区域**

| Position | Element | Type | 前置条件 | Behavior |
|----------|---------|------|----------|----------|
| 右侧 | 刷新 | Icon Button（🔄） | — | 重新从 API 加载{上层对象}列表 |

> {说明本面板为何不提供某些按钮，例如："P01 专注于{下层对象}浏览，{上层对象}的增删改查由本模块的 P06 负责，此处不重复提供。"}

**A-3. 列表区域**

列表为 **1 列**，采用折行显示：

| Row | Content | Style | Description |
|-----|---------|-------|-------------|
| 第 1 行 | {字段1} | 14px，粗体，黑色 | {说明} |
| 第 2 行 | {字段2} 标签 + {字段3} 标签 | 12px，浅色字体，Tag 徽章 | {说明；条件显示规则} |
| 第 3 行 | {字段4} + {字段5} | 11px，灰色字体 | 显示格式：`{fmt}`，例如 `120 days / 35 daily` |

- 排序：按 {字段} 字母升序排列，**不支持用户排序**（配置类数据，参照树状单列表页规范）。
- 底部仅提供 **上一页** / **下一页** 和 **当前页码**，不显示总记录数与已选记录数。
- 每页 {50} 条记录。
- 默认选中列表的**第一条**记录，右侧面板联动加载。

**A-4. 列表行交互**

| Interaction | Behavior |
|-------------|----------|
| 单击 | 选中该{上层对象}；右侧面板自动清空所有查询条件，重置日期范围为"{默认值}"，并按新选中的{上层对象}刷新数据列表 |
| Ctrl/Shift + 单击 | 多选；右侧面板按**第一个**被选中的{上层对象}刷新 |
| 双击 | 进入该{上层对象}的只读详情页 P{nn} |
| 右键 | 显示右键菜单，菜单项与功能按钮区域一致 |

---

##### B. 右侧面板 — {下层对象}列表

> 遵循双列表页"下层对象列表区域"规范（即标准单列表页，但列数 ≤ 5）。

**隐含查询条件**：当前左侧被选中的第一个{上层对象}的 `{key_field}`。所有查询请求均携带此条件。

> **"隐含查询条件"写法说明**：双列表页的下层列表必然带有一个不在查询条件区域展示、
> 但每次请求都必须携带的父级过滤条件。必须在此显式写出字段名，否则工程师无法确定请求参数。

**B-1. 查询条件区域**

| # | Element | Control Type | Default Value | Description |
|---|---------|-------------|---------------|-------------|
| 1 | 关键字 | Text Input | 空 | 按 {字段1} 和 {字段2} 全文搜索（大小写不敏感） |
| 2 | 日期范围 | Calendar + Dropdown | {当天} | 筛选 `{date_field}` 字段。采用 uiux_design_specification.md 中的**方式二**：日历控件 + 快捷下拉选项（当天 / 最近 3 天 / 最近 1 周 / 最近 1 月） |
| 3 | {状态筛选} | Dropdown | 全部 | 选项：全部 / {ENUM_X} / {ENUM_Y} |

- 每个控件 blur 或值变更时自动触发筛选。
- 页面初次加载时，日期范围为"{当天}"，其他条件为空/默认，自动执行一次带日期条件的查询。

**B-2. 功能按钮区域**

| Position | Element | Type | 前置条件 | Behavior |
|----------|---------|------|----------|----------|
| 左侧（与按钮间有分割线） | 排序方式 | Dropdown | — | 选项：默认排序（`{field} DESC`）/ {排序2}↓ / {排序3}↓。选择后立即向后端 API 发出重新查询请求 |
| 右侧 | 查看 | Text Button | 必须已选择记录 | 进入第一个被选中记录的只读详情页 P02 |
| 右侧 | 创建 | Text Button（{配色见 uiux_design_specification.md §5.2}） | — | 进入创建页 P{nn} |
| 右侧 | 编辑 | Text Button | 必须已选择**恰好一条**记录 | 进入该记录的编辑页 P{nn} |
| 右侧 | 删除 | Text Button（红色） | 必须已选择记录 | 弹出确认对话框"是否确认删除选中的 n 条记录？"；确认后执行删除，取消则关闭对话框并保持选中状态 |

> {说明本页面不提供的按钮及原因，例如："本页面不提供'创建'和'编辑'按钮。{下层对象}由 Daemon 程序自动采集生成，不支持手动创建或编辑。"}

**B-3. 列表区域**

列表包含 **{5}** 列：

| # | Column Header | Data Source | Width | Style / Format | Sortable |
|---|--------------|------------|-------|----------------|----------|
| 1 | ID | `{id_field}` | 60px | 右对齐整数，**不加 `#` 前缀** | 是（列头点击） |
| 2 | {列2} | `{field2}` + `{field2_sub}` | flex | **第 1 行**：{field2}，黑色 14px，最多 2 行截断；**第 2 行**：{field2_sub}，灰色 12px，1 行截断；若为空则显示灰色斜体 "{占位文案}"；**第 3 行**：{tags} Tag 徽章，12px，相同名称相同配色（见 uiux_design_specification.md §3.1.2） | 是（按 {field2} 排序） |
| 3 | {列3} | `{field3}` | 80px | Tag 徽章显示（如 `{示例值}`），居中 | 是 |
| 4 | {列4} | `{field4}` | 120px | **已处理**：绿色 ✓ 图标 + 日期（`yyyy-MM-dd`）；**未处理**：灰色 "—" | 是 |
| 5 | {列5} | `{field5}` | 160px | `yyyy-MM-dd HH:mm` 格式（见 uiux_design_specification.md §9 数据格式化规则） | 是 |

- 默认排序：按 **`{field5}` 倒序**。
- 排序规则：点击表头在正序、倒序之间切换，同时显示小箭头指示方向。不支持多列排序。所有排序均通过**后端 API** 实现（不做前端本地排序）。
- 底部显示：`Selected {n} / Total {m}` | **翻页控件**。每页 {50} 条记录，不提供"每页条数"选择器。
- {软删除/非活跃行样式：遵循 uiux_design_specification.md §3.2.1 / §3.2.2。}

**B-4. 列表行交互**

| Interaction | Behavior |
|-------------|----------|
| 单击 | 选中该记录 |
| Ctrl/Shift + 单击 | 多选记录 |
| 双击 | 进入该记录的只读详情页 P02 |
| 右键 | 显示右键菜单，菜单项与功能按钮区域一致（查看、删除） |

> **选中行视觉反馈**：本页面右侧列表**不使用复选框列**，通过单击 / Ctrl / Shift 单击高亮整行来表示选中状态
> （与 P{nn} 使用复选框列的选中方式不同）。选中行以蓝色背景高亮显示，多选时所有被选中行均高亮。
> "被选中记录数"在底部统计区实时更新。
>
> **写法说明**：同一组件内若不同页面采用了不同的选中方式（复选框 vs 整行高亮），
> 必须在两处都显式标注差异，否则 HTML review 会反复报同一个不一致问题。

#### 3.1.4 Page Behaviors

| # | Trigger | Action | Result |
|---|---------|--------|--------|
| 1 | 页面首次加载 | 调用 `GET /api/v1/{resource-b}?{filter}=false&is_active=true&sort_by={field}&sort_order=asc` | 左侧面板填充{上层对象}列表，自动选中第一个 |
| 2 | 左侧{上层对象}被选中（首次或切换） | 清空右侧查询条件，日期范围重置为"{当天}"；调用 `GET /api/v1/{resource-a}/{key_field}`（带日期范围查询参数） | 右侧面板刷新{下层对象}列表 |
| 3 | 右侧查询条件变更 | 调用 `GET /api/v1/{resource-a}/{key_field}`（带所有当前查询条件） | 右侧列表刷新 |
| 4 | 右侧排序方式变更 | 调用 `GET /api/v1/{resource-a}/{key_field}?sort_by={field}&sort_order={asc\|desc}` | 右侧列表按新排序刷新 |
| 5 | 双击记录 / 点击"查看" | 导航至 P02 页面，传递 `{id_field}` 和 `{key_field}` | 进入只读详情页 |
| 6 | 点击"删除" | 弹出阻塞确认对话框（见 uiux_design_specification.md §2.3.2）；用户确认后调用 `POST /api/v1/{resource-a}/{key_field}/batch-delete`（选中单条时数组含 1 个元素） | 成功则刷新右侧列表；失败则显示错误提示并保持选中状态 |
| 7 | 从 P02 返回（退出或删除成功） | 刷新右侧列表，保持当前左侧选中的{上层对象} | 列表更新 |
| 8 | 左侧"刷新"按钮点击 | 重新调用 `GET /api/v1/{resource-b}?...` | 左侧列表刷新，尝试保持当前选中项 |
| 9 | API 返回错误 | 按 uiux_design_specification.md §16 错误处理展示 | 显示错误提示，页面保持原状态 |

> **Action 列写法要求**：一律写**具体 REST 端点 + 查询参数**（含关键参数名与取值），
> 不得写"调用查询 API"这类模糊描述——工程师依据本表直接编码，模糊描述会导致 review 返工。

---

### 3.2 P02 {页面名2}

> **设计模式**：遵循 [uiux_design_specification.md](../../uiux_design_specification.md) §2.2.2 业务对象预览或只读页。

#### 3.2.1 UI Design

![P02 {页面名2} UI](./ui_page_design/P02  {页面名2}/screen.png)

> Source: [ui_page_design/P02  {页面名2}/code.html](./ui_page_design/P02  {页面名2}/code.html)

#### 3.2.2 Page Business Functions

| # | Function | Description |
|---|----------|-------------|
| 1 | {查看完整信息} | {展示 {CObject} 的全部属性，只读} |
| 2 | {删除记录} | {点击"删除"按钮删除当前记录并返回列表页} |

#### 3.2.3 Page Layout and Elements

页面整体布局：**标题区域（含"退出"/"删除"按钮）** → **属性分组区域** → {**内容折叠展开区域**}。

**A. 标题与功能按钮区域**

| Position | Element | Type | 前置条件 | Behavior |
|----------|---------|------|----------|----------|
| 左侧 | 页面标题 | Text | — | 显示 `{对象名} — {当前记录标识}` |
| 右侧 | 编辑 | Text Button | {条件} | 切换到编辑模式 / 进入 P{nn} |
| 右侧 | 删除 | Text Button（红色） | — | 弹出确认对话框；确认后调用 `DELETE /api/v1/{resource-a}/{id}` |
| 右侧 | 退出 | Text Button | — | 返回来源列表页 P01 |

**B. 属性分组区域**

| # | 分组 | 字段 | 控件 / 展示形式 | 说明 |
|---|------|------|----------------|------|
| 1 | 基本信息 | `{field1}`, `{field2}` | 只读文本 | {说明} |
| 2 | {分组2} | `{field3}` | Tag Cloud 只读展示（§3.1.3） | {说明} |
| 3 | {分组3} | `{field4}` | Slider + Number Input 只读（§3.3，只读模式仅显示数字） | {说明} |
| 4 | 时间信息 | `created_at`, `updated_at`, `deleted_at` | 只读文本，`yyyy-MM-dd HH:mm` | {说明} |

**C. {长文本/媒体资源区域}**

{说明折叠展开规则、媒体预览方式、超长文本截断规则。}

#### 3.2.4 Page Behaviors

| # | Trigger | Action | Result |
|---|---------|--------|--------|
| 1 | 页面加载 | 调用 `GET /api/v1/{resource-a}/{key_field}/{id}` | 填充所有属性 |
| 2 | 点击"删除" | 弹出确认对话框；确认后调用 `DELETE /api/v1/{resource-a}/{key_field}/{id}` | 成功则返回 P01 并刷新列表；失败则显示错误提示 |
| 3 | 点击"退出" | 返回 P01 | 列表保持原有查询条件与选中项 |
| 4 | 记录不存在（404） | 按 uiux_design_specification.md §16 展示 | 显示"记录不存在"并提供返回列表按钮 |

---

> **重复规则**：按页面数量重复 `### 3.x P{nn} {页面名}` 小节。每节固定四个子节：
>
> - `3.x.1 UI Design`（screen.png + code.html）
> - `3.x.2 Page Business Functions`（编号功能表）
> - `3.x.3 Page Layout and Elements`（按面板/区域分块，列表页固定 A-1～A-4 四块）
> - `3.x.4 Page Behaviors`（编号 Trigger/Action/Result 表，Action 写完整端点）
>
> 不同 Layout Mode 的 3.x.3 结构差异：
>
> | Layout Mode | 3.x.3 分块 |
> |-------------|-----------|
> | 单列表页 | A-1 查询条件 / A-2 功能按钮 / A-3 列表区域 / A-4 列表行交互 |
> | 双列表页 | A. 左侧面板（A-1～A-4）+ B. 右侧面板（B-1～B-4，含隐含查询条件） |
> | 树状（双）列表页 | A. 树面板（节点层级、展开规则、节点图标）+ B. 右侧列表面板（B-1～B-4） |
> | 业务对象详情页 | A. 标题与功能按钮 / B. 属性分组（创建/只读/编辑三模式差异表） / C. 校验规则 |
> | 只读详情页 | A. 标题与功能按钮 / B. 属性分组 / C. 长文本与媒体区域 |
> | Dashboard | A. 状态卡片 / B. 统计卡片 / C. 最近事件列表 / D. 自动刷新策略（§10） |
> | 双栏布局（API 调试页） | A. 左栏端点树（按 tech_design §5 分组，含跨模块调用分组）/ B. 右栏请求构造与响应展示 |

---

## 4 路由定义

| # | Route | Page | 组件文件 | 说明 |
|---|-------|------|---------|------|
| 1 | `/{component-code-name}/{page-a}` | P01 {页面名1} | `pages/{page-a}/index.tsx` | {说明} |
| 2 | `/{component-code-name}/{page-a}/:{id_field}` | P02 {页面名2} | `pages/{page-a}-detail/index.tsx` | {说明} |
| 3 | `/{component-code-name}/{page-b}` | P03 {页面名3} | `pages/{page-b}/index.tsx` | {说明} |
| 4 | `/{component-code-name}/{page-c}/new` | P{nn} {创建页} | `pages/{page-c}-detail/index.tsx` | 创建模式 |
| 5 | `/{component-code-name}/{page-c}/:{id_field}` | P{nn} {详情页} | `pages/{page-c}-detail/index.tsx` | 只读模式 |
| 6 | `/{component-code-name}/{page-c}/:{id_field}/edit` | P{nn} {编辑页} | `pages/{page-c}-detail/index.tsx` | 编辑模式 |
| 7 | `/{component-code-name}/api-debug` | P07 API 调试页 | `pages/api-debug/index.tsx` | 开发调试用 |

> **版本范围注记**：{说明本表中哪些路由属于哪个版本，例如："v0.1 实现 P01–P0x 的路由（共 n 条）；
> P07 API 调试页属于 v0.2；P0y 属于 v0.z。"标注必须与
> [{component_code_name}_version_plan.md](./{component_code_name}_version_plan.md) 一致。}
>
> - 路由路径使用 kebab-case，与前端包目录命名规则一致（见 [naming_convention.md](../../naming_convention.md)）。
> - 详情页的创建 / 只读 / 编辑三模式**复用同一个组件文件**，通过路由后缀区分模式
>   （见 uiux_design_specification.md §2.2）。

---

## 5 Frontend Source Code Directory

```
src/frontend/packages/{component-code-name}/
├── src/
│   ├── pages/                       # 页面组件，一个页面一个目录
│   │   ├── {page-a}/index.tsx       # P01
│   │   ├── {page-a}-detail/index.tsx# P02
│   │   └── api-debug/index.tsx      # P07
│   ├── components/                  # 本组件私有的可复用组件
│   ├── models/                      # TypeScript 类型定义（C/E/I 前缀，见 naming_convention.md §3）
│   ├── services/                    # API 客户端封装（api.ts），页面内禁止直接发请求
│   ├── hooks/                       # 数据钩子层
│   ├── stores/                      # 状态管理（one-store-per-entity + uiStore，见规范 §8）
│   ├── mock/                        # v0.1 硬编码数据；v0.2 起仅用于测试
│   ├── routes.tsx                   # 路由注册，与 §4 一一对应
│   └── index.ts                     # 包导出
└── package.json
```

> 目录使用 **kebab-case**（`src/frontend/` 规则）；文件命名规则见
> [uiux_design_specification.md](../../uiux_design_specification.md) §18 与
> [naming_convention.md](../../naming_convention.md)。

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| {x.y.z} | {YYYY-MM-DD} | {变更说明；若源于 review，标注条目 ID，如"修复 R7-04：补充 P01–P05 待完善标注"} |
| {x.y.z} | {YYYY-MM-DD} | {变更说明} |

> Change Log 按**倒序**排列（最新在上）。每次因 review 修改文档都必须新增一行并标注 review 条目 ID，
> 便于 ai_dev/01_DocReviewRefine 的下一轮 review 验证落实情况。
