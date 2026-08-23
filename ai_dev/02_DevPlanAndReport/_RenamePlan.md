<!--
模板说明（使用时删除本注释块）

【文档层级】AI 开发过程文档 —— `02_DevPlanAndReport`

【本目录收什么】开发计划、开发报告、保存现场、AI handover、改进计划、
       命名规范化与目录整理计划。即"计划做什么"和"实际做成什么"这两类文档。

【落盘目录】
  - 平台级：docs/ai_dev_history/02_DevPlanAndReport/
  - 组件级：docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/
  改名往往横跨 docs/ 和 src/ 的多个组件，通常放**平台级**目录，一次性统一规划、
  统一执行，不能各组件各改各的。

【文件命名 —— 本目录的硬规则】所有文档一律以**所在版本号开头**：
       `v{版本号}_{DocType}[_{YYYYMMDD}].md`
  - v0.3_DevPlan.md / v0.3_BE_DevPlan.md / v0.3_FE_DevPlan.md / v0.3_DevReport.md
  - v0.3_Snapshot.md / v0.3_Handover.md / v0.3_ImprovePlan.md
  - v0.3_RenamePlan.md / v0.3_ReorganizePlan.md
  - 同一版本内同类型有多份时追加日期后缀：v0.3_RenamePlan_20260308.md
  - 版本号取自 {component}_version_plan.md；本文这类跨版本的整理计划取**发起时的
    当前版本号**（平台级则取发起时进度最靠前的组件版本号，并在文首注明取值依据）

【本文件的定位】目录与文件**命名规范化执行计划**。当 naming_convention.md 确立或修订了
       命名规则后，用本模板批量盘点现存的不合规路径，逐条列出「当前路径 → 目标路径」
       并跟踪执行状态。

【与同目录其它文档的区别】
  - v{x}_DevPlan.md      —— 改的是**功能**（新增代码、实现端点）。
  - 本文件（RenamePlan） —— 改的是**路径本身**，不动任何功能逻辑。
  - v{x}_ReorganizePlan.md —— 只移动文件位置，不改文件名；本文只改名，不换目录层级。
    两者常常配合使用，但应分成两份文档、分两步执行，便于回滚。

【执行注意】
  - Git 会自动识别为 rename，历史记录保留完整；
  - 改名后必须扫描全仓库更新文档内引用（第四章），否则大量链接失效；
  - 前端目录改名会影响 monorepo workspace 配置与 import 路径。

【人工填写列】「执行状态」由执行方逐条回填；AI 生成计划时可留空或标 🔴 未开始。

【模板文件名】前导 `_` 只是模板标记，复制时按上面的命名规则重命名。
-->

# v{版本号} 目录和文件命名规范化计划

> **文档标题**：目录和文件命名规范化执行计划
> **版本**：v{版本号}
> **创建日期**：{YYYY-MM-DD}
> **规则**：{`docs/` 和 `src/backend/` 使用 **snake_case**；`src/frontend/` 使用 **kebab-case**；`ui_page_design/` 子目录自由格式}
> **规则依据**：[naming_convention.md](../../naming_convention.md) §{n}、[technical_overview.md](../../technical_overview.md) §{n}
> **状态**：{🟡 执行中 / 🟢 已完成}

---

## 一、前端目录改名（{snake_case → kebab-case}）

> 范围：`src/frontend/packages/` 下所有{含下划线的目录}

| # | 类型 | 当前路径 | 目标路径 | 执行状态 |
|---|------|---------|---------|--------|
| FD-01 | 目录 | `src/frontend/packages/{old_name}/` | `src/frontend/packages/{new-name}/` | |
| FD-02 | 目录 | `src/frontend/packages/{old_name}/` | `src/frontend/packages/{new-name}/` | |
| FD-03 | 目录 | `src/frontend/packages/{old_name}/` | `src/frontend/packages/{new-name}/` | |

> 注：{`shared/` 和 `{single_word}/` 为单个单词，snake_case 和 kebab-case 相同，无需改名。}
> 「执行状态」列取值：✅ 已完成 / 🟡 进行中 / 🔴 未开始 / ⚪ 放弃（附原因）。

---

## 二、文档文件改名（{kebab-case → snake_case}）

> 范围：{`docs/` 下所有含连字符的文件}

| # | 类型 | 当前路径 | 目标路径 | 执行状态 |
|---|------|---------|---------|--------|
| DF-01 | 文件 | `docs/{old-name}.md` | `docs/{new_name}.md` | |
| DF-02 | 文件 | `docs/components/{component}/{old-name}.md` | `docs/components/{component}/{new_name}.md` | |

---

## 三、文档文件改名（{混合大小写 → snake_case}）

> 范围：{`docs/` 下所有含大写字母的文件名}

| # | 类型 | 当前路径 | 目标路径 | 执行状态 |
|---|------|---------|---------|--------|
| DC-01 | 文件 | `docs/components/{component}/{Component}_UiUx.md` | `docs/components/{component}/{component}_uiux.md` | |
| DC-02 | 文件 | `docs/{OldName}.md` | `docs/{new_name}.md` | |

---

## 四、文档内引用更新

> 改名会破坏 Markdown 链接和文档正文中的路径引用。本章列出所有需要同步更新的引用点。
> **必须在改名后立即执行**，否则大量链接失效。

| # | 引用所在文件 | 引用内容 | 更新为 | 执行状态 |
|---|-------------|---------|--------|--------|
| RF-01 | `docs/{file}.md` §{n} | `{old path}` | `{new path}` | |
| RF-02 | `docs/components/{component}/{file}.md` §{n} | `{old path}` | `{new path}` | |
| RF-03 | `README.md` | `{old path}` | `{new path}` | |
| RF-04 | `project_directory_plan.md` §{n} | `{old path}` | `{new path}` | |

**扫描方式**：

```bash
# 扫描所有对旧路径的引用
grep -rn "{old_name}" --include="*.md" .
grep -rn "{old_name}" --include="*.ts" --include="*.tsx" src/frontend/
grep -rn "{old_name}" --include="*.go" src/backend/
```

---

## 五、配置与代码引用更新

> 目录改名会影响构建配置和代码 import 路径。

| # | 文件 | 更新内容 | 执行状态 |
|---|------|---------|--------|
| CF-01 | `{monorepo workspace 配置}` | {包路径由 {old} 改为 {new}} | |
| CF-02 | `{tsconfig paths / 别名配置}` | {路径映射更新} | |
| CF-03 | `{CI/CD 配置}` | {构建路径更新} | |
| CF-04 | `{Dockerfile / 部署脚本}` | {路径更新} | |
| CF-05 | {源码 import 语句} | {全局替换 import 路径} | |

---

## 汇总

| 类别 | 条目数 | ✅ 已完成 | 🟡 进行中 | 🔴 未开始 |
|------|-------|----------|----------|----------|
| 前端目录改名（FD） | {n} | {n} | {n} | {n} |
| 文档文件改名（DF） | {n} | {n} | {n} | {n} |
| 文档文件改名（DC） | {n} | {n} | {n} | {n} |
| 文档内引用更新（RF） | {n} | {n} | {n} | {n} |
| 配置与代码引用（CF） | {n} | {n} | {n} | {n} |
| **总计** | **{N}** | **{N}** | **{N}** | **{N}** |

---

## 验证

| # | 验证项 | 方法 | 结果 |
|---|--------|------|------|
| 1 | {无残留旧路径} | `grep -rn "{old_name}" .` | {🟢 无匹配} |
| 2 | {前端构建通过} | `{build 命令}` | {🟢 通过} |
| 3 | {后端构建通过} | `{build 命令}` | {🟢 通过} |
| 4 | {文档链接有效} | {逐个点击 README 与 overview 中的链接} | {🟢 全部有效} |
| 5 | {Git 识别为 rename} | `git status` | {🟢 显示 renamed 而非 delete+add} |

---

## 影响范围与风险

| # | 风险 | 影响 | 缓解措施 |
|---|------|------|---------|
| 1 | {历史文档中的路径引用失效} | {ai_dev_history 中的旧报告链接指向不存在的路径} | {历史归档文档不做更新，仅在本计划中记录映射关系供追溯} |
| 2 | {外部书签/链接失效} | {影响} | {措施} |
| 3 | {并行开发分支冲突} | {其他分支的改动会与改名冲突} | {改名前合并所有在途分支，改名后统一 rebase} |

---

## 旧路径 → 新路径 映射总表

> 供后续追溯历史文档中的旧路径。改名完成后此表长期保留。

| 旧路径 | 新路径 |
|--------|--------|
| `{old path}` | `{new path}` |
| `{old path}` | `{new path}` |
