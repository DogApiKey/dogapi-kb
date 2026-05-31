# New API 部署指南

本指南详细介绍 New API 的各种部署方式、环境变量配置、生产环境优化和常见运维操作。

---

## 部署方式总览

| 部署方式 | 适用场景 | 难度 |
|---------|---------|------|
| Docker Compose | 推荐，适合大多数用户 | 简单 |
| Docker 命令 | 熟悉 Docker 的用户 | 简单 |
| 宝塔面板 | 使用宝塔管理服务器的用户 | 简单 |
| 多机部署 | 高可用、高并发生产环境 | 中等 |

---

## 方式一：Docker Compose 部署（推荐）

### 基本配置

```yaml
version: '3'
services:
  new-api:
    image: calciumion/new-api:latest
    container_name: new-api
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
    environment:
      - SESSION_SECRET=your_random_secret_here
      - TZ=Asia/Shanghai
```

### 使用 MySQL 的配置

```yaml
version: '3'
services:
  new-api:
    image: calciumion/new-api:latest
    container_name: new-api
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
    environment:
      - SQL_DSN=root:your_password@tcp(mysql_host:3306)/newapi
      - SESSION_SECRET=your_random_secret
      - CRYPTO_SECRET=your_crypto_secret
      - REDIS_CONN_STRING=redis://redis_host:6379
      - TZ=Asia/Shanghai
    depends_on:
      - mysql
      - redis

  mysql:
    image: mysql:8.0
    container_name: new-api-mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=your_password
      - MYSQL_DATABASE=newapi
    volumes:
      - mysql_data:/var/lib/mysql

  redis:
    image: redis:7-alpine
    container_name: new-api-redis
    restart: always
    volumes:
      - redis_data:/data

volumes:
  mysql_data:
  redis_data:
```

### 启动与管理

```bash
# 首次启动
docker-compose up -d

# 查看日志
docker-compose logs -f new-api

# 停止服务
docker-compose down

# 重启服务
docker-compose restart

# 更新版本
docker-compose down
docker pull calciumion/new-api:latest
docker-compose up -d
```

---

## 方式二：Docker 命令部署

### 使用 SQLite（零配置）

```bash
docker pull calciumion/new-api:latest

docker run --name new-api -d --restart always \
  -p 3000:3000 \
  -e TZ=Asia/Shanghai \
  -v ./data:/data \
  calciumion/new-api:latest
```

### 使用 MySQL

```bash
docker run --name new-api -d --restart always \
  -p 3000:3000 \
  -e SQL_DSN="root:your_password@tcp(mysql_host:3306)/newapi" \
  -e SESSION_SECRET=your_random_secret \
  -e CRYPTO_SECRET=your_crypto_secret \
  -e TZ=Asia/Shanghai \
  -v ./data:/data \
  calciumion/new-api:latest
```

### 使用 PostgreSQL

```bash
docker run --name new-api -d --restart always \
  -p 3000:3000 \
  -e SQL_DSN="postgres://user:password@postgres_host:5432/newapi?sslmode=disable" \
  -e SESSION_SECRET=your_random_secret \
  -e TZ=Asia/Shanghai \
  -v ./data:/data \
  calciumion/new-api:latest
```

> **数据路径说明：**
> - `./data:/data` 使用相对路径，数据保存在当前目录的 `data` 文件夹
> - 也可以使用绝对路径，例如 `/opt/new-api/data:/data`

---

## 方式三：宝塔面板部署

### 前置要求

| 项目 | 要求 |
|------|------|
| 宝塔面板 | >= 9.2.0 版本 |
| 推荐系统 | CentOS 7+、Ubuntu 18.04+、Debian 10+ |
| 服务器配置 | 至少 1 核 2GB 内存 |

### 安装步骤

1. **安装宝塔面板**
   - 前往 [宝塔面板官网](https://www.bt.cn/new/download.html) 下载安装脚本
   - 运行安装脚本完成安装
   - 使用提供的地址、用户名和密码登录

2. **安装 Docker 服务**
   - 登录宝塔面板后，在左侧菜单点击 **Docker**
   - 首次进入会提示安装 Docker，点击 **立即安装**

3. **安装 New API**
   - 在 Docker 功能中点击 **应用商店**
   - 搜索 **New-API**
   - 点击 **安装**
   - 配置容器名称、端口映射和环境变量
   - 点击 **确认** 开始安装

4. **验证安装**
   - 访问 `http://您的服务器IP:3000`

### 使用 Docker Compose（宝塔终端）

```bash
# 创建目录
mkdir -p /www/wwwroot/new-api
cd /www/wwwroot/new-api

# 创建 docker-compose.yml（参考上方配置）
nano docker-compose.yml

# 启动
docker-compose up -d
```

---

## 环境变量配置

### 核心配置

| 变量名 | 说明 | 是否必填 | 默认值 |
|--------|------|---------|--------|
| `SESSION_SECRET` | 会话加密密钥 | 多机部署必填 | - |
| `CRYPTO_SECRET` | 数据加密密钥 | 使用 Redis 时必填 | - |
| `SQL_DSN` | 数据库连接字符串 | 否（默认 SQLite） | - |
| `REDIS_CONN_STRING` | Redis 连接字符串 | 否 | - |
| `TZ` | 时区设置 | 推荐设置 | - |

### 数据库连接字符串格式

**MySQL：**
```
root:password@tcp(host:3306)/database_name
```

**PostgreSQL：**
```
postgres://user:password@host:5432/database_name?sslmode=disable
```

### Redis 连接字符串格式

```
redis://host:port
redis://:password@host:port
redis://host:port/db_number
```

### 网络与超时配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `RELAY_DIAL_TIMEOUT` | TCP 建连超时（秒） | `15` |
| `RELAY_DIAL_KEEPALIVE` | TCP keepalive 间隔（秒） | `15` |
| `RELAY_TLS_HANDSHAKE_TIMEOUT` | TLS 握手超时（秒） | `10` |
| `RELAY_RESPONSE_HEADER_TIMEOUT` | 等待上游响应头超时（秒） | `60` |
| `RELAY_IDLE_CONN_TIMEOUT` | 空闲连接关闭时间（秒） | `30` |
| `RELAY_EXPECT_CONTINUE_TIMEOUT` | 100-continue 等待超时（秒） | `1` |
| `RELAY_FORCE_ATTEMPT_HTTP2` | 是否启用 HTTP/2 | `false` |
| `STREAMING_TIMEOUT` | 流式响应超时（秒） | `300` |

### 请求限制配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `STREAM_SCANNER_MAX_BUFFER_MB` | 流式扫描器单行最大缓冲（MB） | `64` |
| `MAX_REQUEST_BODY_MB` | 请求体最大大小（MB，解压后计算） | `32` |

### Azure 配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `AZURE_DEFAULT_API_VERSION` | Azure API 版本 | `2025-04-01-preview` |

### 日志与监控配置

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `ERROR_LOG_ENABLED` | 错误日志开关 | `false` |
| `PYROSCOPE_URL` | Pyroscope 性能分析地址 | - |
| `PYROSCOPE_APP_NAME` | Pyroscope 应用名 | `new-api` |
| `PYROSCOPE_BASIC_AUTH_USER` | Pyroscope 认证用户名 | - |
| `PYROSCOPE_BASIC_AUTH_PASSWORD` | Pyroscope 认证密码 | - |

---

## 多机部署

当单台服务器无法满足需求时，可以部署多个 New API 实例。

### 架构说明

```
用户请求 -> 负载均衡器（Nginx） -> New API 实例 1
                                  -> New API 实例 2
                                  -> New API 实例 3
                                      |
                                      v
                                   共享 MySQL
                                   共享 Redis
```

### 必要配置

多机部署时，所有实例必须配置相同的环境变量：

```bash
# 所有实例必须相同
SESSION_SECRET=same_secret_for_all_instances
CRYPTO_SECRET=same_crypto_secret_for_all

# 共享数据库
SQL_DSN=root:password@tcp(mysql_host:3306)/newapi
REDIS_CONN_STRING=redis://redis_host:6379
```

### Nginx 负载均衡配置示例

```nginx
upstream new_api {
    server 192.168.1.10:3000 weight=1;
    server 192.168.1.11:3000 weight=1;
    server 192.168.1.12:3000 weight=1;
}

server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://new_api;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket 支持（Realtime API 需要）
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # 超时设置
        proxy_read_timeout 300s;
        proxy_send_timeout 300s;
    }
}
```

### 注意事项

> **警告：**
> - 必须设置 `SESSION_SECRET`，否则登录状态在不同实例间不一致
> - 使用 Redis 时必须设置 `CRYPTO_SECRET`，否则加密数据无法在不同实例间解密
> - 所有实例的 `SESSION_SECRET` 和 `CRYPTO_SECRET` 必须完全相同

---

## 缓存配置

### Redis 缓存（推荐）

配置 `REDIS_CONN_STRING` 环境变量启用 Redis 缓存：

```bash
REDIS_CONN_STRING=redis://localhost:6379
```

Redis 缓存适用于：
- 多机部署时的会话共享
- 数据缓存加速
- 分布式锁

### 内存缓存

如果不使用 Redis，可以启用内存缓存：

```bash
MEMORY_CACHE_ENABLED=true
```

> 注意：内存缓存仅适用于单实例部署，多机部署请使用 Redis。

---

## 数据库要求

| 数据库 | 最低版本 | 说明 |
|--------|---------|------|
| SQLite | - | 默认数据库，无需额外安装，Docker 需挂载 `/data` 目录 |
| MySQL | >= 5.7.8 | 推荐用于生产环境 |
| PostgreSQL | >= 9.6 | 生产环境可选 |

---

## 更新与维护

### 更新版本

```bash
# Docker Compose 方式
cd /path/to/new-api
docker-compose down
docker pull calciumion/new-api:latest
docker-compose up -d

# Docker 命令方式
docker stop new-api
docker rm new-api
docker pull calciumion/new-api:latest
# 重新执行 docker run 命令
```

### 数据备份

**SQLite 备份：**
```bash
# 备份
tar -czf new-api-backup-$(date +%Y%m%d).tar.gz ./data

# 恢复
tar -xzf new-api-backup-20240101.tar.gz
```

**MySQL 备份：**
```bash
# 备份
mysqldump -u root -p newapi > new-api-backup-$(date +%Y%m%d).sql

# 恢复
mysql -u root -p newapi < new-api-backup-20240101.sql
```

### 查看日志

```bash
# Docker Compose
docker-compose logs -f new-api

# Docker 命令
docker logs -f new-api

# 最近 100 行
docker logs --tail 100 new-api
```

---

## 故障排查

### 服务无法启动

1. 检查端口是否被占用：`lsof -i :3000`
2. 查看容器日志：`docker logs new-api`
3. 检查环境变量配置是否正确
4. 确认数据库连接是否正常

### API 调用返回错误

1. 检查渠道配置是否正确（API Key 是否有效）
2. 查看渠道状态是否启用
3. 检查网络连接是否正常
4. 查看错误日志：设置 `ERROR_LOG_ENABLED=true`

### 多机部署会话丢失

1. 确认所有实例设置了相同的 `SESSION_SECRET`
2. 确认 Redis 连接正常
3. 确认设置了 `CRYPTO_SECRET`

### 数据库连接失败

1. 检查 `SQL_DSN` 格式是否正确
2. 确认数据库服务正在运行
3. 检查数据库用户权限
4. 确认网络连通性

### 性能问题

1. 检查服务器资源使用情况（CPU、内存、磁盘）
2. 考虑使用 Redis 缓存替代内存缓存
3. 增加实例数量进行水平扩展
4. 调整超时配置参数
5. 启用 Pyroscope 进行性能分析

---

## 安全建议

1. **修改默认端口**：生产环境建议使用非默认端口
2. **启用 HTTPS**：通过 Nginx 反向代理配置 SSL 证书
3. **设置强密钥**：`SESSION_SECRET` 和 `CRYPTO_SECRET` 使用随机生成的强密码
4. **限制访问**：通过防火墙限制管理端口的访问来源
5. **定期备份**：配置自动备份策略
6. **开启错误日志**：设置 `ERROR_LOG_ENABLED=true` 便于排查问题
7. **监控告警**：配置服务器监控和告警机制

---

## 更多资源

- [官方文档](https://docs.newapi.pro/zh/docs)
- [环境变量完整配置](https://docs.newapi.pro/zh/docs/installation/config-maintenance/environment-variables)
- [API 接口文档](https://docs.newapi.pro/zh/docs/api)
- [常见问题 FAQ](https://docs.newapi.pro/zh/docs/support/faq)
- [社区交流](https://docs.newapi.pro/zh/docs/support/community-interaction)
- [问题反馈](https://github.com/QuantumNous/new-api/issues)
