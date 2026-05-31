# DogAPI 配置选项与系统常量参考手册

本文档整理自 DogAPI 项目的 `constant/` 和 `common/` 目录下的配置文件，涵盖渠道类型、API 类型、端点类型、环境变量、系统配置等全部常量定义。

---

## 1. 渠道类型（ChannelType）

渠道类型用于标识不同的上游 API 服务商。每个渠道类型对应一个唯一整数值和默认 Base URL。

| ID | 常量名 | 名称 | 默认 Base URL | 说明 |
|----|--------|------|---------------|------|
| 0 | `ChannelTypeUnknown` | Unknown | - | 未知渠道 |
| 1 | `ChannelTypeOpenAI` | OpenAI | `https://api.openai.com` | OpenAI 官方 API |
| 2 | `ChannelTypeMidjourney` | Midjourney | `https://oa.api2d.net` | Midjourney 代理 |
| 3 | `ChannelTypeAzure` | Azure | - | Azure OpenAI 服务 |
| 4 | `ChannelTypeOllama` | Ollama | `http://localhost:11434` | 本地 Ollama 服务 |
| 5 | `ChannelTypeMidjourneyPlus` | MidjourneyPlus | `https://api.openai-sb.com` | Midjourney Plus 代理 |
| 6 | `ChannelTypeOpenAIMax` | OpenAIMax | `https://api.openaimax.com` | OpenAI Max 代理 |
| 7 | `ChannelTypeOhMyGPT` | OhMyGPT | `https://api.ohmygpt.com` | OhMyGPT 代理 |
| 8 | `ChannelTypeCustom` | Custom | - | 自定义渠道 |
| 9 | `ChannelTypeAILS` | AILS | `https://api.caipacity.com` | AILS 服务 |
| 10 | `ChannelTypeAIProxy` | AIProxy | `https://api.aiproxy.io` | AIProxy 代理 |
| 11 | `ChannelTypePaLM` | PaLM | - | Google PaLM API |
| 12 | `ChannelTypeAPI2GPT` | API2GPT | `https://api.api2gpt.com` | API2GPT 代理 |
| 13 | `ChannelTypeAIGC2D` | AIGC2D | `https://api.aigc2d.com` | AIGC2D 代理 |
| 14 | `ChannelTypeAnthropic` | Anthropic | `https://api.anthropic.com` | Anthropic Claude API |
| 15 | `ChannelTypeBaidu` | Baidu | `https://aip.baidubce.com` | 百度文心一言 |
| 16 | `ChannelTypeZhipu` | Zhipu | `https://open.bigmodel.cn` | 智谱 ChatGLM |
| 17 | `ChannelTypeAli` | Ali | `https://dashscope.aliyuncs.com` | 阿里通义千问 |
| 18 | `ChannelTypeXunfei` | Xunfei | - | 讯飞星火 |
| 19 | `ChannelType360` | 360 | `https://api.360.cn` | 360 智脑 |
| 20 | `ChannelTypeOpenRouter` | OpenRouter | `https://openrouter.ai/api` | OpenRouter 聚合 |
| 21 | `ChannelTypeAIProxyLibrary` | AIProxyLibrary | `https://api.aiproxy.io` | AIProxy 库 |
| 22 | `ChannelTypeFastGPT` | FastGPT | `https://fastgpt.run/api/openapi` | FastGPT 知识库 |
| 23 | `ChannelTypeTencent` | Tencent | `https://hunyuan.tencentcloudapi.com` | 腾讯混元 |
| 24 | `ChannelTypeGemini` | Gemini | `https://generativelanguage.googleapis.com` | Google Gemini |
| 25 | `ChannelTypeMoonshot` | Moonshot | `https://api.moonshot.cn` | 月之暗面 Kimi |
| 26 | `ChannelTypeZhipu_v4` | ZhipuV4 | `https://open.bigmodel.cn` | 智谱 V4 版本 |
| 27 | `ChannelTypePerplexity` | Perplexity | `https://api.perplexity.ai` | Perplexity AI |
| 31 | `ChannelTypeLingYiWanWu` | LingYiWanWu | `https://api.lingyiwanwu.com` | 零一万物 |
| 33 | `ChannelTypeAws` | AWS | - | AWS Bedrock |
| 34 | `ChannelTypeCohere` | Cohere | `https://api.cohere.ai` | Cohere AI |
| 35 | `ChannelTypeMiniMax` | MiniMax | `https://api.minimax.chat` | MiniMax |
| 36 | `ChannelTypeSunoAPI` | SunoAPI | - | Suno 音乐生成 |
| 37 | `ChannelTypeDify` | Dify | `https://api.dify.ai` | Dify 平台 |
| 38 | `ChannelTypeJina` | Jina | `https://api.jina.ai` | Jina AI（Rerank） |
| 39 | `ChannelCloudflare` | Cloudflare | `https://api.cloudflare.com` | Cloudflare Workers AI |
| 40 | `ChannelTypeSiliconFlow` | SiliconFlow | `https://api.siliconflow.cn` | 硅基流动 |
| 41 | `ChannelTypeVertexAi` | VertexAI | - | Google Vertex AI |
| 42 | `ChannelTypeMistral` | Mistral | `https://api.mistral.ai` | Mistral AI |
| 43 | `ChannelTypeDeepSeek` | DeepSeek | `https://api.deepseek.com` | DeepSeek |
| 44 | `ChannelTypeMokaAI` | MokaAI | `https://api.moka.ai` | Moka AI |
| 45 | `ChannelTypeVolcEngine` | VolcEngine | `https://ark.cn-beijing.volces.com` | 火山引擎（豆包） |
| 46 | `ChannelTypeBaiduV2` | BaiduV2 | `https://qianfan.baidubce.com` | 百度千帆 V2 |
| 47 | `ChannelTypeXinference` | Xinference | - | Xinference 本地推理 |
| 48 | `ChannelTypeXai` | xAI | `https://api.x.ai` | xAI (Grok) |
| 49 | `ChannelTypeCoze` | Coze | `https://api.coze.cn` | Coze 智能体 |
| 50 | `ChannelTypeKling` | Kling | `https://api.klingai.com` | 快手可灵（视频） |
| 51 | `ChannelTypeJimeng` | Jimeng | `https://visual.volcengineapi.com` | 即梦（图像） |
| 52 | `ChannelTypeVidu` | Vidu | `https://api.vidu.cn` | Vidu（视频） |
| 53 | `ChannelTypeSubmodel` | Submodel | `https://llm.submodel.ai` | Submodel |
| 54 | `ChannelTypeDoubaoVideo` | DoubaoVideo | `https://ark.cn-beijing.volces.com` | 豆包视频 |
| 55 | `ChannelTypeSora` | Sora | `https://api.openai.com` | OpenAI Sora 视频 |
| 56 | `ChannelTypeReplicate` | Replicate | `https://api.replicate.com` | Replicate |
| 57 | `ChannelTypeCodex` | Codex | `https://chatgpt.com` | OpenAI Codex |

---

## 2. 特殊渠道 Base URL 配置

部分渠道支持专属编程计划，提供独立的 Claude 和 OpenAI 端点。

| 计划名称 | Claude Base URL | OpenAI Base URL |
|----------|-----------------|-----------------|
| `glm-coding-plan` | `https://open.bigmodel.cn/api/anthropic` | `https://open.bigmodel.cn/api/coding/paas/v4` |
| `glm-coding-plan-international` | `https://api.z.ai/api/anthropic` | `https://api.z.ai/api/coding/paas/v4` |
| `kimi-coding-plan` | `https://api.kimi.com/coding` | `https://api.kimi.com/coding/v1` |
| `doubao-coding-plan` | `https://ark.cn-beijing.volces.com/api/coding` | `https://ark.cn-beijing.volces.com/api/coding/v3` |

---

## 3. API 类型（APIType）

API 类型标识了与上游通信时使用的协议/SDK 类型。

| 常量值 | 名称 | 说明 |
|--------|------|------|
| 0 | `APITypeOpenAI` | OpenAI API 协议（默认） |
| 1 | `APITypeAnthropic` | Anthropic Claude API 协议 |
| 2 | `APITypePaLM` | Google PaLM API |
| 3 | `APITypeBaidu` | 百度文心 API |
| 4 | `APITypeZhipu` | 智谱 ChatGLM API |
| 5 | `APITypeAli` | 阿里通义千问 API |
| 6 | `APITypeXunfei` | 讯飞星火 API |
| 7 | `APITypeAIProxyLibrary` | AIProxy 库协议 |
| 8 | `APITypeTencent` | 腾讯混元 API |
| 9 | `APITypeGemini` | Google Gemini API |
| 10 | `APITypeZhipuV4` | 智谱 V4 API |
| 11 | `APITypeOllama` | Ollama 本地 API |
| 12 | `APITypePerplexity` | Perplexity API |
| 13 | `APITypeAws` | AWS Bedrock API |
| 14 | `APITypeCohere` | Cohere API |
| 15 | `APITypeDify` | Dify 平台 API |
| 16 | `APITypeJina` | Jina AI API |
| 17 | `APITypeCloudflare` | Cloudflare Workers AI API |
| 18 | `APITypeSiliconFlow` | 硅基流动 API |
| 19 | `APITypeVertexAi` | Google Vertex AI API |
| 20 | `APITypeMistral` | Mistral AI API |
| 21 | `APITypeDeepSeek` | DeepSeek API |
| 22 | `APITypeMokaAI` | Moka AI API |
| 23 | `APITypeVolcEngine` | 火山引擎 API |
| 24 | `APITypeBaiduV2` | 百度千帆 V2 API |
| 25 | `APITypeOpenRouter` | OpenRouter API |
| 26 | `APITypeXinference` | Xinference API |
| 27 | `APITypeXai` | xAI (Grok) API |
| 28 | `APITypeCoze` | Coze API |
| 29 | `APITypeJimeng` | 即梦图像 API |
| 30 | `APITypeMoonshot` | 月之暗面 API |
| 31 | `APITypeSubmodel` | Submodel API |
| 32 | `APITypeMiniMax` | MiniMax API |
| 33 | `APITypeReplicate` | Replicate API |
| 34 | `APITypeCodex` | OpenAI Codex API |

---

## 4. 渠道类型到 API 类型映射

| 渠道类型 | API 类型 |
|----------|----------|
| OpenAI (1) | OpenAI (0) |
| Anthropic (14) | Anthropic (1) |
| Baidu (15) | Baidu (3) |
| PaLM (11) | PaLM (2) |
| Zhipu (16) | Zhipu (4) |
| Ali (17) | Ali (5) |
| Xunfei (18) | Xunfei (6) |
| AIProxyLibrary (21) | AIProxyLibrary (7) |
| Tencent (23) | Tencent (8) |
| Gemini (24) | Gemini (9) |
| Zhipu_v4 (26) | ZhipuV4 (10) |
| Ollama (4) | Ollama (11) |
| Perplexity (27) | Perplexity (12) |
| AWS (33) | AWS (13) |
| Cohere (34) | Cohere (14) |
| Dify (37) | Dify (15) |
| Jina (38) | Jina (16) |
| Cloudflare (39) | Cloudflare (17) |
| SiliconFlow (40) | SiliconFlow (18) |
| VertexAI (41) | VertexAI (19) |
| Mistral (42) | Mistral (20) |
| DeepSeek (43) | DeepSeek (21) |
| MokaAI (44) | MokaAI (22) |
| VolcEngine (45) | VolcEngine (23) |
| BaiduV2 (46) | BaiduV2 (24) |
| OpenRouter (20) | OpenRouter (25) |
| Xinference (47) | Xinference (26) |
| xAI (48) | xAI (27) |
| Coze (49) | Coze (28) |
| Jimeng (51) | Jimeng (29) |
| Moonshot (25) | Moonshot (30) |
| Submodel (53) | Submodel (31) |
| MiniMax (35) | MiniMax (32) |
| Replicate (56) | Replicate (33) |
| Codex (57) | Codex (34) |

---

## 5. 端点类型（EndpointType）

端点类型定义了 API 请求的协议端点。

| 端点类型 | 值 | 默认路径 | 默认方法 | 说明 |
|----------|-----|----------|----------|------|
| `openai` | `openai` | `/v1/chat/completions` | POST | OpenAI Chat Completions 标准端点 |
| `openai-response` | `openai-response` | `/v1/responses` | POST | OpenAI Responses API 端点 |
| `openai-response-compact` | `openai-response-compact` | `/v1/responses/compact` | POST | OpenAI Responses 压缩端点 |
| `anthropic` | `anthropic` | `/v1/messages` | POST | Anthropic Claude Messages 端点 |
| `gemini` | `gemini` | `/v1beta/models/{model}:generateContent` | POST | Google Gemini 生成端点 |
| `jina-rerank` | `jina-rerank` | `/v1/rerank` | POST | Jina Rerank 端点 |
| `image-generation` | `image-generation` | `/v1/images/generations` | POST | 图像生成端点 |
| `embeddings` | `embeddings` | `/v1/embeddings` | POST | 向量嵌入端点 |
| `openai-video` | `openai-video` | - | POST | OpenAI 视频生成端点（Sora） |

### 5.1 渠道到端点类型映射

| 渠道类型 | 优先端点类型 | 说明 |
|----------|-------------|------|
| Jina | jina-rerank | Jina 仅支持 Rerank 端点 |
| Anthropic, AWS | anthropic, openai | 优先 Anthropic，回退到 OpenAI |
| Gemini, VertexAI | gemini, openai | 优先 Gemini，回退到 OpenAI |
| OpenRouter | openai | 仅支持 OpenAI 端点 |
| xAI | openai, openai-response | 支持 OpenAI 和 Response 端点 |
| Sora | openai-video | 专用视频生成端点 |
| 其他 | openai | 默认使用 OpenAI 端点 |

### 5.2 特殊模型端点规则

- **图像生成模型**（如 `dall-e-3`、`gpt-image-1`、`flux-*`）：自动添加 `image-generation` 端点为最高优先级
- **OpenAI Response 专用模型**（如 `o3-pro`、`o3-deep-research`）：仅支持 `openai-response` 端点

---

## 6. 系统配置环境变量（constant/env.go）

以下变量为运行时可配置的系统参数。

| 变量名 | 类型 | 说明 |
|--------|------|------|
| `StreamingTimeout` | int | 流式请求超时时间（秒） |
| `DifyDebug` | bool | Dify 渠道调试模式开关 |
| `MaxFileDownloadMB` | int | 最大文件下载大小（MB） |
| `StreamScannerMaxBufferMB` | int | 流式扫描器最大缓冲区（MB） |
| `ForceStreamOption` | bool | 强制使用流式传输选项 |
| `CountToken` | bool | 是否启用 Token 计数 |
| `GetMediaToken` | bool | 是否获取媒体 Token |
| `GetMediaTokenNotStream` | bool | 非流式请求是否获取媒体 Token |
| `UpdateTask` | bool | 是否更新任务状态 |
| `MaxRequestBodyMB` | int | 最大请求体大小（MB） |
| `AzureDefaultAPIVersion` | string | Azure 默认 API 版本 |
| `NotifyLimitCount` | int | 通知限制次数 |
| `NotificationLimitDurationMinute` | int | 通知限制时间窗口（分钟） |
| `GenerateDefaultToken` | bool | 是否为新用户生成默认 Token |
| `ErrorLogEnabled` | bool | 是否启用错误日志 |
| `TaskQueryLimit` | int | 任务查询数量限制 |
| `TaskTimeoutMinutes` | int | 任务超时时间（分钟） |
| `TaskPricePatches` | []string | 任务价格补丁（临时，如 Sora） |
| `TrustedRedirectDomains` | []string | 受信任的重定向域名列表（支持子域名匹配） |

---

## 7. 缓存键格式（Cache Key）

系统使用 Redis 缓存时的键名格式。

| 缓存键格式 | 说明 |
|------------|------|
| `user_group:%d` | 用户分组缓存（%d 为用户 ID） |
| `user_quota:%d` | 用户额度缓存 |
| `user_enabled:%d` | 用户启用状态缓存 |
| `user_name:%d` | 用户名缓存 |

### Token 字段名

| 字段名 | 说明 |
|--------|------|
| `RemainQuota` | Token 剩余额度 |
| `Group` | Token 所属分组 |

---

## 8. 用户角色与权限

### 8.1 角色定义

| 角色 | 值 | 说明 |
|------|-----|------|
| `RoleGuestUser` | 0 | 访客用户 |
| `RoleCommonUser` | 1 | 普通用户 |
| `RoleAdminUser` | 10 | 管理员 |
| `RoleRootUser` | 100 | 超级管理员 |

### 8.2 文件权限配置

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `FileUploadPermission` | 0 (Guest) | 文件上传所需最低角色 |
| `FileDownloadPermission` | 0 (Guest) | 文件下载所需最低角色 |
| `ImageUploadPermission` | 0 (Guest) | 图像上传所需最低角色 |
| `ImageDownloadPermission` | 0 (Guest) | 图像下载所需最低角色 |

---

## 9. 用户/Token/渠道/兑换码状态

### 9.1 用户状态

| 状态 | 值 | 说明 |
|------|-----|------|
| `UserStatusEnabled` | 1 | 用户已启用 |
| `UserStatusDisabled` | 2 | 用户已禁用 |

### 9.2 Token 状态

| 状态 | 值 | 说明 |
|------|-----|------|
| `TokenStatusEnabled` | 1 | Token 已启用 |
| `TokenStatusDisabled` | 2 | Token 已禁用 |
| `TokenStatusExpired` | 3 | Token 已过期 |
| `TokenStatusExhausted` | 4 | Token 额度已耗尽 |

### 9.3 渠道状态

| 状态 | 值 | 说明 |
|------|-----|------|
| `ChannelStatusUnknown` | 0 | 未知状态 |
| `ChannelStatusEnabled` | 1 | 渠道已启用 |
| `ChannelStatusManuallyDisabled` | 2 | 手动禁用 |
| `ChannelStatusAutoDisabled` | 3 | 自动禁用（错误率过高） |

### 9.4 兑换码状态

| 状态 | 值 | 说明 |
|------|-----|------|
| `RedemptionCodeStatusEnabled` | 1 | 兑换码可用 |
| `RedemptionCodeStatusDisabled` | 2 | 兑换码已禁用 |
| `RedemptionCodeStatusUsed` | 3 | 兑换码已使用 |

### 9.5 充值状态

| 状态 | 说明 |
|------|------|
| `pending` | 待处理 |
| `success` | 成功 |
| `failed` | 失败 |
| `expired` | 已过期 |

---

## 10. 多密钥模式

| 模式 | 值 | 说明 |
|------|-----|------|
| `random` | 随机 | 从可用密钥中随机选择 |
| `polling` | 轮询 | 按顺序轮询使用密钥 |

---

## 11. 速率限制配置

### 11.1 全局限制

| 配置项 | 说明 |
|--------|------|
| `GlobalApiRateLimitEnable` | 全局 API 速率限制开关 |
| `GlobalApiRateLimitNum` | 全局 API 速率限制次数 |
| `GlobalApiRateLimitDuration` | 全局 API 速率限制时间窗口（秒） |
| `GlobalWebRateLimitEnable` | 全局 Web 速率限制开关 |
| `GlobalWebRateLimitNum` | 全局 Web 速率限制次数 |
| `GlobalWebRateLimitDuration` | 全局 Web 速率限制时间窗口（秒） |

### 11.2 关键操作限制

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `CriticalRateLimitEnable` | - | 关键操作速率限制开关 |
| `CriticalRateLimitNum` | 100 | 关键操作限制次数 |
| `CriticalRateLimitDuration` | 600 (10分钟) | 关键操作时间窗口（秒） |

### 11.3 上传/下载限制

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `UploadRateLimitNum` | 10 | 上传限制次数 |
| `UploadRateLimitDuration` | 60 | 上传时间窗口（秒） |
| `DownloadRateLimitNum` | 10 | 下载限制次数 |
| `DownloadRateLimitDuration` | 60 | 下载时间窗口（秒） |

### 11.4 搜索限制

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `SearchRateLimitEnable` | true | 搜索速率限制开关 |
| `SearchRateLimitNum` | 10 | 搜索限制次数 |
| `SearchRateLimitDuration` | 60 | 搜索时间窗口（秒） |

### 11.5 速率限制器机制

- 使用内存中的滑动窗口算法（`InMemoryRateLimiter`）
- 键过期时间：20 分钟
- 到期自动清理过期键
- 请求未超限返回 `true`，超限返回 `false`

---

## 12. 通用系统配置（common/constants.go）

### 12.1 基础信息

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `Version` | `v0.0.0` | 系统版本号（构建时自动替换） |
| `SystemName` | `DogAPI` | 系统名称 |
| `Footer` | `""` | 页脚文本 |
| `Logo` | `""` | Logo 地址 |
| `TopUpLink` | `""` | 充值链接 |

### 12.2 额度相关

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `QuotaPerUnit` | 500000.0 | 每美元对应额度（$0.002/1K tokens） |
| `DisplayInCurrencyEnabled` | true | 是否以货币形式显示额度 |
| `DisplayTokenStatEnabled` | true | 是否显示 Token 统计 |
| `QuotaForNewUser` | 0 | 新用户赠送额度 |
| `QuotaForInviter` | 0 | 邀请人奖励额度 |
| `QuotaForInvitee` | 0 | 被邀请人奖励额度 |
| `InviteCommissionEnabled` | false | 邀请佣金开关 |
| `InviteCommissionRate` | 0.0 | 邀请佣金比例 |
| `QuotaRemindThreshold` | 1000 | 额度提醒阈值 |
| `PreConsumedQuota` | 500 | 预消费额度 |
| `ChannelDisableThreshold` | 5.0 | 渠道禁用阈值 |
| `AutomaticDisableChannelEnabled` | false | 自动禁用渠道开关 |
| `AutomaticEnableChannelEnabled` | false | 自动启用渠道开关 |

### 12.3 功能开关

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `DrawingEnabled` | true | 绘图功能开关 |
| `TaskEnabled` | true | 任务功能开关 |
| `DataExportEnabled` | true | 数据导出开关 |
| `DataExportInterval` | 5 | 数据导出间隔（分钟） |
| `DataExportDefaultTime` | `"hour"` | 数据导出默认时间范围 |
| `DefaultCollapseSidebar` | false | 默认折叠侧边栏 |
| `LogConsumeEnabled` | true | 消费日志开关 |

### 12.4 认证配置

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `PasswordLoginEnabled` | true | 密码登录开关 |
| `PasswordRegisterEnabled` | true | 密码注册开关 |
| `RegisterEnabled` | true | 注册总开关 |
| `EmailVerificationEnabled` | false | 邮箱验证开关 |
| `GitHubOAuthEnabled` | false | GitHub OAuth 开关 |
| `LinuxDOOAuthEnabled` | false | LinuxDO OAuth 开关 |
| `WeChatAuthEnabled` | false | 微信认证开关 |
| `TelegramOAuthEnabled` | false | Telegram OAuth 开关 |
| `TurnstileCheckEnabled` | false | Cloudflare Turnstile 验证开关 |

### 12.5 邮箱域名限制

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `EmailDomainRestrictionEnabled` | false | 邮箱域名限制开关 |
| `EmailAliasRestrictionEnabled` | false | 邮箱别名限制开关 |

**默认白名单域名：**
`gmail.com`, `163.com`, `126.com`, `qq.com`, `outlook.com`, `hotmail.com`, `icloud.com`, `yahoo.com`, `foxmail.com`

**邮箱登录认证服务器：**
`smtp.sendcloud.net`, `smtp.azurecomm.net`

### 12.6 网络与超时配置

| 配置项 | 说明 |
|--------|------|
| `RetryTimes` | 重试次数（默认 0） |
| `RelayTimeout` | 中继超时时间（秒） |
| `RelayMaxIdleConns` | 最大空闲连接数 |
| `RelayMaxIdleConnsPerHost` | 每主机最大空闲连接数 |
| `RelayIdleConnTimeout` | 空闲连接超时（秒） |
| `RelayResponseHeaderTimeout` | 响应头超时（秒） |
| `RelayTLSHandshakeTimeout` | TLS 握手超时（秒） |
| `RelayExpectContinueTimeout` | 100-Continue 超时（秒） |
| `RelayDialTimeout` | 拨号超时（秒） |
| `RelayDialKeepAlive` | 拨号保活时间（秒） |
| `RelayForceAttemptHTTP2` | 强制使用 HTTP/2 |
| `TLSInsecureSkipVerify` | 跳过 TLS 证书验证 |
| `SyncFrequency` | 同步频率（秒） |

### 12.7 SMTP 邮件配置

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `SMTPServer` | `""` | SMTP 服务器地址 |
| `SMTPPort` | 587 | SMTP 端口 |
| `SMTPSSLEnabled` | false | SMTP SSL 开关 |
| `SMTPForceAuthLogin` | false | 强制 SMTP 认证 |
| `SMTPAccount` | `""` | SMTP 账号 |
| `SMTPFrom` | `""` | 发件人地址 |
| `SMTPToken` | `""` | SMTP 密码/Token |

### 12.8 第三方 OAuth 配置

| 配置项 | 说明 |
|--------|------|
| `GitHubClientId` | GitHub OAuth Client ID |
| `GitHubClientSecret` | GitHub OAuth Client Secret |
| `LinuxDOClientId` | LinuxDO OAuth Client ID |
| `LinuxDOClientSecret` | LinuxDO OAuth Client Secret |
| `LinuxDOMinimumTrustLevel` | LinuxDO 最低信任等级 |
| `WeChatServerAddress` | 微信服务器地址 |
| `WeChatServerToken` | 微信服务器 Token |
| `WeChatAccountQRCodeImageURL` | 微信公众号二维码图片 URL |
| `TurnstileSiteKey` | Cloudflare Turnstile Site Key |
| `TurnstileSecretKey` | Cloudflare Turnstile Secret Key |
| `TelegramBotToken` | Telegram Bot Token |
| `TelegramBotName` | Telegram Bot 名称 |

### 12.9 安全相关

| 配置项 | 说明 |
|--------|------|
| `SessionSecret` | 会话密钥（启动时随机生成） |
| `CryptoSecret` | 加密密钥（启动时随机生成） |
| `RequestIdKey` | 请求 ID 头部键名：`X-Oneapi-Request-Id` |

---

## 13. 环境变量读取工具

系统提供了三种环境变量读取辅助函数：

| 函数 | 说明 | 用法 |
|------|------|------|
| `GetEnvOrDefault(env, default)` | 读取整数环境变量 | 未设置或解析失败时返回默认值 |
| `GetEnvOrDefaultString(env, default)` | 读取字符串环境变量 | 未设置时返回默认值 |
| `GetEnvOrDefaultBool(env, default)` | 读取布尔环境变量 | 未设置或解析失败时返回默认值 |

---

## 14. 充值分组比例

系统支持按用户分组设置不同的充值比例。

| 分组 | 默认比例 | 说明 |
|------|----------|------|
| `default` | 1.0 | 默认分组 |
| `vip` | 1.0 | VIP 分组 |
| `svip` | 1.0 | SVIP 分组 |

- 比例值通过 JSON 字符串动态配置
- 比例为 1.0 表示 1:1 充值
- 比例 > 1.0 表示充值赠送加成

---

## 15. 模型分类规则

### 15.1 OpenAI Response 专用模型

以下模型仅支持 OpenAI Response 端点：

- `o3-pro`
- `o3-deep-research`
- `o4-mini-deep-research`

### 15.2 图像生成模型

以下模型会被自动识别为图像生成模型：

- `dall-e-3`
- `dall-e-2`
- `gpt-image-1`
- `prefix:imagen-`（以 `imagen-` 开头的模型）
- `flux-`（Flux 系列）
- `flux.1-`（Flux.1 系列）

### 15.3 OpenAI 文本模型

以下模型被归类为 OpenAI 文本模型：

- `gpt-` 前缀
- `o1`
- `o3`
- `o4`
- `chatgpt`

---

## 16. Waffo 支付方式

| 名称 | 图标 | PayMethodType | PayMethodName |
|------|------|---------------|---------------|
| Card | `/pay-card.png` | `CREDITCARD,DEBITCARD` | （自动选择） |
| Apple Pay | `/pay-apple.png` | `APPLEPAY` | `APPLEPAY` |
| Google Pay | `/pay-google.png` | `GOOGLEPAY` | `GOOGLEPAY` |

---

## 17. Azure 特殊配置

- `AzureNoRemoveDotTime`：Azure 渠道在 2025-05-10 之后不再移除模型名称中的点号（`.`）
- 此时间戳用于兼容旧版 Azure API 的模型命名规范

---

## 18. 请求上下文键（ContextKey）

系统在请求处理过程中通过上下文传递的键名。

### 18.1 Token 计数相关

| 键名 | 说明 |
|------|------|
| `token_count_meta` | Token 计数元数据 |
| `prompt_tokens` | 提示词 Token 数 |
| `estimated_tokens` | 预估 Token 数 |
| `local_count_tokens` | 本地 Token 计数标志 |

### 18.2 请求元数据

| 键名 | 说明 |
|------|------|
| `original_model` | 原始模型名称 |
| `request_start_time` | 请求开始时间 |

### 18.3 Token 相关

| 键名 | 说明 |
|------|------|
| `token_unlimited_quota` | Token 无限额度标志 |
| `token_key` | Token 密钥 |
| `token_id` | Token ID |
| `token_group` | Token 分组 |
| `specific_channel_id` | 指定渠道 ID |
| `token_model_limit_enabled` | Token 模型限制开关 |
| `token_model_limit` | Token 模型限制列表 |
| `token_cross_group_retry` | Token 跨分组重试标志 |

### 18.4 渠道相关

| 键名 | 说明 |
|------|------|
| `channel_id` | 渠道 ID |
| `channel_name` | 渠道名称 |
| `channel_create_time` | 渠道创建时间 |
| `base_url` | 渠道 Base URL |
| `channel_type` | 渠道类型 |
| `channel_setting` | 渠道设置 |
| `channel_other_setting` | 渠道其他设置 |
| `param_override` | 参数覆盖 |
| `header_override` | 请求头覆盖 |
| `channel_organization` | 渠道组织 |
| `auto_ban` | 自动禁用标志 |
| `model_mapping` | 模型映射 |
| `status_code_mapping` | 状态码映射 |
| `channel_is_multi_key` | 是否多密钥 |
| `channel_multi_key_index` | 多密钥索引 |
| `channel_key` | 渠道密钥 |

### 18.5 用户相关

| 键名 | 说明 |
|------|------|
| `id` | 用户 ID |
| `user_setting` | 用户设置 |
| `user_quota` | 用户额度 |
| `user_status` | 用户状态 |
| `user_email` | 用户邮箱 |
| `user_group` | 用户分组 |
| `group` | 使用的分组 |
| `username` | 用户名 |
| `language` | 用户语言偏好 |

### 18.6 其他

| 键名 | 说明 |
|------|------|
| `auto_group` | 自动分组标志 |
| `auto_group_index` | 自动分组索引 |
| `auto_group_retry_index` | 自动分组重试索引 |
| `system_prompt_override` | 系统提示词覆盖 |
| `file_sources_to_cleanup` | 待清理的文件源 |
| `admin_reject_reason` | 管理员拒绝原因（不返回客户端，仅记录日志） |

---

## 19. 安全设置

### 19.1 Gemini 安全设置

- `GeminiSafetySetting`：配置 Google Gemini API 的安全过滤级别

### 19.2 Cohere 安全设置

- `CohereSafetySetting`：配置 Cohere API 的安全模式
  - `NONE`：无安全过滤
  - `CONTEXTUAL`：上下文过滤
  - `STRICT`：严格过滤

---

## 20. 分页配置

| 配置项 | 默认值 | 说明 |
|--------|--------|------|
| `ItemsPerPage` | 10 | 每页显示条目数 |
| `MaxRecentItems` | 1000 | 最大最近条目数 |
