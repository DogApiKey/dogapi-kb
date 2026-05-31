# New API 快速开始指南

## 项目简介

New API 是新一代大模型网关与 AI 资产管理系统，完全兼容原版 One API 数据库，提供现代化的用户界面和丰富的功能特性。本指南将帮助您在 5 分钟内完成部署并开始使用。

---

## 前置要求

| 组件 | 最低要求 |
|------|----------|
| 操作系统 | Linux（推荐 CentOS 7+、Ubuntu 18.04+、Debian 10+）、macOS、Windows |
| 内存 | 至少 1GB（推荐 2GB 以上） |
| Docker | 已安装 Docker 和 Docker Compose |
| 网络 | 服务器可访问外部 AI API 服务 |

---

## 方式一：Docker Compose 部署（推荐）

这是最简单的部署方式，适合大多数用户。

### 第一步：克隆项目

```bash
git clone https://github.com/QuantumNous/new-api.git
cd new-api
```

### 第二步：编辑配置文件

使用编辑器打开 `docker-compose.yml`，根据需要修改配置：

```bash
nano docker-compose.yml
```

基本配置示例：

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

### 第三步：启动服务

```bash
docker-compose up -d
```

### 第四步：访问系统

部署完成后，在浏览器中访问：

```
http://localhost:3000
```

首次访问时，系统会引导您完成初始配置。

---

## 方式二：Docker 命令部署

适合熟悉 Docker 命令的用户，无需克隆项目。

### 使用 SQLite（默认，零配置）

```bash
docker pull calciumion/new-api:latest

docker run --name new-api -d --restart always \
  -p 3000:3000 \
  -e TZ=Asia/Shanghai \
  -v ./data:/data \
  calciumion/new-api:latest
```

### 使用 MySQL（适合生产环境）

```bash
docker run --name new-api -d --restart always \
  -p 3000:3000 \
  -e SQL_DSN="root:your_password@tcp(your_mysql_host:3306)/newapi" \
  -e SESSION_SECRET=your_random_secret \
  -e TZ=Asia/Shanghai \
  -v ./data:/data \
  calciumion/new-api:latest
```

> **路径说明：** `-v ./data:/data` 将数据保存在当前目录的 `data` 文件夹中。您也可以使用绝对路径，例如 `-v /opt/new-api/data:/data`。

---

## 方式三：宝塔面板部署

适合使用宝塔面板管理服务器的用户。

### 前置条件

- 宝塔面板版本 >= 9.2.0
- 已在宝塔面板中安装 Docker 服务

### 操作步骤

1. 登录宝塔面板
2. 在左侧菜单找到并点击 **Docker**
3. 点击 **应用商店**
4. 搜索 **New-API**
5. 点击 **安装**，配置以下选项：
   - **容器名称**：默认 `new-api`，可自定义
   - **端口映射**：默认 `3000:3000`
   - **环境变量**：
     - `SESSION_SECRET`：会话密钥（必填）
     - `CRYPTO_SECRET`：加密密钥（使用 Redis 时必填）
6. 点击 **确认** 开始安装
7. 安装完成后访问 `http://您的服务器IP:3000`

---

## 初始配置

### 1. 登录系统

首次访问系统后，使用默认管理员账号登录（请在首次登录后立即修改密码）。

### 2. 添加渠道

渠道是连接 AI 模型服务的桥梁。添加渠道的步骤：

1. 进入 **控制台** -> **渠道** 页面
2. 点击 **添加渠道**
3. 选择渠道类型（如 OpenAI、Claude、Gemini 等）
4. 填写 API Key 和其他必要信息
5. 点击保存

### 3. 创建令牌

令牌用于应用程序访问 API：

1. 进入 **控制台** -> **令牌** 页面
2. 点击 **添加令牌**
3. 设置令牌名称、额度和关联模型
4. 复制生成的令牌用于 API 调用

### 4. 测试 API 调用

使用 curl 测试 API 是否正常工作：

```bash
curl http://localhost:3000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_token_here" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [
      {"role": "user", "content": "你好"}
    ]
  }'
```

---

## 生成随机密钥

在部署时需要设置 `SESSION_SECRET`，建议使用以下命令生成安全的随机密钥：

```bash
# 方法一：使用 openssl
openssl rand -hex 16

# 方法二：使用 Linux 内置命令
head -c 16 /dev/urandom | xxd -p
```

---

## 常见问题

### 无法访问 3000 端口？

1. 确认容器正在运行：`docker ps | grep new-api`
2. 检查防火墙是否放行 3000 端口
3. 如果使用云服务器，检查安全组规则
4. 宝塔面板用户请在 **安全** 中放行端口

### 登录后提示会话失效？

确保已设置 `SESSION_SECRET` 环境变量，且值不为空。多机部署时所有实例必须使用相同的 `SESSION_SECRET`。

### 数据如何备份？

SQLite 模式下，备份 `data` 目录即可：

```bash
# 备份
cp -r ./data ./data_backup_$(date +%Y%m%d)

# 恢复
cp -r ./data_backup_20240101 ./data
```

### 如何更新到最新版本？

```bash
# Docker Compose 方式
docker-compose down
docker pull calciumion/new-api:latest
docker-compose up -d

# Docker 命令方式
docker stop new-api
docker rm new-api
docker pull calciumion/new-api:latest
# 重新执行之前的 docker run 命令
```

---

## 下一步

- 查看 [功能特性](./features.md) 了解 New API 的完整功能
- 查看 [部署指南](./deployment.md) 了解高级部署配置和生产环境优化
- 访问 [官方文档](https://docs.newapi.pro/zh/docs) 获取更多详细信息
