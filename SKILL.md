---
name: nku-dev-knowledge
description: 深度学习多个代码仓库（N 个后端服务 + N 个前端项目），按各自技术栈的分层视角产出结构化知识文档。首次全量学习，后续增量更新。
---

# nku-dev-knowledge — 仓库知识学习

面向多服务全栈项目的 AI 驱动代码学习 Skill。支持 N 个后端服务 + N 个前端项目，按各技术栈的分层视角独立学习，产出知识文档到 `docs/<target-name>/` 目录。

## 触发条件

| 用户说法 | 效果 |
|---------|------|
| "学习仓库" / "学习所有服务" | 首次全量学习所有 targets |
| "学习 <target-name>" | 仅学习指定 target |
| "更新知识库" / "同步知识" | 增量学习，重新读取本地目录，对比现有知识文档仅更新变更部分 |
| "重新学习 <target> 的 <模块>" | 指定 target + 模块增量更新 |

## 配置文件

执行前先读取 `config.json`，获取：
- `targets` — 要学习的项目列表，每个 target 包含：
  - `name` — 唯一标识（如 `user-service`、`admin-frontend`）
  - `type` — 服务类型：`backend`（Spring Boot 后端）或 `frontend`（Vue3/React 前端）
  - `local_path` — 本地代码目录的绝对路径（不再拉取远程代码，直接读取本地目录）
  - `tech_stack` — 技术栈
  - `docs_output` — 文档产出目录（`./docs/<name>`）
  - `layers` — 该技术栈对应的分层模块
- `knowledge_repo.git_url` — 知识库自身的 git 仓库地址（学习完成后 push 知识文档用）

## 学习策略

**首次全量，后续增量。** `type` 决定使用哪种分层视角。

### type=backend（Spring Boot 后端服务）

分层视角：Controller → Service → Mapper → Entity → Config

| 文档 | 内容 |
|------|------|
| `_overview.md` | 服务定位、包结构、Controller 清单、MySQL 表概览、外部依赖（含调用的其他服务） |
| `server.md` | pom.xml 关键依赖、Maven 命令、application.yml 摘要、数据库连接、服务端口 |
| `modules/controller.md` | 所有 REST 接口：路径、方法、参数、返回值、异常处理 |
| `modules/service.md` | 业务逻辑：核心方法、事务、调用关系（含 Feign/RPC 调用其他服务） |
| `modules/mapper.md` | 数据访问：SQL、关联查询、分页 |
| `modules/entity.md` | 实体类与 MySQL 表映射、字段、索引、主键策略 |
| `modules/config.md` | Spring 配置类、拦截器、过滤器、全局异常处理、Feign 客户端配置 |

### type=frontend（Vue3 / React 前端）

分层视角：Pages → Components → Store → API → Router → Utils

| 文档 | 内容 |
|------|------|
| `_overview.md` | 项目定位、技术栈版本、页面路由清单、组件树概览、调用的后端服务列表 |
| `server.md` | package.json 关键依赖、构建/启动命令、环境变量、代理配置 |
| `modules/pages.md` | 页面组件与路由映射、页面级状态、数据获取 |
| `modules/components.md` | 公共组件：Props、Emits、Slots、使用场景 |
| `modules/store.md` | 状态管理：Store 模块、State/Getter/Action |
| `modules/api.md` | 接口调用层：请求封装、拦截器、API 函数列表（标注调用的后端服务） |
| `modules/router.md` | 路由配置：路径、守卫、懒加载 |
| `modules/utils.md` | 工具函数、Composables/Hooks、常量 |

## config.json 字段分类

学习过程中，config.json 的 target 字段分为两类：

### 受保护字段（人工指定，学习过程不可修改）

| 字段 | 说明 |
|------|------|
| `name` | 服务唯一标识 |
| `type` | backend / frontend |
| `description` | 服务描述 |
| `local_path` | 本地代码目录路径 |
| `docs_output` | 知识文档产出目录 |

### 可发现字段（学习过程自动探测并回写）

| 字段 | 说明 | 探测方式 |
|------|------|---------|
| `tech_stack` | 实际技术栈 | 从 pom.xml / package.json / 项目文件推断 |
| `build_tool` | 构建工具 | 检测 pom.xml(maven) / build.gradle(gradle) / package.json 等 |
| `main_class` | 启动类（后端） | 扫描 @SpringBootApplication 注解 |
| `profiles` | 激活的 profile | 扫描 application-*.yml / .env 文件 |
| `database` | 数据库名 | 从 application.yml 连接串提取 |
| `orm` | ORM 框架（后端） | 检测 mybatis-plus / jpa / jdbc 等依赖 |
| `layers` | 实际分层模块 | 扫描包结构，以实际存在的目录为准 |
| `port` | 服务端口（后端） | 从 application.yml server.port 提取 |

学习过程中如果实际探测值与 config 中的值不一致，**自动更新 config.json** 中对应的可发现字段。受保护字段永远不动。

## 首次全量学习流程（4步）

对每个 target 依次执行：

### STEP 1：确认目标 + 更新配置
读取 `local_path` 本地目录 → 探测可发现字段 → 若与 config.json 当前值不一致则回写更新 → 输出确认报告（标注哪些配置字段被自动修正）

### STEP 2：全局学习
产出 `_overview.md` + `server.md`

### STEP 3：模块深度学习
按 target 的实际 `layers`（以探测结果为准）逐模块学习，产出 `modules/*.md`

### STEP 4：校验归档 + 推送
完整性检查 → 写入 `docs/<target-name>/` → 将更新后的 config.json 一并 git add → git commit → git push（将最新知识文档 + 配置推送到 `knowledge_repo.git_url` 仓库）

## 增量学习

重新读取各 target 的 `local_path` 本地目录，对比已有知识文档，仅更新有变更的部分。增量学习同样需要重新探测可发现字段，如有变化则回写 config.json。每次增量更新后 git add → git commit → git push 知识库（含 config.json）。

## 新增服务

在 `config.json` 的 `targets` 数组中追加即可（受保护字段必填，可发现字段可留空让学习过程自动填充）：
```json
{ "name": "payment-service", "type": "backend", "local_path": "C:/path/to/payment-service" }
```
然后说"学习 payment-service"触发该服务的学习，可发现字段会自动探测并回写。
