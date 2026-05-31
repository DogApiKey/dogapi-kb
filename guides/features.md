# New API 功能特性

New API 是新一代大模型网关与 AI 资产管理系统，提供全面的 AI 模型管理、计费、路由和安全功能。以下是完整的功能特性说明。

---

## 核心功能

### 现代化用户界面

- 全新设计的 Web 管理界面，操作直观
- 数据看板（Dashboard）提供可视化统计分析
- 实时监控 API 调用量、Token 消耗和费用

### 多语言支持

系统支持以下语言：
- 简体中文
- 繁体中文
- English（英语）
- Francais（法语）
- 日本語（日语）

### 数据兼容性

- 完全兼容原版 One API 数据库
- 支持从 One API 无缝迁移
- 支持 SQLite、MySQL（>= 5.7.8）、PostgreSQL（>= 9.6）

### 权限管理

- 用户角色分级管理
- 令牌分组与模型限制
- 细粒度的访问控制

---

## 支持的 AI 模型

### 大语言模型（LLM）

| 模型提供商 | 格式支持 | 说明 |
|-----------|---------|------|
| OpenAI | OpenAI Compatible / Responses | GPT-4o、GPT-5、o3-mini 等 |
| Anthropic Claude | Claude Messages | Claude 3.5 Sonnet、Claude 3.7 Sonnet 等 |
| Google Gemini | Gemini 原生格式 | Gemini 2.5 Pro、Gemini 2.5 Flash 等 |
| DeepSeek | OpenAI Compatible | DeepSeek-V3、DeepSeek-R1 等 |
| Azure OpenAI | OpenAI Compatible | 通过 Azure 部署的 OpenAI 模型 |
| 其他兼容模型 | OpenAI Compatible | 任何兼容 OpenAI 格式的服务 |

### 多模态与创意模型

| 模型类型 | 支持的服务 | 说明 |
|---------|-----------|------|
| 图像生成 | Midjourney-Proxy(Plus) | 支持 MJ 图像生成接口 |
| 音乐生成 | Suno API | 支持 AI 音乐创作 |
| 图像生成 | DALL-E | 通过 OpenAI 图像接口 |

### 专用模型

| 模型类型 | 支持的服务 | 说明 |
|---------|-----------|------|
| 文本嵌入 | OpenAI Embeddings | 向量化文本表示 |
| 重排序 | Cohere、Jina | Rerank 模型支持 |
| 语音合成 | OpenAI TTS | 文本转语音 |
| 语音识别 | OpenAI Whisper | 语音转文本 |
| 实时对话 | OpenAI Realtime API | 含 Azure 支持 |
| 自定义 | 完整调用地址 | 支持任意自定义接口 |

---

## API 格式转换

New API 提供强大的格式转换能力，让您可以使用一种格式的客户端调用另一种格式的模型。

### 支持的转换

| 转换方向 | 状态 | 说明 |
|---------|------|------|
| OpenAI Compatible <-> Claude Messages | 已支持 | 双向转换 |
| OpenAI Compatible -> Google Gemini | 已支持 | 单向转换 |
| Google Gemini -> OpenAI Compatible | 已支持 | 仅文本，暂不支持函数调用 |
| OpenAI Compatible <-> OpenAI Responses | 开发中 | 即将支持 |

### 思考转内容功能

支持将模型的思考过程（reasoning_content）转换为 `<think>` 标签拼接到内容中返回，方便客户端展示。

配置方式：在渠道额外设置中启用 `thinking_to_content`：

```json
{
    "thinking_to_content": true
}
```

---

## Reasoning Effort 支持

New API 支持通过模型名称后缀控制推理力度。

### OpenAI 系列模型

| 模型名称 | 推理力度 |
|---------|---------|
| `o3-mini-high` | 高 |
| `o3-mini-medium` | 中 |
| `o3-mini-low` | 低 |
| `gpt-5-high` | 高 |
| `gpt-5-medium` | 中 |
| `gpt-5-low` | 低 |

### Claude 思考模型

| 模型名称 | 说明 |
|---------|------|
| `claude-3-7-sonnet-20250219-thinking` | 启用思考模式 |

### Google Gemini 系列模型

| 模型名称 | 说明 |
|---------|------|
| `gemini-2.5-flash-thinking` | 启用思考模式 |
| `gemini-2.5-flash-nothinking` | 禁用思考模式 |
| `gemini-2.5-pro-thinking` | 启用思考模式 |
| `gemini-2.5-pro-thinking-128` | 启用思考模式，思考预算 128 tokens |

您也可以在任意 Gemini 模型名称后追加 `-low`、`-medium` 或 `-high` 来控制推理力度，无需额外设置思考预算后缀。

---

## 智能路由

### 渠道加权随机

支持为多个渠道设置不同权重，系统根据权重随机选择渠道，实现负载均衡。

### 失败自动重试

当某个渠道调用失败时，系统自动切换到其他可用渠道重试。重试次数可在管理后台配置：

**配置路径：** 设置 -> 运营设置 -> 通用设置 -> 失败重试次数

### 用户级别模型限流

支持按用户设置模型调用频率限制，防止单个用户过度消耗资源。

---

## 支付与计费

### 在线充值

支持以下支付方式：
- **易支付（EPay）**：国内常用支付网关
- **Stripe**：国际信用卡支付

### 计费模式

- **按次计费**：按模型调用次数收费
- **按量计费**：按 Token 消耗量收费
- **缓存计费**：支持 OpenAI、Azure、DeepSeek、Claude、Qwen 等所有支持的模型的缓存计费
- **灵活策略**：可针对不同模型和用户组配置不同的计费策略

---

## 授权与安全

### 第三方登录

| 登录方式 | 说明 |
|---------|------|
| Discord | Discord OAuth 授权登录 |
| LinuxDO | LinuxDO 社区授权登录 |
| Telegram | Telegram Bot 授权登录 |
| OIDC | 统一身份认证（支持对接企业 SSO） |

### 安全特性

- 会话加密（SESSION_SECRET）
- 数据加密存储（CRYPTO_SECRET，使用 Redis 时必须）
- API Key 额度查询（配合 neko-api-key-tool）

---

## 支持的 API 接口

New API 提供以下标准 API 接口：

| 接口 | 说明 | 文档链接 |
|------|------|---------|
| Chat Completions | 聊天对话接口 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/chat/openai/createchatcompletion) |
| Responses | OpenAI Responses 格式 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/chat/openai/createresponse) |
| Image | 图像生成接口 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/images/openai/post-v1-images-generations) |
| Audio | 音频处理接口（TTS/STT） | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/audio/openai/create-transcription) |
| Embeddings | 文本嵌入接口 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/embeddings/createembedding) |
| Rerank | 重排序接口 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/rerank/creatererank) |
| Realtime | 实时对话接口 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/realtime/createrealtimesession) |
| Claude Chat | Claude 消息接口 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/chat/createmessage) |
| Gemini Chat | Google Gemini 接口 | [文档](https://docs.newapi.pro/zh/docs/api/ai-model/chat/gemini/geminirelayv1beta) |

---

## 渠道额外设置

渠道支持通过 JSON 配置额外参数：

| 参数 | 类型 | 说明 |
|------|------|------|
| `force_format` | 布尔值 | 是否强制格式化为 OpenAI 格式 |
| `proxy` | 字符串 | 网络代理地址（如 `socks5://proxy:1080`） |
| `thinking_to_content` | 布尔值 | 是否将思考内容转换为 `<think>` 标签返回 |

配置示例：

```json
{
    "force_format": true,
    "thinking_to_content": true,
    "proxy": "socks5://your-proxy:1080"
}
```

---

## 配套工具

| 工具 | 说明 |
|------|------|
| [neko-api-key-tool](https://github.com/Calcium-Ion/neko-api-key-tool) | API Key 额度查询工具，方便用户查看 Key 使用情况 |
| [new-api-horizon](https://github.com/Calcium-Ion/new-api-horizon) | New API 高性能优化版，适合高并发场景 |
| [Cherry Studio](https://www.cherry-ai.com/) | 推荐的桌面客户端 |
| [Aion UI](https://github.com/iOfficeAI/AionUi/) | 推荐的 Web 客户端 |
