# nku-dev-knowledge — 仓库知识学习

面向多服务全栈项目的 AI 驱动代码学习 Skill。支持 N 个后端服务 + N 个前端项目，按各技术栈的分层视角深度学习，产出结构化知识文档。

## 核心能力

- **多服务支持**：`targets` 数组管理任意数量的后端服务和前端项目
- **类型驱动学习**：`type=backend` 按 Spring Boot 分层，`type=frontend` 按前端分层
- **首次全量、后续增量**：首次 4 步全量学习，后续对比 git diff 仅更新变更部分
- **独立知识文档**：每个服务产出到 `docs/<服务名>/`，互不干扰

## 目录结构

```
nku-dev-knowledge/
├── SKILL.md
├── config.json             ← targets 数组，每个服务一个条目
├── README.md
└── docs/
    ├── user-service/       ← type=backend 的服务
    │   ├── _overview.md
    │   ├── server.md
    │   └── modules/ (controller|service|mapper|entity|config)
    ├── order-service/      ← 又一个 backend 服务
    │   └── ...
    └── admin-frontend/     ← type=frontend 的项目
        ├── _overview.md
        ├── server.md
        └── modules/ (pages|components|store|api|router|utils)
```

## 快速开始

### 1. 配置

编辑 `config.json`，在 `targets` 数组中配置每个服务：

```json
{
  "targets": [
    {
      "name": "user-service",
      "type": "backend",
      "git_url": "https://github.com/your-org/user-service.git",
      "tech_stack": "spring-boot-mysql",
      "docs_output": "./docs/user-service",
      "layers": ["controller", "service", "mapper", "entity", "config"]
    },
    {
      "name": "admin-frontend",
      "type": "frontend",
      "git_url": "https://github.com/your-org/admin-frontend.git",
      "tech_stack": "vue3",
      "docs_output": "./docs/admin-frontend",
      "layers": ["components", "pages", "store", "api", "router", "utils"]
    }
  ]
}
```

### 2. 触发学习

| 说法 | 效果 |
|------|------|
| `学习仓库` / `学习所有服务` | 全量学习所有 targets |
| `学习 user-service` | 仅学习指定服务 |
| `更新知识库` | 增量学习所有 targets（对比 git diff） |
| `重新学习 user-service 的 Controller 层` | 指定服务 + 指定模块 |

### 3. 新增服务

在 `targets` 数组中追加条目，然后说"学习 <新服务名>"。

## 技术栈与分层

| type | tech_stack | layers |
|------|-----------|--------|
| `backend` | `spring-boot-mysql` | controller, service, mapper, entity, config |
| `frontend` | `vue3` / `react` | pages, components, store, api, router, utils |

新增技术栈时，配置对应的 `layers` 并创建对应的文档模板即可。
