# Dog Chat 用户指南

## 简介

Dog Chat 是一个基于 [LibreChat](https://github.com/danny-avila/LibreChat) 定制的 AI 对话面板，专为 OpenAI 兼容的 API 中转站部署而设计。用户无需注册邮箱/密码账号，只需输入自己的 API Key 和 API URL 即可登录使用，系统会自动为每个 API Key 创建隔离的聊天环境。

**项目地址：** https://github.com/DogApiKey/dog_chat

---

## 核心功能

### API Key 登录

- 访问 `/apikey-login` 页面，输入你的 API Key 和 API URL 即可完成登录。
- 系统会向你配置的 `/models` 端点验证 API Key 的有效性。
- 验证通过后，系统自动创建一个与该 API Key 绑定的独立账号。
- 每个 `API Key + API URL` 组合对应一个独立的 LibreChat 账号，聊天记录、上传文件、端点凭据完全隔离。

### 默认配置

- 默认 API URL：`https://www.dogapi.cc/v1`（可在登录页面修改）
- 默认模型：`gpt-5.4`
- 默认启用 Responses API
- 默认启用原生网页搜索（web_search）
- 默认推理强度：`high`
- 默认推理摘要：`detailed`
- 推理/思考内容默认展开显示

### 端点配置

- 系统暴露一个名为 `中转站` 的自定义端点，用于连接 OpenAI 兼容的 API 网关。
- 模型列表从用户配置的 `/models` 端点动态获取。
- 支持的模型预设包括 GPT-5.4 和 Claude Opus 4.6 等。

### 文件上传

- 单端点文件数量限制：5 个
- 单文件大小限制：20 MB
- 单端点总大小限制：50 MB
- 服务器文件大小限制：100 MB
- 支持图片自动压缩（最大 1900x1900，质量 92%）

### 其他特性

- 书签和多会话并行功能
- 文件搜索和文件引用
- 提示词创建和使用（不支持公开分享）
- 禁用公开注册（仅限 API Key 登录）
- HTTP 部署时自动适配 Cookie 策略（非 HTTPS 环境不设置 Secure 标记）

---

## 快速部署

### 前置要求

- Docker 和 Docker Compose
- 至少 2GB 可用内存

### 方式一：使用官方部署模板（推荐）

1. 克隆仓库：

```bash
git clone https://github.com/DogApiKey/dog_chat.git
cd dog_chat/deploy/gateway-demo
```

2. 复制并编辑环境配置文件：

```bash
cp librechat/.env.example librechat/.env
```

3. 编辑 `librechat/.env` 文件，替换所有 `CHANGE_ME` 的值（详见下方「环境变量说明」）。

4. 创建必要的目录并设置权限：

```bash
mkdir -p librechat/images librechat/uploads librechat/logs librechat/data-node librechat/meili_data_v1.35.1
sudo chown -R 1000:1000 librechat/images librechat/uploads librechat/logs librechat/data-node librechat/meili_data_v1.35.1
```

5. 启动服务：

```bash
docker compose up -d --build
```

6. 访问 `http://localhost:3080/apikey-login`，输入你的 API Key 开始使用。

### 方式二：本地开发构建

```bash
npm install
npm run build:api
npm run build:client
```

然后使用 `deploy/gateway-demo` 中的 Docker Compose 模板运行，或根据需要调整配置。

---

## 环境变量说明

以下是部署时需要关注的关键环境变量（在 `librechat/.env` 中配置）：

### 必须修改的变量

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `JWT_SECRET` | JWT 签名密钥 | 随机生成的 64 字符十六进制字符串 |
| `JWT_REFRESH_SECRET` | JWT 刷新令牌密钥 | 随机生成的 64 字符十六进制字符串 |
| `CREDS_KEY` | 凭据加密密钥 | 随机生成的 64 字符十六进制字符串 |
| `CREDS_IV` | 凭据加密初始向量 | 随机生成的 32 字符十六进制字符串 |
| `MEILI_MASTER_KEY` | MeiliSearch 搜索引擎密钥 | 随机生成的字符串 |
| `API_KEY_LOGIN_PEPPER` | API Key 登录哈希加盐 | 任意随机字符串 |

### 服务地址配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `DOMAIN_CLIENT` | 客户端访问地址 | `http://localhost:3080` |
| `DOMAIN_SERVER` | 服务端地址 | `http://localhost:3080` |
| `MONGO_URI` | MongoDB 连接地址 | `mongodb://librechat-mongodb:27017/LibreChat` |
| `MEILI_HOST` | MeiliSearch 地址 | `http://librechat-meilisearch:7700` |
| `RAG_API_URL` | RAG API 地址 | `http://librechat-rag-api:8000` |

### API Key 登录配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `API_KEY_LOGIN_ENDPOINT` | 登录后使用的端点名称 | `dogapi` |
| `API_KEY_LOGIN_DEFAULT_BASE_URL` | 默认 API 中转地址 | `https://www.dogapi.cc/v1` |
| `NEW_API_MODELS` | 备用模型列表（逗号分隔） | 空（从 /models 动态获取） |

### 安全配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `ALLOW_REGISTRATION` | 是否允许公开注册 | `false` |
| `ALLOW_EMAIL_LOGIN` | 是否允许邮箱登录 | `true` |
| `ALLOW_PASSWORD_RESET` | 是否允许密码重置 | `false` |
| `DISABLE_SSRF_PROTECTION` | 是否禁用 SSRF 保护 | `true`（仅限演示环境） |

---

## 生产环境部署注意事项

### 安全加固

1. **必须替换所有默认密钥**：`JWT_SECRET`、`JWT_REFRESH_SECRET`、`CREDS_KEY`、`CREDS_IV`、`MEILI_MASTER_KEY`、`API_KEY_LOGIN_PEPPER` 都必须使用随机生成的唯一值。

2. **启用 HTTPS**：生产环境必须使用 HTTPS。设置 `DOMAIN_CLIENT` 和 `DOMAIN_SERVER` 为你的 HTTPS 公网地址。

3. **SSRF 保护**：生产环境不应设置 `DISABLE_SSRF_PROTECTION=true`。应使用 `endpoints.allowedAddresses` 配置仅允许已知的网关域名。

4. **不要提交敏感文件**：`.env` 文件、数据库目录、上传文件、日志和 Docker 卷不应提交到代码仓库。

### 架构组成

部署模板包含以下服务：

| 服务 | 说明 | 内存限制 |
|------|------|----------|
| `librechat` | Dog Chat 主应用 | 768 MB |
| `librechat-mongodb` | MongoDB 数据库 | 512 MB |
| `librechat-meilisearch` | MeiliSearch 全文搜索引擎 | 384 MB |
| `librechat-vectordb` | pgvector 向量数据库（用于 RAG） | 256 MB |
| `librechat-rag-api` | RAG API 服务 | 384 MB |

### 数据持久化

以下目录包含持久化数据，备份时请注意：

- `librechat/data-node/` -- MongoDB 数据
- `librechat/meili_data_v1.35.1/` -- 搜索索引
- `librechat/uploads/` -- 用户上传文件
- `librechat/images/` -- 生成的图片
- `librechat/logs/` -- 应用日志
- Docker 卷 `librechat-pgdata` -- 向量数据库数据

---

## 常见问题

### Q: 登录时提示 API Key 验证失败？

A: 请检查：
- API Key 是否正确且有效
- API URL 是否可访问（系统会调用 `{API_URL}/models` 进行验证）
- 如果使用自定义网关，确认网关地址在 `endpoints.allowedAddresses` 白名单中

### Q: 如何更换默认 API 中转地址？

A: 修改 `librechat/.env` 中的 `API_KEY_LOGIN_DEFAULT_BASE_URL` 变量，然后重启服务。

### Q: 不同用户的聊天记录会互相看到吗？

A: 不会。每个 `API Key + API URL` 组合对应一个独立的 LibreChat 账号，聊天记录、上传文件和端点凭据完全隔离。

### Q: 如何更新到新版本？

A: 拉取最新代码后重新构建镜像并重启：

```bash
git pull
cd deploy/gateway-demo
docker compose up -d --build
```

### Q: HTTP 部署时会话丢失怎么办？

A: Dog Chat 已针对 HTTP 部署做了适配，不会在非 HTTPS 环境下设置 `Secure` Cookie 标记。如果仍然遇到问题，建议升级到 HTTPS。

---

## 项目结构

```
dog_chat/
├── api/                    # 后端 API 代码（修补后的 LibreChat）
├── client/                 # 前端代码（修补后的 LibreChat）
├── packages/               # 共享包
├── deploy/
│   └── gateway-demo/       # 官方部署模板
│       ├── docker-compose.yml
│       └── librechat/
│           ├── .env.example
│           └── librechat.yaml
├── config/                 # 管理脚本
├── librechat.yaml          # LibreChat 主配置文件
├── rag.yml                 # RAG 服务配置
├── Dockerfile              # 单阶段构建
├── Dockerfile.multi        # 多阶段构建
└── docker-compose.yml      # 开发用 Docker Compose
```

---

## 许可证

本项目基于 [LibreChat](https://github.com/danny-avila/LibreChat) 构建，遵循 LibreChat 的开源许可证。使用和分发时请保留 LibreChat 的许可证和上游声明。
