# Mermaid 绘图规范（mmd_style_guide.md）

本文件是 `templates/mermaid/` 目录的说明文档，定义本项目**所有** `.mmd` 图表的统一样式、
配色、命名与落盘规则。目录中以 `_` 开头的 `.mmd` 文件为可复制的图表模板。

---

## 1 为什么必须统一样式

1. **图会被渲染成 PNG 嵌入 Markdown 文档**。文档最终会被打印、导出 PDF、贴进评审材料，
   Mermaid 默认主题（`default` / `dark`）在白底文档中会出现灰底色块、浅灰文字，
   打印后几乎不可读。
2. **AI 与人类需要靠颜色快速分类**。同一张图里"角色 / 页面 / 接口 / 数据表 / 外部系统"
   混排时，只有稳定的配色约定才能让读者一眼分辨节点类型。
3. **跨模块图表要能拼在一起看**。各模块的类图、API 图、页面流转图使用同一套配色后，
   跨模块对照时不会因为"同一种东西两种颜色"而误判。

因此本项目对所有 `.mmd` 强制三条硬规则：

> **① 白色画布　② 所有方框按类型归类，每类一个浅色系背景色　③ 所有文字为黑色**

---

## 2 标准 init 块（直接复制）

每个 `.mmd` 文件的第一行有效内容必须是 `%%{init: ...}%%` 指令块。

### 2.1 流程图 / 架构图 / 页面流转图 / API 图（`graph` / `flowchart`）

```
%%{init: {
  "theme": "base",
  "themeVariables": {
    "background": "#FFFFFF",
    "primaryColor": "#E3F2FD",
    "primaryTextColor": "#000000",
    "primaryBorderColor": "#1565C0",
    "secondaryColor": "#E8F5E9",
    "tertiaryColor": "#FFF9C4",
    "lineColor": "#555555",
    "textColor": "#000000",
    "titleColor": "#000000",
    "clusterBkg": "#FAFAFA",
    "clusterBorder": "#CCCCCC",
    "edgeLabelBackground": "#FFFFFF",
    "fontFamily": "Arial, \"Microsoft YaHei\", sans-serif",
    "fontSize": "14px"
  }
}}%%
```

### 2.2 类图 / 数据库结构图（`classDiagram`）

```
%%{init: {
  "theme": "base",
  "themeVariables": {
    "background": "#FFFFFF",
    "primaryColor": "#E3F2FD",
    "primaryTextColor": "#000000",
    "primaryBorderColor": "#1565C0",
    "lineColor": "#333333",
    "textColor": "#000000",
    "titleColor": "#000000",
    "classText": "#000000",
    "clusterBkg": "#FAFAFA",
    "clusterBorder": "#CCCCCC",
    "edgeLabelBackground": "#FFFFFF",
    "fontFamily": "Arial, \"Microsoft YaHei\", sans-serif",
    "fontSize": "13px"
  }
}}%%
```

> `classText` 只对 `classDiagram` 生效，用于强制类成员文字为黑色；
> `titleColor` 用于 `--- title: ... ---` front-matter 标题。

### 2.3 着色机制按图型不同（⚠ 极易踩坑，已实测验证）

**同一套配色，两种图型要用完全不同的写法。**

| 图型 | 生效的着色写法 | 会被静默忽略的写法 |
|------|--------------|------------------|
| `graph` / `flowchart` | `classDef xxxCls fill:...` + 节点后缀 `:::xxxCls` | — |
| `classDiagram` | **每个类一条 `style 类名 fill:...,stroke:...,color:#000000`** | `classDef`、`cssClass "A,B" cls`、`class A:::cls` |

在 mermaid v11.16 上实测：`classDiagram` 中写 `classDef` / `cssClass` / `:::`
**不会报任何错误**，但完全不起作用，渲染结果是所有类框都是同一个主题色 —— 
这正是"看起来写了归类却没有归类"的典型陷阱。

`classDiagram` 必须逐个类写 `style`，示例（对 `namespace` 内部的类同样生效，已验证）：

```
classDiagram
    direction TB

    namespace main_db {
        class main_object {
            <<Table>>
            +INT_PK object_id
        }
    }
    class ref_table {
        +INT_PK ref_id
    }

    %% ----- tableCls 常规业务表：浅蓝 -----
    style main_object fill:#E3F2FD,stroke:#1565C0,color:#000000
    %% ----- refTableCls 跨库引用表：浅灰 -----
    style ref_table   fill:#F5F5F5,stroke:#616161,color:#000000
```

> 写法虽然啰嗦，但换来的是"每个类都必须显式归类"——漏掉一个类，它就会是默认蓝色，
> 一眼可见，反而更容易在 review 中发现遗漏。

### 2.4 两个会导致渲染直接失败的语法陷阱（已实测）

| # | 陷阱 | 现象 | 正确写法 |
|---|------|------|---------|
| 1 | YAML front-matter 的 `title` 以 `{` 开头 | `YAMLException` 渲染失败（`{` 在 YAML 中会被当作 flow mapping 起始） | 给标题加引号：`title: "{组件中文名} — 业务流程"` |
| 2 | 存在**空的** `%%` 注释行 | `graph` / `flowchart` 报 `Parse error ... %%%%flowchart TD`（mermaid 的注释清理正则要求 `%%` 后至少有一个字符，空 `%%` 行会残留并粘连） | 空行写成 `%% -`，或直接删掉该行 |

> front-matter（`--- title: ... ---`）必须位于文件**最开头**，其前不得有任何注释或空行。

---

## 3 标准配色表

### 3.1 全局调色板（9 种浅色）

所有图表只能从这 9 种浅色中取值，不得自创颜色。`fill` 为方框背景，`stroke` 为同色系描边。

| 代号 | 色名 | fill | stroke |
|------|------|---------|---------|
| `BLUE` | 浅蓝 | `#E3F2FD` | `#1565C0` |
| `CYAN` | 浅青 | `#E0F7FA` | `#00838F` |
| `GREEN` | 浅绿 | `#E8F5E9` | `#2E7D32` |
| `YELLOW` | 浅黄 | `#FFF9C4` | `#F9A825` |
| `ORANGE` | 浅橙 | `#FFF3E0` | `#EF6C00` |
| `CORAL` | 浅珊瑚 | `#FBE9E7` | `#D84315` |
| `PURPLE` | 浅紫 | `#F3E5F5` | `#6A1B9A` |
| `PINK` | 浅粉 | `#FADBD8` | `#C0392B` |
| `GREY` | 浅灰 | `#F5F5F5` | `#616161` |

### 3.2 分类 → 颜色映射（权威表）

| 分类名（classDef 名） | 适用节点类型 | 调色 | fill | stroke | 文字 |
|----------------------|-------------|------|---------|---------|------|
| `actorCls` | 人 / 角色 / 参与者（业务流程图） | BLUE | `#E3F2FD` | `#1565C0` | `#000000` |
| `pageCls` | 前端页面 / UI 视图（API 图） | CYAN | `#E0F7FA` | `#00838F` | `#000000` |
| `apiCls` | REST 端点 / 接口 | GREEN | `#E8F5E9` | `#2E7D32` | `#000000` |
| `decisionCls` | 判定 / 分支节点（菱形） | YELLOW | `#FFF9C4` | `#F9A825` | `#000000` |
| `menuCls` | 菜单 / 入口 / 网关 | ORANGE | `#FFF3E0` | `#EF6C00` | `#000000` |
| `debugCls` | 调试页 / 工具类页面 | CORAL | `#FBE9E7` | `#D84315` | `#000000` |
| `serviceCls` | 后端服务 / daemon / 共享服务 | PURPLE | `#F3E5F5` | `#6A1B9A` | `#000000` |
| `dataCls` | 数据库 / 数据表 / 存储 | PINK | `#FADBD8` | `#C0392B` | `#000000` |
| `externalCls` | 外部系统 / 第三方 / 跨模块引用 | GREY | `#F5F5F5` | `#616161` | `#000000` |

### 3.3 各图型的分类别名

不同图型沿用同一调色板，只是 `classDef` 名按语义改写：

| 图型 | 分类名 → 调色 |
|------|--------------|
| 业务流程图 `_business_process.mmd` | `roleACls`→BLUE、`roleBCls`→PURPLE、`roleCCls`→GREEN、`systemCls`→ORANGE、`decisionCls`→YELLOW、`dataCls`→PINK |
| 技术架构图 `_technical_architecture.mmd` | `clientCls`→BLUE、`gatewayCls`→ORANGE、`portalCls`→CYAN、`componentCls`→GREEN、`serviceCls`→PURPLE、`dataCls`→PINK、`externalCls`→GREY |
| 类图 `_class_diagram.mmd` | `baseCls`→BLUE、`entityCls`→GREEN、`valueCls`→CYAN、`enumCls`→YELLOW、`refCls`→GREY |
| 数据库图 `_db_schema.mmd` | `tableCls`→BLUE、`templateCls`→ORANGE、`lookupCls`→YELLOW、`refTableCls`→GREY |
| API 图 `_api_diagram.mmd` | `pageCls`→CYAN、`apiCls`→GREEN、`extCls`→GREY |
| 页面流转图 `_page_flow.mmd` | `menuCls`→ORANGE、`listCls`→BLUE、`detailCls`→GREEN、`dashCls`→PURPLE、`debugCls`→CORAL |

### 3.4 容器（subgraph）底色

`subgraph` 的底色必须比其中节点**更浅**，否则节点无法从容器上"浮起来"。
统一使用下列近白色调，并在 `style` 语句中同样写明 `color:#000`：

| 容器语义 | fill | stroke |
|---------|---------|---------|
| 蓝系分组 | `#F5FAFE` | `#1565C0` |
| 青系分组 | `#F2FBFC` | `#00838F` |
| 绿系分组 | `#F5FCF7` | `#2E7D32` |
| 黄系分组 | `#FFFDF2` | `#F9A825` |
| 橙系分组 | `#FFFAF3` | `#EF6C00` |
| 紫系分组 | `#FBF6FD` | `#6A1B9A` |
| 粉系分组 | `#FEF7F6` | `#C0392B` |
| 中性分组 | `#FAFAFA` | `#CCCCCC` |
| **图例 legend** | `#F8F9FA` | `#CCCCCC` |

---

## 4 图例（legend）

除类图外，**每张图都必须带图例**。写法固定为：

```
    subgraph legend["图例"]
        direction LR
        LG1["角色 A"]:::roleACls
        LG2["角色 B"]:::roleBCls
    end
    style legend fill:#F8F9FA,stroke:#CCCCCC,color:#000
```

图例节点 ID 统一用 `LG1`、`LG2`… 前缀，避免与业务节点冲突。
类图（`classDiagram`）不支持 `subgraph`，改为在文件顶部用 `%%` 注释块说明配色含义。

---

## 5 命名与落盘约定

### 5.1 文件命名

```
{component_code_name}_{diagram_type}.mmd
{component_code_name}_{diagram_type}.png     # 与 .mmd 同名同目录
```

| diagram_type | 模板 | 存放目录 |
|-------------|------|---------|
| `business_process` | `_business_process.mmd` | `docs/components/{component}/` |
| `class_diagram` | `_class_diagram.mmd` | `docs/components/{component}/` |
| `db_schema` | `_db_schema.mmd` | `docs/components/{component}/` |
| `api` | `_api_diagram.mmd` | `docs/components/{component}/` |
| `page_flow` | `_page_flow.mmd` | `docs/components/{component}/ui_page_design/` |

平台级图表不带组件前缀，直接放在 `docs/` 根目录：
`business_process.mmd`、`technical_architecture.mmd`。

> **注意**：`page_flow` 是唯一放在 `ui_page_design/` 子目录下的图，
> 因为它属于 UI 设计资产，与各页面的 `code.html` / `screen.png` 同级管理。

### 5.2 在文档中引用

Markdown 中统一按"图 + 源文件链接"两行写：

```markdown
![{图标题}](./{component_code_name}_class_diagram.png)

> 源文件：[{component_code_name}_class_diagram.mmd](./{component_code_name}_class_diagram.mmd)
```

### 5.3 渲染成 PNG

使用 Mermaid CLI（`@mermaid-js/mermaid-cli`）：

```bash
# 一次性安装
npm install -g @mermaid-js/mermaid-cli

# 单个文件：-b white 强制白底，-s 3 输出 3 倍分辨率便于打印
mmdc -i material_collector_class_diagram.mmd \
     -o material_collector_class_diagram.png \
     -b white -s 3

# 批量渲染当前目录下所有 mmd
for f in *.mmd; do mmdc -i "$f" -o "${f%.mmd}.png" -b white -s 3; done
```

> `-b white` 与 init 块中的 `"background": "#FFFFFF"` 是**双保险**，两者都要保留：
> 前者控制导出画布，后者控制主题内部的底色计算。

### 5.4 使用模板

1. 从本目录复制对应的 `_{diagram_type}.mmd`。
2. 重命名为 `{component_code_name}_{diagram_type}.mmd` 并移入目标目录。
3. 删除文件顶部的 `%% ===== 模板说明 =====` 注释块。
4. 替换所有 `{花括号占位符}`；节点 ID（如 `A1`、`P01`、`SM_LIST`）可按需改名，
   但必须保持 `classDef` 归类不丢失。
5. 渲染 PNG，与 `.mmd` 同名同目录提交。

---

## 6 发布前硬性检查清单

提交 `.mmd` 与 `.png` 之前，逐条自查：

| # | 检查项 | 通过标准 |
|---|--------|---------|
| 1 | **白底** | init 块含 `"background": "#FFFFFF"`；渲染命令带 `-b white` |
| 2 | **全黑字** | `textColor` / `primaryTextColor` / `titleColor`（类图另加 `classText`）均为 `#000000` |
| 3 | **每条着色语句带 color** | 每条 `classDef` / `style` 语句结尾都有 `,color:#000000` |
| 4 | **每个节点都归类** | `graph`/`flowchart`：不存在没有 `:::xxxCls` 的节点；`classDiagram`：每个类都有一条对应的 `style` 语句 |
| 4b | **着色写法与图型匹配** | `classDiagram` 中没有残留的 `classDef` / `cssClass` / `:::`（它们不生效，见 §2.3） |
| 4c | **渲染后肉眼验证配色** | 打开 PNG 确认各类节点确实是不同颜色，而不是一片默认蓝（漏归类的唯一可靠发现方式） |
| 5 | **配色取自调色板** | 所有 fill / stroke 均出现在 §3.1 的 9 色表中 |
| 6 | **无深色 fill** | 不存在 `fill:#333`、`fill:#4A90D9` 之类的深色块（浅色 fill 的亮度都在 `#E0` 以上） |
| 7 | **容器比节点浅** | 每条 `style {subgraph} fill:` 取自 §3.4，且带 `color:#000` |
| 8 | **有图例** | 非类图必须含 `subgraph legend["图例"]`；类图必须含顶部配色注释块 |
| 9 | **占位符已替换** | 全文搜索 `{` 无残留模板占位符；类名 / 表名 / namespace 名中**不含**花括号 |
| 9b | **无空 `%%` 行** | `grep -c '^%%$' x.mmd` 结果为 0（见 §2.4） |
| 10 | **PNG 已同步** | `.png` 与 `.mmd` 同名同目录，且是本次修改后重新渲染的 |
| 11 | **文档已引用** | 对应 `.md` 中的图片链接与源文件链接路径正确 |
| 12 | **文字无截断** | 默认 `-s 1` 渲染时窄框内的长标识符（如枚举值 `CANCELED`）可能被裁成 `CANCELE`。始终带 `-s 3` 渲染，并在 PNG 中确认最长的那条文字完整可见 |

---

## Change Log

| 版本 | 日期 | 变更内容 |
|------|------|---------|
| 1.0.0 | {YYYY-MM-DD} | 首版：确立白底黑字 + 9 色浅色调色板 + 分类映射表 + 6 个图表模板 |
