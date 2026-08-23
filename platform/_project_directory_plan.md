# 项目目录规划

<!--
模板说明（使用时删除本注释块）
- 层级：**平台级目录规划**。注意它放在**项目根目录**（与 docs/ 同级），
  文件名去掉前导下划线 → `project_directory_plan.md`。
- 与其它平台级文档的分工：
  - 本文管**目录树长什么样**（顶层、docs/、src/、部署配置、VERSION 分布）。
  - `technical_overview.md` 管技术规范，只在 §2 摘要引用本文。
  - `naming_convention.md` 管标识符命名；本文 §1.1 只管**目录与文件名的大小写风格**。
- 本文没有组件级对应物 —— 它是全仓库唯一的目录权威。
  组件文档中的「Source Code Directory Layout」章节只描述**该组件目录内部**的结构，
  且必须落在本文 §4 规定的位置下。
- 目录树一律用代码块 + 行内注释表达，注释对齐；只列目录和关键文件，不逐一列举普通文件。
- 建议为每章加锚点 `<a id="c4-2"></a>`，供其它文档精确引用（如 "见 project_directory_plan.md §4.3"）。
-->

# 索引

1. [1 总则](#c1)
   1. [1.1 目录和文件命名规则](#c1-1)
2. [2 顶层目录结构](#c2)
3. [3 文档目录（docs/）](#c3)
   1. [3.1 组织原则](#c3-1)
   2. [3.2 目录结构](#c3-2)
4. [4 源代码目录（src/）](#c4)
   1. [4.1 组织原则](#c4-1)
   2. [4.2 前端目录结构](#c4-2)
   3. [4.3 后端目录结构](#c4-3)
   4. [4.4 API 定义目录](#c4-4)
   5. [4.5 网关、认证与部署配置](#c4-5)
5. [5 组件级版本控制](#c5)
   1. [5.1 策略说明](#c5-1)
   2. [5.2 VERSION 文件分布](#c5-2)

---

<a id="c1"></a>

# 1 总则

本文档定义「{平台中文名}」平台的项目目录结构，适用于整个代码仓库。
所有开发人员（含 AI 开发者）须遵循本规划创建和组织文件。

<a id="c1-1"></a>

## 1.1 目录和文件命名规则

| 范围 | 命名风格 | 示例 |
|------|---------|------|
| 所有 `docs/` 目录和文件 | **snake_case** | `{component_code_name}/`、`{component_code_name}_tech_design.md` |
| 所有后端代码目录和文件（`src/backend/`） | **snake_case** | `{component_code_name}/`、`source_media_handler.go` |
| 所有前端代码目录和文件（`src/frontend/`） | **kebab-case** | `{component-code-name}/`、`source-media-list.tsx` |
| `ui_page_design/` 子目录 | 自由格式 | `P{nn}  {页面中文名}/`（第三方工具生成，不强制 snake_case） |
| `ai_dev_history/` 子目录 | **编号 + PascalCase** | `01_DocReviewRefine/`、`02_DevPlanAndReport/` |

> **例外**：`VERSION`、`README.md`、`.gitkeep` 等通用文件名保持原样，不受上述规则约束。

> 标识符（类名、变量名、列名）的命名规范见 [naming_convention.md](docs/naming_convention.md)。

---

<a id="c2"></a>

# 2 顶层目录结构

项目仓库的最顶层仅包含以下内容：

```
{ProjectName}/
├── .github/                        # CI/CD 配置
├── .gitignore                      # Git 忽略规则
├── README.md                       # 项目说明
├── project_directory_plan.md       # 本文档：项目目录规划
├── docs/                           # 项目文档（详见 §3）
├── templates/                      # 文档模板（平台级 / 组件级 / mmd / ai_dev）
├── src/                            # 所有源代码（详见 §4）
├── deployment/                     # 编译产物与运行环境
└── {src_old_projects_migration}/   # 旧版项目数据迁移工具（可选）
```

| 目录 | 用途 | 是否入 Git |
|------|------|-----------|
| `docs/` | {全部设计文档与开发过程记录} | 是 |
| `templates/` | {文档模板，新建文档时从此处复制} | 是 |
| `src/` | {全部源代码与契约} | 是 |
| `deployment/` | {部署配置与运行环境} | {是；含密钥的文件除外} |

---

<a id="c3"></a>

# 3 文档目录（docs/）

<a id="c3-1"></a>

## 3.1 组织原则

| # | 原则 |
|---|------|
| 1 | **`docs/` 根目录**存放**平台级文档**——跨组件、面向整个平台的文档（业务概述、技术概述、技术架构图、各类设计规范、版本规划等）。具体文件数量随项目演进增加，不在本规划中逐一列举 |
| 2 | **`docs/components/`** 下为每个**产品组件**创建独立子目录，每个组件目录包含标准文档：`{组件名}_business_desc.md`、`{组件名}_tech_design.md`、`{组件名}_uiux.md`、`{组件名}_version_plan.md`，以及与之同名前缀的 `.mmd` / `.png` 图 |
| 3 | 每个组件目录下创建 **`ui_page_design/`** 子目录，存放该组件所有页面的 UI 设计文件。**每个页面一个文件夹**，以页面编号 + 页面名称命名（如 `P01  {页面名}/`）；每个页面文件夹内含该页面的 **HTML** 和 **PNG** 版本设计稿。页面流转图 `{组件名}_page_flow.mmd/.png` 也放在此目录 |
| 4 | **`docs/` 根目录**、**`docs/common_lib/`** 及 **`docs/components/` 下的每个组件目录**均创建 **`ai_dev_history/`** 子目录，存放使用 AI 开发模式时在该模块下的所有过程记录（review、dev_plan、debug、raw_prompts 等）。该目录将**开发过程记录与正式项目文档分离**。其内部再按类别分子目录，模板与目录结构见 `templates/ai_dev/` |
| 5 | 组件若存在**子模块 / 常驻进程**，为每个子模块建一个 `sub_{子模块名}/` 子目录，内含该子模块的设计文档 |
| 6 | 组件若涉及**旧系统迁移**，建一个 `old_projects_migration/` 子目录存放迁移计划与旧系统技术说明 |

<a id="c3-2"></a>

## 3.2 目录结构

> 注：目录结构中仅列出子目录，不逐一列出文件。各目录应含的标准文件已在 §3.1 中定义。

```
docs/
├── （平台级文档：business_overview.md / technical_overview.md /
│     uiux_design_specification.md / naming_convention.md /
│     version_plan.md / technical_architecture.mmd / business_process.mmd …）
├── VERSION                                 # 平台级文档整体版本
├── ai_dev_history/                         # AI 开发过程记录（平台级）
│   ├── prompt_history.md                # 原始 Prompt 归档（根目录，不进子目录）
│   ├── 01_DocReviewRefine/              # 文档级 review 与 refine
│   ├── 02_DevPlanAndReport/             # 开发计划 / 报告 / 现场 / 交接 / 改进计划
│   ├── 03_CodeReviewRefine/             # 代码级 review 与 refine
│   ├── 04_DebugFix/                     # 调试与缺陷修复
│   └── 99_Prompts/                      # 可复用 Prompt 库（分类见 templates/ai_dev/）
│
├── APIs/                                   # 全平台 API 端点汇总
│   └── api_endpoint_list.md
│
├── common_lib/                             # 公共类库文档（跨组件复用）
│   ├── VERSION
│   ├── ai_dev_history/
│   └── ui_page_design/
│
└── components/
    ├── {component_code_name_1}/            # {组件中文名 1}
    │   ├── VERSION
    │   ├── ai_dev_history/                 # 该组件的 AI 开发过程记录
    │   ├── ui_page_design/
    │   │   ├── P01  {页面中文名}/           #   └ code.html + screen.png
    │   │   └── …
    │   ├── sub_{子模块名}/                  # 子模块 / daemon 设计文档（可选）
    │   └── old_projects_migration/         # 旧系统迁移文档（可选）
    ├── {component_code_name_2}/
    │   ├── VERSION
    │   ├── ai_dev_history/
    │   └── ui_page_design/
    └── …
```

---

<a id="c4"></a>

# 4 源代码目录（src/）

<a id="c4-1"></a>

## 4.1 组织原则

| # | 原则 |
|---|------|
| 1 | `src/` 下包含所有与源代码相关的目录：前端、后端、API 定义、网关配置、认证配置、部署配置和脚本 |
| 2 | **前端**采用 {pnpm workspace Monorepo} 结构，所有产品组件共享同一套构建配置和公用库 |
| 3 | **后端**支持 {语言A} 和 {语言B} 两种语言，公用库按语言分别维护 |
| 4 | **API 定义**集中存放在 `src/api/`，作为前后端开发的**唯一契约** |
| 5 | 前端和后端内部均按「**公用库 + 产品组件**」方式组织子目录 |
| 6 | 各组件目录**仅在实际进入该模块开发阶段时创建**，规划阶段不预先创建空目录（前端例外：可先放 `VERSION` 占位） |

```
src/
├── frontend/                   # 前端 Monorepo
├── backend/                    # 后端服务
├── api/                        # OpenAPI 定义（集中存放）
├── gateway/                    # API 网关配置
├── auth/                       # 认证与权限配置
├── deploy/                     # 部署配置（Docker / K8s 等）
└── scripts/                    # 构建、开发、运维脚本
```

<a id="c4-2"></a>

## 4.2 前端目录结构

前端采用 **{pnpm workspace Monorepo}** 结构，所有产品组件共享同一套构建配置和公用库。

```
src/frontend/
├── packages/
│   ├── shared/                 # 前端公用库（UI 组件、工具函数、类型定义）
│   │   └── VERSION
│   ├── common-lib/             # 公共类库前端（跨组件复用的业务组件）
│   │   └── VERSION
│   ├── {unified-portal}/       # 统一入口前端（框架、菜单、Dashboard）
│   │   └── VERSION
│   ├── {component-code-name}/  # {组件中文名} 前端
│   │   └── VERSION
│   └── …
├── package.json                # Monorepo 根配置
├── {pnpm-workspace.yaml}
├── tsconfig.base.json          # 共享 TypeScript 配置
├── {vite.config.shared.ts}     # 共享构建配置
└── {.eslintrc.js}
```

**单个组件包内部结构**（仅在进入该模块前端开发阶段时创建）：

```
packages/{component-code-name}/
├── src/
│   ├── pages/                  # 页面组件，一页一目录，与 uiux 文档的 P{nn} 对应
│   ├── components/             # 该包内复用的组件
│   ├── services/               # API 客户端封装（api.ts）
│   ├── models/                 # TypeScript 类型定义（C/E/I 前缀）
│   ├── stores/                 # 状态管理，一业务实体一 store
│   ├── mock/                   # 硬编码 mock 数据（v0.1 阶段用）
│   └── routes.tsx              # 路由注册，与 uiux 文档「路由定义」章节一致
└── VERSION
```

**说明**：

- `packages/shared/` 存放跨组件复用的 UI 组件、HTTP 封装、权限工具、类型定义。
- `packages/common-lib/` 存放跨组件复用的**业务**组件，对应 `docs/common_lib/`。
- 前端包命名采用 **kebab-case**（见 §1.1）。
- {每个组件前端包独立打包，由 Portal 在运行时动态加载或通过路由引入 / 或统一打包为单一 SPA——按实际选型填写}。

<a id="c4-3"></a>

## 4.3 后端目录结构

后端支持 {语言A} 和 {语言B}，公用库按语言分别维护。

```
src/backend/
├── shared/                            # 后端公用库
│   ├── {py-shared}/                   # {语言A} 公用库（DB 连接、日志、鉴权中间件）
│   │   └── VERSION
│   └── {go-shared}/                   # {语言B} 公用库
│       └── VERSION
├── {component_code_name}/             # {组件中文名} 后端
│   └── VERSION
└── …
```

**单个后端服务内部结构**（仅在进入该模块后端开发阶段时创建）：

```
src/backend/{component_code_name}/
├── cmd/                        # 入口程序，一个可执行文件一个子目录
│   ├── {component}_api/        #   REST API 服务入口
│   └── {daemon_name}/          #   常驻 daemon 入口（若有）
├── config/                     # 配置文件与配置结构体
├── internal/
│   ├── handler/                # HTTP 层：参数解析、验证、响应封装、路由注册
│   ├── service/                # 业务逻辑层、事务编排
│   ├── repository/             # 数据访问层（SQL / ORM）
│   ├── model/                  # 数据结构定义（C 前缀类）
│   ├── enum/                   # 枚举定义（E 前缀）
│   └── mock/                   # 硬编码 mock 数据（v0.2 阶段用）
├── pkg/                        # 可被外部引用的工具（统一响应结构等）
├── sql/                        # 建库脚本、迁移脚本、样例数据
│   ├── init_databases.sql
│   ├── migrations/
│   └── sample_data.sql
└── VERSION
```

**说明**：

- `shared/{py-shared}/` 与 `shared/{go-shared}/` 分别为两种语言的公用库，含数据库连接管理、
  统一日志、鉴权中间件、错误处理、通用数据模型。
- 分层职责边界见 [technical_overview.md](docs/technical_overview.md) §1.3，**禁止跨层调用**。
- 各组件可自由选择实现语言，选型登记在 `technical_overview.md` §3。

<a id="c4-4"></a>

## 4.4 API 定义目录

所有产品组件的 OpenAPI 定义集中存放在 `src/api/`，作为前后端开发的**唯一契约**。

```
src/api/
├── {component_code_name}/      # {组件中文名} API 定义
│   └── openapi.yaml
├── …
└── shared/                     # 公共 Schema（跨组件复用的数据结构）
    └── common-schemas.yaml
```

**说明**：

- 每个组件的 OpenAPI 定义独立维护，版本跟随组件迭代。
- `shared/common-schemas.yaml` 存放跨组件复用的数据结构（分页参数、统一响应体、错误结构等）。
- {前端通过 OpenAPI 定义自动生成 TypeScript 类型和 API 客户端代码}。
- 端点清单的人类可读汇总见 `docs/APIs/api_endpoint_list.md`，两者必须一致。

<a id="c4-5"></a>

## 4.5 网关、认证与部署配置

```
src/gateway/                    # API 网关配置
├── routes.yaml                 # 路由规则（URL → 后端服务映射）
├── rate-limit.yaml             # 限流策略
└── cors.yaml                   # 跨域策略

src/auth/                       # 认证与权限配置
├── providers.yaml              # 认证提供方配置（{GCP / AWS}）
├── permissions.yaml            # 功能注册表与权限矩阵
└── rbac-policies.yaml          # RBAC 策略定义

src/deploy/                     # 部署配置
├── docker/                     # 各服务的 Dockerfile
├── k8s/                        # Kubernetes 清单文件
├── env/                        # 各环境变量配置（dev / staging / prod）
└── docker-compose.yaml         # 本地开发一键启动

src/scripts/                    # 构建、开发、运维脚本
├── build.sh
├── dev-setup.sh
└── …
```

> **安全约束**：`env/` 下含真实密钥的文件**不得入 Git**，仅提交 `.example` 模板。

---

<a id="c5"></a>

# 5 组件级版本控制

<a id="c5-1"></a>

## 5.1 策略说明

本平台采用**多组件独立版本控制**策略（Per-Component Versioning）。
这是多组件、跨语言 Monorepo 中的常见做法，核心思路如下：

| # | 要点 | 说明 |
|---|------|------|
| 1 | **每个可独立发布的单元拥有自己的 `VERSION` 文件** | 包括每个组件的文档、前端包、后端服务以及公用库。文件内容为**纯文本一行版本号**（如 `0.0.0`），不含其它内容 |
| 2 | **版本号格式** | 遵循 `docs/version_plan.md` 中定义的 `vA.B.C.D.E` 规范。`VERSION` 文件中的值**不带 `v` 前缀**（`0.0.0` 而非 `v0.0.0`） |
| 3 | **独立演进** | 各组件按自身节奏迭代——文档可能在 `0.2`，前端已到 `1.0.3`，后端仍在 `0.2.5`。组件之间**不要求版本号同步** |
| 4 | **Git Tag 命名规则** | 发布某组件特定版本时使用 `{层}/{组件名}/v{版本号}` 格式打 Tag，例如：<br>`docs/{component_code_name}/v0.1`<br>`fe/{component_code_name}/v1.0.1`<br>`be/{component_code_name}/v0.2.3`<br>`be/shared/{go-shared}/v1.0.0` |
| 5 | **CI/CD 集成** | 流水线读取各组件 `VERSION` 文件决定 Docker 镜像 Tag、制品版本号和发布 Changelog |

<a id="c5-2"></a>

## 5.2 VERSION 文件分布

下表列出所有 `VERSION` 文件的位置及初始值：

| 位置 | 初始版本 | 说明 |
|------|---------|------|
| `docs/VERSION` | `{0.1.0}` | 平台级文档整体版本 |
| `docs/common_lib/VERSION` | `{0.0.0}` | 公共类库文档版本 |
| `docs/components/{组件名}/VERSION` | `{0.0.0}` | 各组件文档版本（×{N}） |
| `src/frontend/packages/shared/VERSION` | `{0.0.0}` | 前端公用库版本 |
| `src/frontend/packages/{组件名}/VERSION` | `{0.0.0}` | 各组件前端版本（×{N}） |
| `src/backend/shared/{py-shared}/VERSION` | `{0.0.0}` | {语言A} 后端公用库版本 |
| `src/backend/shared/{go-shared}/VERSION` | `{0.0.0}` | {语言B} 后端公用库版本 |
| `src/backend/{组件名}/VERSION` | `{0.0.0}` | 各组件后端版本（×{N}） |

> 组件自身的版本路线图（每个 v0.x 做什么）写在
> `docs/components/{组件名}/{组件名}_version_plan.md`，模板见 `templates/component/_version_plan.md`。
