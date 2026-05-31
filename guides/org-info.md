# DogApiKey 组织信息

## 组织概述

DogApiKey 是一个 GitHub 组织，专注于 AI API 网关和相关工具的开发与部署。组织提供多个产品，涵盖 LLM 网关、客服机器人、知识库管理、API 订阅配额分发以及聊天面板等功能。

- **GitHub 主页**: https://github.com/DogApiKey
- **组织类型**: GitHub Organization

---

## 产品与项目

### 1. DogAPI（主项目）

- **仓库**: https://github.com/DogApiKey/DogAPI
- **描述**: 下一代 LLM 网关与 AI 资产管理系统
- **基础**: 基于 [New API](https://github.com/Calcium-Ion/new-api)（原 [One API](https://github.com/songquanpeng/one-api)）的分支
- **主要语言**: Go
- **许可证**: AGPLv3

**核心功能**:
- 多语言支持（简体中文、繁体中文、英文、法文、日文）
- 与原版 One API 数据库完全兼容
- 数据可视化仪表盘与统计分析
- Token 分组、模型限制、用户权限管理
- 在线充值（EPay、Stripe）
- 按量计费、缓存计费
- 多种授权登录方式（Discord、LinuxDO、Telegram、OIDC）

**支持的 API 格式**:
- OpenAI 兼容格式
- OpenAI Responses 格式
- OpenAI Realtime API（含 Azure）
- Claude Messages 格式
- Google Gemini 格式
- Midjourney-Proxy
- Suno API
- Rerank 模型（Cohere、Jina）
- Dify ChatFlow 模式

**智能路由**:
- 渠道加权随机
- 失败自动重试
- 用户级别模型限速

**格式转换**:
- OpenAI 兼容 <-> Claude Messages
- OpenAI 兼容 -> Google Gemini
- Google Gemini -> OpenAI 兼容
- Thinking-to-content 功能

**部署方式**:
- Docker Compose（推荐）
- Docker 命令行
- 宝塔面板一键安装

**官方文档**: https://docs.newapi.pro/en/docs

---

### 2. CSBot（客服机器人）

- **仓库**: https://github.com/DogApiKey/dog-csbot
- **描述**: 基于 RAG 的多渠道智能客服机器人
- **技术栈**: TypeScript、Bun、Hono、Pi Agent 框架
- **许可证**: MIT

**核心功能**:
- 多渠道架构：Web 组件（MVP）、Telegram/Discord（计划中）
- RAG 问答：文档导入、分块、嵌入、混合搜索
- 流式响应：实时 SSE 流式推送
- 管理后台：文档管理、对话查看、统计数据
- 水平扩展：无状态服务器、Redis Pub/Sub、容器化部署
- LLM 无关：支持 Anthropic、OpenAI、Google 等 20+ 提供商

**架构**:
- Web Widget / Telegram / Discord -> CSBot Server（Bun + Hono）
- 依赖：Redis（Pub/Sub）、PostgreSQL（会话）、Qdrant（向量存储）

**API 端点**:
- `POST /api/chat` — 发送消息
- `GET /api/chat/:id/stream` — SSE 流式响应
- `GET /api/chat/:id/messages` — 消息历史
- `POST /api/chat/conversations` — 创建对话
- `GET /api/admin/stats` — 仪表盘统计
- `GET /api/admin/documents` — 文档列表
- `POST /api/admin/documents` — 上传文档
- `POST /api/admin/sync/github` — 从 GitHub 同步知识库
- `GET /health` — 存活探针
- `GET /ready` — 就绪探针

**Widget 嵌入**:
```html
<script src="https://your-domain/csbot-widget.js"></script>
<csbot-widget
  api-url="https://your-domain/api"
  title="Support Chat"
  greeting="Hi! How can I help you?"
></csbot-widget>
```

**部署方式**:
- Railway（推荐）
- Docker Compose（自托管）

---

### 3. DogAPI 知识库

- **仓库**: https://github.com/DogApiKey/dogapi-kb
- **描述**: CSBot 客服机器人的知识库文档仓库

**目录结构**:
- `api/` — API 使用文档（端点说明、认证方式、错误码）
- `pricing/` — 价格相关（模型定价表）
- `guides/` — 使用教程（快速开始、常见问题）
- `changelog/` — 更新日志
- `qq-faq/` — QQ 群整理的 FAQ

**文档更新流程**:
1. 在对应目录下添加或修改 Markdown 文件
2. Commit 并 Push 到仓库
3. CSBot 自动检测变更并更新知识库（或手动触发重新索引）

---

### 4. dog_sub2api（API 订阅配额分发网关）

- **仓库**: https://github.com/DogApiKey/dog_sub2api
- **描述**: AI API 网关平台，用于订阅配额分发与管理
- **基础**: 基于 [Sub2API](https://github.com/Wei-Shaw/sub2api) 的分支
- **技术栈**: Go（后端）、Vue 3（前端）、PostgreSQL、Redis
- **许可证**: LGPLv3

**核心功能**:
- 多账户管理（OAuth、API Key）
- API Key 分发与管理
- Token 级别精确计费
- 智能调度与粘性会话
- 并发控制与限速
- 内置支付系统（EasyPay、支付宝、微信、Stripe）
- 管理后台仪表盘

**官方域名**: `sub2api.org`、`pincc.ai`

---

### 5. Dog Chat（聊天面板）

- **仓库**: https://github.com/DogApiKey/dog_chat
- **描述**: 基于 LibreChat 定制的聊天面板，适用于 OpenAI 兼容 API 网关和 New API 部署

**核心功能**:
- API Key 登录（`/apikey-login`）
- 按 `API Key + API URL` 隔离聊天历史、上传文件和凭证
- 自定义端点 `中转站`，兼容 OpenAI 格式
- 从用户配置的 `/models` 端点获取模型列表
- 默认对话配置：`gpt-5.4`、Responses API、原生 `web_search`、高推理强度

---

## 联系方式与链接

| 项目 | 链接 |
|------|------|
| GitHub 组织主页 | https://github.com/DogApiKey |
| DogAPI 仓库 | https://github.com/DogApiKey/DogAPI |
| CSBot 仓库 | https://github.com/DogApiKey/dog-csbot |
| 知识库仓库 | https://github.com/DogApiKey/dogapi-kb |
| Sub2API 仓库 | https://github.com/DogApiKey/dog_sub2api |
| Dog Chat 仓库 | https://github.com/DogApiKey/dog_chat |
| New API 官方文档 | https://docs.newapi.pro/en/docs |
| Sub2API 官方域名 | https://sub2api.org / https://pincc.ai |
| Sub2API 演示站 | https://demo.sub2api.org/ |

---

## 技术栈总览

| 组件 | 技术 |
|------|------|
| LLM 网关（DogAPI） | Go、SQLite/MySQL/PostgreSQL、Redis |
| 客服机器人（CSBot） | TypeScript、Bun、Hono、PostgreSQL、Redis、Qdrant |
| API 网关（Sub2API） | Go、Gin、Ent、Vue 3、PostgreSQL、Redis |
| 聊天面板（Dog Chat） | LibreChat、Node.js、MongoDB |
| 容器化 | Docker、Docker Compose |
| 部署平台 | Railway、宝塔面板、自托管 |
