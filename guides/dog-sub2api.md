# Sub2API 用户指南

## 简介

Sub2API 是一个 AI API 网关平台，用于将 AI 产品订阅的 API 配额分发给多个用户。管理员通过平台管理上游 AI 服务账号（如 Anthropic Claude、OpenAI、Google Gemini 等），用户则通过平台生成的 API Key 调用这些 AI 服务。平台负责处理鉴权、计费、负载均衡和请求转发。

**典型使用场景：**
- 团队内部共享 AI 服务订阅配额
- 将多个 AI 订阅账号的配额统一管理并分发
- 对 AI API 用量进行精确计费和成本控制
- 为多个上游账号实现智能负载均衡

**官方域名：** `sub2api.org` 和 `pincc.ai`，其他使用 Sub2API 名义的网站可能为第三方部署。

**在线体验：** https://demo.sub2api.org/ （账号 `admin@sub2api.org` / `admin123`）

---

## 核心功能

| 功能 | 说明 |
|------|------|
| 多账号管理 | 支持 OAuth、API Key 等多种上游账号类型 |
| API Key 分发 | 为用户生成和管理 API Key |
| 精确计费 | Token 级别的用量追踪和成本计算 |
| 智能调度 | 智能账号选择，支持粘性会话（同一会话固定到同一账号） |
| 并发控制 | 用户级和账号级并发限制 |
| 速率限制 | 可配置的请求和 Token 速率限制 |
| 内置支付系统 | 支持易支付、支付宝、微信支付、Stripe 用户自助充值 |
| 管理后台 | Web 界面进行监控和管理 |

---

## 技术栈

| 组件 | 技术 |
|------|------|
| 后端 | Go 1.25.7, Gin, Ent |
| 前端 | Vue 3.4+, Vite 5+, TailwindCSS |
| 数据库 | PostgreSQL 15+ |
| 缓存/队列 | Redis 7+ |

---

## 部署指南

### 方式一：脚本安装（推荐）

适用于已有 PostgreSQL 和 Redis 的 Linux 服务器。

**前置条件：**
- Linux 服务器（amd64 或 arm64）
- PostgreSQL 15+（已安装并运行）
- Redis 7+（已安装并运行）
- Root 权限

**安装：**

```bash
curl -sSL https://raw.githubusercontent.com/Wei-Shaw/sub2api/main/deploy/install.sh | sudo bash
```

脚本会自动检测系统架构、下载最新版本、安装到 `/opt/sub2api`、创建 systemd 服务。

**安装后操作：**

```bash
# 启动服务
sudo systemctl start sub2api

# 设置开机自启
sudo systemctl enable sub2api

# 浏览器打开设置向导
# http://你的服务器IP:8080
```

设置向导将引导完成数据库配置、Redis 配置和管理员账号创建。

**常用命令：**

```bash
sudo systemctl status sub2api      # 查看状态
sudo journalctl -u sub2api -f      # 查看日志
sudo systemctl restart sub2api     # 重启服务
```

**卸载：**

```bash
curl -sSL https://raw.githubusercontent.com/Wei-Shaw/sub2api/main/deploy/install.sh | sudo bash -s -- uninstall -y
```

---

### 方式二：Docker Compose（推荐）

使用 Docker Compose 部署，自动包含 PostgreSQL 和 Redis 容器。

**前置条件：**
- Docker 20.10+
- Docker Compose v2+

**一键部署：**

```bash
mkdir -p sub2api-deploy && cd sub2api-deploy
curl -sSL https://raw.githubusercontent.com/Wei-Shaw/sub2api/main/deploy/docker-deploy.sh | bash
docker compose up -d
docker compose logs -f sub2api
```

部署脚本会自动生成安全凭证（JWT_SECRET、TOTP_ENCRYPTION_KEY、POSTGRES_PASSWORD）并创建 `.env` 文件。

**手动部署：**

```bash
git clone https://github.com/Wei-Shaw/sub2api.git
cd sub2api/deploy
cp .env.example .env
nano .env   # 编辑配置
mkdir -p data postgres_data redis_data
docker compose -f docker-compose.local.yml up -d
```

**`.env` 必须配置项：**

```bash
POSTGRES_PASSWORD=your_secure_password_here   # PostgreSQL 密码（必需）
JWT_SECRET=your_jwt_secret_here               # JWT 密钥（推荐，保持登录状态）
TOTP_ENCRYPTION_KEY=your_totp_key_here        # TOTP 加密密钥（推荐，保留双因素认证）
ADMIN_EMAIL=admin@example.com                 # 管理员邮箱（可选）
ADMIN_PASSWORD=your_admin_password            # 管理员密码（可选）
SERVER_PORT=8080                              # 服务端口（可选）
```

**生成安全密钥：**

```bash
openssl rand -hex 32   # 用于 JWT_SECRET、TOTP_ENCRYPTION_KEY、POSTGRES_PASSWORD
```

**部署版本对比：**

| 版本 | 数据存储 | 迁移便利性 | 适用场景 |
|------|---------|-----------|---------|
| `docker-compose.local.yml` | 本地目录 | 简单（打包整个目录） | 生产环境、频繁备份 |
| `docker-compose.yml` | 命名卷 | 需 docker 命令 | 简单设置 |

推荐使用 `docker-compose.local.yml` 以便于数据管理和迁移。

**访问：** 浏览器打开 `http://你的服务器IP:8080`

如管理员密码自动生成，在日志中查找：

```bash
docker compose -f docker-compose.local.yml logs sub2api | grep "admin password"
```

**升级：**

```bash
docker compose -f docker-compose.local.yml pull
docker compose -f docker-compose.local.yml up -d
```

**迁移（本地目录版）：**

```bash
# 源服务器
docker compose -f docker-compose.local.yml down
cd ..
tar czf sub2api-complete.tar.gz sub2api-deploy/

# 传输到新服务器
scp sub2api-complete.tar.gz user@new-server:/path/

# 新服务器
tar xzf sub2api-complete.tar.gz
cd sub2api-deploy/
docker compose -f docker-compose.local.yml up -d
```

---

### 方式三：源码编译

适用于开发或定制需求。

**前置条件：**
- Go 1.21+
- Node.js 18+
- PostgreSQL 15+
- Redis 7+

**编译步骤：**

```bash
git clone https://github.com/Wei-Shaw/sub2api.git
cd sub2api

# 编译前端
npm install -g pnpm
cd frontend && pnpm install && pnpm run build

# 编译后端（嵌入前端）
cd ../backend
go build -tags embed -o sub2api ./cmd/server

# 配置并运行
cp ../deploy/config.example.yaml ./config.yaml
nano config.yaml
./sub2api
```

> `-tags embed` 参数会将前端嵌入到二进制文件中，不使用此参数编译的程序将不包含前端界面。

---

## Nginx 反向代理配置

通过 Nginx 反向代理时，需要在 `http` 块中添加：

```nginx
underscores_in_headers on;
```

Nginx 默认会丢弃名称中含下划线的请求头（如 `session_id`），这会导致多账号环境下的粘性会话功能失效。

---

## 配置说明

配置文件位于 `/etc/sub2api/config.yaml`（脚本安装）或 `deploy/config.example.yaml`（源码编译）。Docker 部署通过 `.env` 文件配置关键参数。

### 服务器配置

```yaml
server:
  host: "0.0.0.0"          # 绑定地址
  port: 8080                # 监听端口
  mode: "release"           # 运行模式：debug（开发）/ release（生产）
  frontend_url: ""          # 前端基础地址，用于生成邮件中的外部链接
  trusted_proxies: []       # 信任的代理地址（CIDR/IP 格式）
  max_request_body_size: 268435456  # 最大请求体大小（字节，默认 256MB）
```

### 数据库配置

```yaml
database:
  host: "localhost"
  port: 5432
  user: "postgres"
  password: "your_secure_password_here"
  dbname: "sub2api"
  sslmode: "prefer"         # disable / prefer / require / verify-ca / verify-full
  max_open_conns: 256       # 最大打开连接数
  max_idle_conns: 128       # 最大空闲连接数
```

### Redis 配置

```yaml
redis:
  host: "localhost"
  port: 6379
  password: ""
  db: 0
  pool_size: 1024           # 连接池大小
  min_idle_conns: 128       # 最小空闲连接数
  enable_tls: false
```

### JWT 配置

```yaml
jwt:
  secret: "change-this-to-a-secure-random-string"   # 生产环境务必更改！
  expire_hour: 24            # 令牌过期时间（小时，最大 168）
  access_token_expire_minutes: 0  # Access Token 过期时间（分钟，>0 时优先于 expire_hour）
```

### TOTP 双因素认证

```yaml
totp:
  encryption_key: ""   # 建议设置固定密钥，否则重启后 2FA 配置失效
                        # 生成命令：openssl rand -hex 32
```

### 默认设置

```yaml
default:
  admin_email: "admin@example.com"     # 初始管理员邮箱
  admin_password: "admin123"           # 初始管理员密码
  user_concurrency: 5                  # 每用户最大并发请求数
  user_balance: 0                      # 新用户初始余额
  api_key_prefix: "sk-"               # API Key 前缀
  rate_multiplier: 1.0                 # 费率倍数
```

### 网关配置

```yaml
gateway:
  response_header_timeout: 600         # 等待上游响应头超时（秒）
  max_body_size: 268435456             # 请求体最大字节数
  upstream_response_read_max_bytes: 8388608  # 非流式上游响应读取上限（默认 8MB）
  stream_data_interval_timeout: 180    # 流数据间隔超时（秒）
  stream_keepalive_interval: 10        # 流式 keepalive 间隔（秒）
  connection_pool_isolation: "account_proxy"  # 连接池隔离策略
```

### 安全配置

```yaml
security:
  url_allowlist:
    enabled: false                     # 启用 URL 白名单验证
    upstream_hosts:                    # 允许代理的上游主机
      - "api.openai.com"
      - "api.anthropic.com"
      - "generativelanguage.googleapis.com"
    allow_insecure_http: true          # 允许 HTTP URL（仅开发环境）
    allow_private_hosts: true          # 允许私有 IP 地址
  response_headers:
    enabled: true                      # 启用响应头过滤
  csp:
    enabled: true                      # 启用 CSP 响应头
```

### CORS 配置

```yaml
cors:
  allowed_origins: []                  # 允许的来源列表，留空则禁用跨域
  allow_credentials: true              # 允许携带凭证
```

### 计费熔断器

```yaml
billing:
  circuit_breaker:
    enabled: true
    failure_threshold: 5               # 触发熔断的失败次数
    reset_timeout_seconds: 30          # 熔断后重试等待时间
    half_open_requests: 3              # 半开状态允许通过的请求数
```

### 速率限制

```yaml
rate_limit:
  overload_cooldown_minutes: 10        # 上游返回 529 时的冷却时间（分钟）
```

### 日志配置

```yaml
log:
  level: "info"                        # debug / info / warn / error
  format: "console"                    # json / console
  output:
    to_stdout: true
    to_file: true
  rotation:
    max_size_mb: 100
    max_backups: 10
    max_age_days: 7
    compress: true
```

### 定价数据源

```yaml
pricing:
  remote_url: "https://raw.githubusercontent.com/Wei-Shaw/model-price-repo/..."
  update_interval_hours: 24
  data_dir: "./data"
```

### 在线更新代理

```yaml
update:
  proxy_url: ""   # 支持 http、https、socks5、socks5h
                   # 示例："http://127.0.0.1:7890"
                   # 留空表示直连（适用于海外服务器）
```

### OAuth SSO 登录

Sub2API 支持 LinuxDo Connect 和通用 OIDC 两种 OAuth SSO 登录方式。

**LinuxDo Connect：**

```yaml
linuxdo_connect:
  enabled: false
  client_id: ""
  client_secret: ""
  redirect_url: "https://your-domain.com/api/v1/auth/oauth/linuxdo/callback"
  frontend_redirect_url: "/auth/linuxdo/callback"
  use_pkce: true
```

**通用 OIDC：**

```yaml
oidc_connect:
  enabled: false
  provider_name: "OIDC"
  client_id: ""
  client_secret: ""
  issuer_url: "https://keycloak.example.com/realms/myrealm"
  scopes: "openid email profile"
  redirect_url: "https://your-domain.com/api/v1/auth/oauth/oidc/callback"
  validate_id_token: true
```

### Gemini OAuth 配置

Sub2API 支持两种 Gemini OAuth 模式：

1. **Code Assist OAuth** - 需要 GCP project_id，使用 `cloudcode-pa.googleapis.com`
2. **AI Studio OAuth** - 不需要 project_id，使用 `generativelanguage.googleapis.com`

```yaml
gemini:
  oauth:
    client_id: ""        # 留空则使用 Gemini CLI 内置 OAuth Client
    client_secret: ""
    scopes: ""
```

### Turnstile 人机验证

```yaml
turnstile:
  required: false   # 在 release 模式下是否要求 Turnstile 验证
```

---

## 简易模式

简易模式适合个人开发者或内部团队快速使用，不依赖完整 SaaS 功能。

**启用方式：** 设置环境变量 `RUN_MODE=simple`

**功能差异：** 隐藏 SaaS 相关功能，跳过计费流程

**安全注意事项：** 生产环境需同时设置 `SIMPLE_MODE_CONFIRM=true` 才允许启动

---

## 支付系统配置

Sub2API 内置支付系统，支持用户自助充值，无需部署独立的支付服务。

### 支持的支付方式

| 服务商 | 支付方式 | 说明 |
|--------|---------|------|
| EasyPay（易支付） | 支付宝、微信支付 | 兼容易支付协议的第三方聚合支付 |
| 支付宝官方 | 二维码扫码、移动端跳转 | 直接对接支付宝开放平台 |
| 微信官方 | Native 扫码、H5、公众号/JSAPI | 直接对接微信支付 APIv3 |
| Stripe | 银行卡、支付宝、微信等 | 国际支付，支持多币种 |

### 快速开始

1. 进入管理后台 -> 设置 -> 支付设置
2. 开启"启用支付"
3. 配置基本参数（金额范围、超时时间等）
4. 在"服务商管理"中添加至少一个服务商实例
5. 用户即可在前端页面进行充值

### 系统设置参数

| 设置项 | 说明 | 默认值 |
|--------|------|--------|
| 启用支付 | 启用或禁用支付系统 | 关闭 |
| 商品名前缀/后缀 | 支付页面显示的商品名 | - |
| 最低金额 | 单笔最低充值金额 | 1 |
| 最高金额 | 单笔最高充值金额（留空不限制） | - |
| 每日限额 | 每用户每日累计充值上限 | - |
| 订单超时时间 | 订单超时分钟数 | 30 |
| 最大待支付订单数 | 同一用户最大并行待支付订单数 | 3 |
| 负载均衡策略 | 多服务商实例时的选择策略 | 轮询 |

### 回调地址格式

添加服务商时系统自动根据站点域名拼接回调地址：

| 服务商 | 回调路径 |
|--------|---------|
| EasyPay | `https://your-domain.com/api/v1/payment/webhook/easypay` |
| 支付宝官方 | `https://your-domain.com/api/v1/payment/webhook/alipay` |
| 微信官方 | `https://your-domain.com/api/v1/payment/webhook/wxpay` |
| Stripe | `https://your-domain.com/api/v1/payment/webhook/stripe` |

---

## Antigravity 支持

Sub2API 支持 Antigravity 账户，授权后可通过专用端点访问 Claude 和 Gemini 模型。

### 专用端点

| 端点 | 模型 |
|------|------|
| `/antigravity/v1/messages` | Claude 模型 |
| `/antigravity/v1beta/` | Gemini 模型 |

### Claude Code 配置示例

```bash
export ANTHROPIC_BASE_URL="http://localhost:8080/antigravity"
export ANTHROPIC_AUTH_TOKEN="sk-xxx"
```

### 混合调度模式

Antigravity 账户支持可选的混合调度功能。开启后，通用端点 `/v1/messages` 和 `/v1beta/` 也会调度该账户。

> **注意：** Anthropic Claude 和 Antigravity Claude 不能在同一上下文中混合使用，请通过分组功能做好隔离。

### 已知问题

在 Claude Code 中无法自动退出 Plan Mode。解决办法：按 `Shift + Tab` 手动退出 Plan Mode，然后输入内容告诉 Claude Code 同意或拒绝 Plan。

---

## 安全注意事项

### HTTP URL 配置

当 `security.url_allowlist.enabled=false` 时，系统默认拒绝 HTTP URL，仅允许 HTTPS。如需允许 HTTP（仅限开发/测试环境）：

```yaml
security:
  url_allowlist:
    enabled: false
    allow_insecure_http: true   # 不安全，仅用于开发/测试
```

### 网络层防护建议

如关闭 URL 校验或响应头过滤，建议加强网络层防护：
- 出站访问白名单限制上游域名/IP
- 阻断私网/回环/链路本地地址
- 强制仅允许 TLS 出站
- 在反向代理层移除敏感响应头

### 关键密钥

生产环境中务必修改以下配置：
- `jwt.secret` - 用于签发用户令牌
- `totp.encryption_key` - 用于加密双因素认证密钥
- `database.password` - 数据库密码

---

## 项目结构

```
sub2api/
├── backend/                  # Go 后端服务
│   ├── cmd/server/           # 应用入口
│   ├── internal/             # 内部模块
│   │   ├── config/           # 配置管理
│   │   ├── model/            # 数据模型
│   │   ├── service/          # 业务逻辑
│   │   ├── handler/          # HTTP 处理器
│   │   └── gateway/          # API 网关核心
│   └── resources/            # 静态资源
├── frontend/                 # Vue 3 前端
│   └── src/
│       ├── api/              # API 调用
│       ├── stores/           # 状态管理
│       ├── views/            # 页面组件
│       └── components/       # 通用组件
└── deploy/                   # 部署文件
    ├── docker-compose.yml
    ├── .env.example
    ├── config.example.yaml
    └── install.sh
```

---

## 生态项目

| 项目 | 说明 |
|------|------|
| sub2api-mobile | 移动端管理控制台，跨平台应用（iOS/Android/Web），支持用户管理、账号管理、监控看板 |

---

## 免责声明

- 使用本项目可能违反 Anthropic 的服务条款，请在使用前仔细阅读相关用户协议
- 本项目仅供技术学习和研究使用，作者不对因使用本项目导致的账户封禁、服务中断或其他损失承担任何责任
- 所有风险由用户自行承担

---

## 许可证

本项目基于 GNU 宽通用公共许可证 v3.0（或更高版本）授权。
