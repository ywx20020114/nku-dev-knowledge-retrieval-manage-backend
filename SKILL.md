---
name: nku-dev-knowledge
description: 深度学习代码仓库（支持多项目），以各自技术栈的分层架构视角产出结构化知识文档。首次全量学习（4步），后续增量更新。
---

# nku-dev-knowledge — 仓库知识学习

针对多个代码仓库，以各自技术栈的分层视角进行代码学习，产出结构化知识文档到 `docs/<target>/` 目录，供 `nku-dev-workflow` 研发流程读取使用。

## 触发条件

| 用户说法 | 触发模式 |
|---------|---------|
| "学习仓库" / "学习项目" / "开始学习" | 首次全量学习所有 targets |
| "学习后端" / "学习 backend" | 仅学习 backend target |
| "学习前端" / "学习 frontend" | 仅学习 frontend target |
| "更新知识库" / "同步知识" | 增量学习，对比各 target git diff 仅更新变更部分 |
| "重新学习 Controller 层" / "重新学习 components" 等 | 指定 target + 模块增量更新 |

## 配置文件

执行前先读取 `config.json`，获取：
- `targets` — 要学习的项目列表，每个 target 包含：
  - `name` — 项目标识（backend / frontend）
  - `git_url` / `branch` — 项目仓库地址
  - `tech_stack` — 技术栈（spring-boot-mysql / vue3 / react 等）
  - `docs_output` — 文档产出目录
  - `layers` — 分层模块列表
- `knowledge_repo.git_url` — 知识库自身的 git 仓库地址

## 学习策略

**首次全量，后续增量。** 不同技术栈使用不同的分层视角和文档模板。

### 后端技术栈（spring-boot-mysql）

分层视角：Controller → Service → Mapper → Entity → Config

| 文档 | 对应层 | 内容 |
|------|-------|------|
| `_overview.md` | 全局 | 项目定位、包结构、Controller 清单、MySQL 表概览、外部依赖 |
| `server.md` | 运维 | pom.xml 关键依赖、Maven 命令、application.yml 摘要、数据库连接 |
| `modules/controller.md` | Controller | 所有 REST 接口：路径、方法、参数、返回值、异常处理 |
| `modules/service.md` | Service | 业务逻辑：核心方法、事务、调用关系 |
| `modules/mapper.md` | Mapper/DAO | 数据访问：SQL、关联查询、分页 |
| `modules/entity.md` | Entity/Model | 实体类与 MySQL 表映射、字段、索引、主键策略 |
| `modules/config.md` | Config | Spring 配置类、拦截器、过滤器、全局异常处理 |

### 前端技术栈（vue3 / react）

分层视角：Components → Pages → Store → API → Router → Utils

| 文档 | 对应层 | 内容 |
|------|-------|------|
| `_overview.md` | 全局 | 项目定位、技术栈版本、页面路由清单、组件树概览、外部依赖 |
| `server.md` | 运维 | package.json 关键依赖、构建/启动命令、环境变量、代理配置 |
| `modules/components.md` | Components | 公共组件：Props、Emits、Slots、使用场景 |
| `modules/pages.md` | Pages | 页面组件与路由映射、页面级状态、生命周期数据获取 |
| `modules/store.md` | Store | 状态管理：Store 模块、State/Getter/Action 定义 |
| `modules/api.md` | API | 接口调用层：请求封装、拦截器、API 函数列表 |
| `modules/router.md` | Router | 路由配置：路径、守卫、懒加载 |
| `modules/utils.md` | Utils | 工具函数、自定义 Hooks/Composables、常量定义 |

## 首次全量学习流程（4步）

### STEP 1：确认目标

对每个 target：
1. clone 目标仓库，切换到指定分支
2. 根据 `tech_stack` 识别技术栈，定位核心配置文件和依赖
3. 输出目标确认报告

### STEP 2：全局学习 → 产出 `_overview.md` + `server.md`

针对每个 target 的技术栈，产出全局视图文档。

### STEP 3：模块深度学习 → 产出 `modules/*.md`

按照 target 的 `layers` 配置，逐个模块深度学习并产出文档。

### STEP 4：校验归档

1. 检查文档完整性
2. 所有文档写入对应的 `docs/<target>/` 目录
3. 知识库 git commit + push

## 增量学习

全量学习完成后，后续根据代码变更增量更新。

**自动判断更新范围（对比 git diff）**：

| 变更类型 | 更新文档 |
|---------|---------|
| 新增/删除路由或接口 | `_overview.md` + 对应模块文档 |
| 新增/删除业务模块 | 对应模块文档 |
| 核心配置文件变更 | `server.md` |
| 新增外部依赖 | `_overview.md` |

**每次增量更新后必须**：git add → git commit → git push。

## 指定 target + 模块更新

- "重新学习后端 Controller 层" → 仅更新 `docs/backend/modules/controller.md`
- "重新学习前端 components" → 仅更新 `docs/frontend/modules/components.md`
