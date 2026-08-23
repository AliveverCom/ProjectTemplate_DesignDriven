<!--
模板说明（使用时删除本注释块）

【文档层级】AI 开发过程文档 —— 02_DevPlanAndReport

【本目录收什么】
  开发计划（DevPlan / BE_DevPlan / FE_DevPlan）、开发报告（DevReport）、
  保存现场文档（Snapshot）、AI 之间的交接文档（Handover）、改进计划（ImprovePlan）、
  命名规范化与目录整理计划（RenamePlan / ReorganizePlan）。

【落盘目录】
  平台级：docs/ai_dev_history/02_DevPlanAndReport/
  组件级：docs/components/{component_code_name}/ai_dev_history/02_DevPlanAndReport/

【文件命名 —— 本目录的硬规则】
  所有文档一律以**所在版本号**开头：v{版本号}_{DocType}[_{YYYYMMDD}].md
    v0.3_DevPlan.md        — 该版本的总体开发计划
    v0.3_BE_DevPlan.md     — 后端专项开发计划
    v0.3_FE_DevPlan.md     — 前端专项开发计划（本模板）
    v0.3_DevReport.md      — 该版本的开发报告 / 进度审查
    v0.3_Snapshot.md       — 保存现场
    v0.3_Handover.md       — AI 之间交接
    v0.3_ImprovePlan.md    — 改进计划
  同一版本内同类型有多份时，追加日期后缀：v0.3_FE_DevPlan_20260326.md
  跨版本补齐时并列版本号：v0.1_v0.2_FE_DevPlan.md
  版本号取自组件的 {component}_version_plan.md；
  跨版本的整理类计划（RenamePlan / ReorganizePlan）取**发起时的当前版本号**。

【三份开发计划模板的分工】
  _DevPlan.md = **版本总纲**。面向一次完整的 AI 编码 session，是交给执行者的
      作战简报，偏"开工前必须知道什么"：读什么（带 🔴必读 / 🟠重要 / 🟡参考
      优先级）、红线是什么、当前代码到哪了、Phase 划分、产出文件清单、
      路由速查、技术版本锁定、给执行者的提示。前后端同版本一并完成时用它。
  _BE_DevPlan.md = **后端专项计划**。关联设计文档索引（本组件 / 平台规范 /
      公共库 / 上一版本代码 四类）、技术栈与架构约束、目标目录结构、
      数据库脚本规划、按 Phase 的开发任务、API 端点路由表、开发注意事项、
      完成状态 Review。
  _FE_DevPlan.md（本文件）= **前端专项计划**。背景与动机、关联文档索引、
      技术方案（架构变更 / 新增目录 / API 对接端点清单）、Phase 任务、
      路由注册最终状态、文件变更汇总、验证方法。

【与 _BE_DevPlan.md 的结构差异】
  - 前端计划通常由一份**开发报告（DevReport）**驱动（发现断层 → 补齐），
    故第 0 章是「背景与动机」而非「文档索引」；文档索引降为第 1 章。
  - 增加「路由注册最终状态」和「文件变更汇总」两章，
    因为前端的产出以文件和路由为单位验收。

【权威范围依据】
  开发计划只能实现 {component}_version_plan.md 已定义的版本范围。
  超出范围必须先回去修订 version_plan，不得在计划里自行扩张。

【配套文档】
  计划 → 执行 → v{版本}_DevReport.md 回填完成状态；
  中途换 session / 换模型 → v{版本}_Handover.md 交接；
  中断需保存上下文 → v{版本}_Snapshot.md。

【模板文件名】
  前导 `_` 只是模板标记，复制时按上面的命名规则重命名。
-->

# {ComponentDisplayName} 前端开发计划 — v{版本} {版本代号}

**Document**: v{目标版本}_FE_DevPlan.md
**Date**: {YYYY-MM-DD}
**Component**: {component}
**Target**: {本次目标，例如：v0.1 收尾（P04 采集任务详情页）+ v0.2 前端 API 对接层完整实现}
**Developer**: AI ({模型名称})
**Prerequisite**: {前置条件，例如：v0.1 前端 6/7 页面已完成（v0.1_FE_DevPlan.md）；v0.3 后端已完成（v0.3_BE_DevPlan.md）}
**Status**: {🟡 进行中 / 🟢 全部完成（后补计划，记录本次 session 的实际开发内容） / ⚪ 待启动}

---

## 0 背景与动机

{说明本计划的驱动来源。若由开发报告驱动，引用该报告并摘出结论。}

基于 `{驱动文档，如 v{版本}_DevReport.md}` 的 Review 结论，当前模块存在以下断层：

| # | 问题 | 严重度 | 说明 |
|---|------|--------|------|
| 1 | {P{nn} 页面缺失} | **P1 阻塞** | {v0.1 计划 {n} 条路由，实际仅 {n} 条；{关联页面的入口按钮被禁用}} |
| 2 | {前端未接入后端 API} | **P1 断层** | {后端已完整实现至 v{n}（真实数据库），前端仍停留在 v{n} Mock 数据阶段} |
| 3 | {`services/` API 客户端目录缺失} | **P2** | {v{n} 里程碑要求，前端无任何 HTTP 请求封装} |
| 4 | {P{nn} API 调试页缺失} | **P2** | {v{n} 里程碑要求，便于开发调试后端} |

本计划目标：{一次性补齐 v{n} 遗留项并完成 v{n} 前端层全部工作，使前后端完整联通。}

---

## 1 关联文档索引

> **⚠️ 开发前必读**：以下文件包含本次前端开发所需的全部规格与参考实现。

| # | 文件路径 | 说明 |
|---|---------|------|
| 1 | `docs/components/{component}/ai_dev_history/02_DevPlanAndReport/v{版本}_DevReport.md` | 本次开发的驱动文档——{开发报告 / 进度审查结论} |
| 2 | `docs/components/{component}/{component}_uiux.md` | **全文** — 页面清单（§2.1）、导航菜单（§2.2）、Page-Object-API 映射（§2.3）、逐页设计（§3）、路由定义（§4） |
| 3 | `docs/components/{component}/{component}_tech_design.md` §5 | API 端点定义（共 {n} 个端点，本次前端对接 {n} 个） |
| 4 | `docs/uiux_design_specification.md` | 平台级 UI/UX 规范：页面设计模式、控件样式、配色、状态管理、数据格式化 |
| 5 | `docs/naming_convention.md` | 类型命名（C/E/I 前缀）、文件命名（kebab-case） |
| 6 | `src/backend/{component}/internal/handler/router.go` | 后端路由注册（确认端点路径和参数） |
| 7 | `src/backend/{component}/pkg/response.go` | 统一响应结构 `{ApiResponse<T>}` + `{PaginatedData<T>}` |
| 8 | `src/frontend/packages/{参考组件}/src/services/api.ts` | 参考模式——已有组件的 API 客户端封装 |
| 9 | `src/frontend/packages/{参考组件}/src/pages/ApiDebug/index.tsx` | 参考模式——已有组件的 API 调试页 |
| 10 | `docs/components/{component}/ui_page_design/P{nn}  {页面名}/code.html` | 页面 HTML 设计稿（布局与 mock 数据来源） |

---

## 2 技术方案

### 2.1 架构变更概要

| 变更 | v{当前版本} 状态 | v{目标版本} 目标 |
|------|-----------------|-----------------|
| 数据来源 | {`mock/` 目录硬编码} | {`services/` 调用后端 REST API} |
| 请求封装 | {无} | {统一 `apiClient` + 拦截器 + 错误处理} |
| 数据获取 | {组件内直接引用 mock 常量} | {`hooks/use{Resource}.ts` 数据钩子} |
| 状态管理 | {组件本地 state} | {{状态库} store，one-store-per-entity} |
| 类型定义 | {部分缺失} | {`models/` 覆盖全部 API 响应类型} |
| 调试能力 | {无} | {P{nn} API 调试页覆盖本模块全部端点} |

### 2.2 新增目录结构

```
src/frontend/packages/{component-kebab-case}/src/
├── services/                       # 【新增】API 客户端层
│   ├── apiClient.ts                # axios/fetch 实例 + 拦截器 + 统一错误处理
│   ├── {resource}Api.ts            # 各资源的端点封装
│   └── index.ts
├── hooks/                          # 【新增】数据钩子层
│   ├── use{Resource}List.ts
│   ├── use{Resource}Detail.ts
│   └── index.ts
├── models/                         # 【补充】TypeScript 类型定义
│   ├── {resource}.ts               # C{Resource} 接口
│   ├── enums.ts                    # E{Enum} 枚举
│   └── api.ts                      # ApiResponse<T> / PaginatedData<T>
├── pages/
│   ├── {page-kebab-case}/          # 【新增】缺失页面
│   │   └── index.tsx
│   └── api-debug/                  # 【新增】API 调试页
│       └── index.tsx
├── stores/                         # 【改造】接入真实数据
└── routes.tsx                      # 【改造】注册新增路由
```

> 目录名使用 **kebab-case**（前端规范），与后端 snake_case 不同。

### 2.3 API 对接端点清单

> 本表是前后端联调的验收清单。端点必须与 `{component}_tech_design.md` §5 和后端 `router.go` 完全一致。

| # | Method | Endpoint | 前端封装函数 | 消费页面 | tech_design | 状态 |
|---|--------|----------|-------------|---------|-------------|------|
| 1 | GET | `/api/v1/{resource}` | `{resource}Api.list()` | {P01, P06} | §5.1.1 | |
| 2 | GET | `/api/v1/{resource}/:{id}` | `{resource}Api.get()` | {P02, P07} | §5.1.2 | |
| 3 | POST | `/api/v1/{resource}` | `{resource}Api.create()` | {P07} | §5.1.3 | |
| 4 | PUT | `/api/v1/{resource}/:{id}` | `{resource}Api.update()` | {P07} | §5.1.4 | |
| 5 | DELETE | `/api/v1/{resource}/:{id}` | `{resource}Api.remove()` | {P06, P07} | §5.1.5 | |
| 6 | POST | `/api/v1/{resource}/batch-delete` | `{resource}Api.batchDelete()` | {P01} | §5.2.4 | |

**本次对接端点数**：{n} / 后端总端点数 {n}。{未对接的端点及原因：{说明}。}

---

## 3 开发任务清单

> Phase 之间串行，Phase 内可并行。「状态」列开发过程中回填。

### Phase 1：{阶段名，如 v0.1 收尾 — P04 页面补齐}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 1.1 | {创建 P{nn} 页面组件} | `pages/{page-kebab}/index.tsx` | {按 uiux §3.x 实现布局与元素；数据先用 mock} | |
| 1.2 | {注册路由} | `routes.tsx` | {路径 `{/path/:id}`，与 uiux §4 一致} | |
| 1.3 | {打通入口} | `pages/{列表页}/index.tsx` | {启用此前被禁用的"查看"按钮与双击行为} | |

### Phase 2：{阶段名，如 API 客户端层}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 2.1 | {创建 apiClient} | `services/apiClient.ts` | {baseURL 来自环境变量；响应拦截器解包 `data`；错误统一转换} | |
| 2.2 | {封装 {resource} 端点} | `services/{resource}Api.ts` | {按 §2.3 清单逐个实现，参数与类型严格对齐 tech_design} | |
| 2.3 | {定义响应类型} | `models/api.ts` | {`ApiResponse<T>`、`PaginatedData<T>`} | |

### Phase 3：{阶段名，如 数据钩子层}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 3.1 | {列表钩子} | `hooks/use{Resource}List.ts` | {封装分页、排序、筛选参数与 loading/error 状态} | |
| 3.2 | {详情钩子} | `hooks/use{Resource}Detail.ts` | {按 id 拉取，含 404 处理} | |

### Phase 4：{阶段名，如 页面迁移（Mock → API）}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 4.1 | {P01 接入 API} | `pages/{page}/index.tsx` | {移除 mock 引用，改用 hooks；筛选/排序改为后端参数} | |
| 4.2 | {P02 接入 API} | `pages/{page}/index.tsx` | {同上} | |

### Phase 5：{阶段名，如 API 调试页}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 5.1 | {创建 API 调试页} | `pages/api-debug/index.tsx` | {按 uiux §3.x 双栏布局；左侧端点分组树，右侧参数表单 + 响应展示} | |
| 5.2 | {端点清单配置} | `pages/api-debug/endpoints.ts` | {覆盖本模块全部 {n} 个端点，含跨模块调用分组} | |

### Phase 6：{阶段名，如 模型补充}

| # | 任务 | 产出文件 | 说明 | 状态 |
|---|------|---------|------|------|
| 6.1 | {补齐类型定义} | `models/{resource}.ts` | {字段与 tech_design §2 类定义逐一对齐，含可空性} | |
| 6.2 | {补齐枚举} | `models/enums.ts` | {E 前缀，取值与后端一致} | |

---

## 4 路由注册最终状态

> 本表是 `routes.tsx` 的验收清单，必须与 `{component}_uiux.md` §4 路由定义完全一致。

| # | 路径 | 页面组件 | Page ID | 参数 | 菜单入口 | 状态 |
|---|------|---------|---------|------|---------|------|
| 1 | `{/component/resource}` | `{ResourceListPage}` | {P01} | — | {菜单：xxx} | |
| 2 | `{/component/resource/:id}` | `{ResourceDetailPage}` | {P02} | `{id}` | {无（从列表进入）} | |
| 3 | `{/component/api-debug}` | `{ApiDebugPage}` | {P08} | — | {菜单：API 调试} | |

**路由总数**：{n} 条。

---

## 5 文件变更汇总

### 5.1 新增文件（{n} 个）

| # | 文件路径 | 类型 | 说明 |
|---|---------|------|------|
| 1 | `src/frontend/packages/{component}/src/services/apiClient.ts` | {服务} | {说明} |
| 2 | `src/frontend/packages/{component}/src/services/{resource}Api.ts` | {服务} | {说明} |
| 3 | `src/frontend/packages/{component}/src/hooks/use{Resource}List.ts` | {钩子} | {说明} |
| 4 | `src/frontend/packages/{component}/src/pages/{page}/index.tsx` | {页面} | {说明} |

### 5.2 修改文件（{n} 个）

| # | 文件路径 | 修改内容 |
|---|---------|---------|
| 1 | `src/frontend/packages/{component}/src/routes.tsx` | {新增 {n} 条路由} |
| 2 | `src/frontend/packages/{component}/src/pages/{page}/index.tsx` | {移除 mock 引用，改用 hooks} |
| 3 | `src/frontend/packages/{component}/src/models/{resource}.ts` | {补充 {字段} 字段} |
| 4 | `src/frontend/packages/{component}/package.json` | {新增依赖 {dep}} |

---

## 6 验证方法

| # | 验证项 | 方法 | 预期结果 | 状态 |
|---|--------|------|---------|------|
| 1 | {编译通过} | `{npm run build}` | {无 TypeScript 错误} | |
| 2 | {路由可达} | {逐条访问 §4 中的路径} | {页面正常渲染，无白屏} | |
| 3 | {API 联通} | {启动后端服务，打开 P{nn} API 调试页逐个端点点击} | {全部返回 `code: 0`，数据结构与 tech_design 一致} | |
| 4 | {列表分页} | {翻页、改排序、改筛选} | {请求参数正确，列表刷新} | |
| 5 | {详情页往返} | {列表 → 详情 → 返回} | {返回后列表状态保持} | |
| 6 | {错误处理} | {停止后端服务后操作页面} | {显示统一错误提示，不白屏} | |
| 7 | {与设计稿一致} | {对照 `ui_page_design/P{nn}/screen.png`} | {布局、字号、配色一致} | |
| 8 | {与 uiux 文档一致} | {对照 `{component}_uiux.md` §3.x} | {元素、交互、行为全部覆盖} | |

---

## 7 遗留与后续

| # | 遗留项 | 原因 | 计划版本 |
|---|--------|------|---------|
| 1 | {未实现项} | {原因} | {v{n}} |
