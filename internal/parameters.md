# DogAPI 参数文档

本文档基于 DogAPI 项目 (`DogApiKey/DogAPI`) 的 DTO (Data Transfer Object) 源码生成，涵盖所有用户面向 API 的请求和响应格式。

---

## 目录

- [1. 通用响应结构](#1-通用响应结构)
- [2. OpenAI 兼容聊天补全 (Chat Completions)](#2-openai-兼容聊天补全-chat-completions)
- [3. OpenAI Responses API](#3-openai-responses-api)
- [4. Embedding 向量化接口](#4-embedding-向量化接口)
- [5. 图像生成 (Image Generation)](#5-图像生成-image-generation)
- [6. 视频生成 (Video Generation)](#6-视频生成-video-generation)
- [7. 语音合成/转写 (Audio TTS/STT)](#7-语音合成转写-audiottsstt)
- [8. Claude 消息接口](#8-claude-消息接口)
- [9. Gemini 聊天接口](#9-gemini-聊天接口)
- [10. Rerank 重排序接口](#10-rerank-重排序接口)
- [11. 任务系统 (Task)](#11-任务系统-task)
- [12. Midjourney 图像生成](#12-midjourney-图像生成)
- [13. Suno 音乐生成](#13-suno-音乐生成)
- [14. Realtime 实时语音接口](#14-realtime-实时语音接口)
- [15. 用户设置 (User Settings)](#15-用户设置-user-settings)
- [16. 渠道设置 (Channel Settings)](#16-渠道设置-channel-settings)
- [17. 模型定价 (Pricing)](#17-模型定价-pricing)
- [18. 通知系统 (Notify)](#18-通知系统-notify)
- [19. 敏感词检测 (Sensitive)](#19-敏感词检测-sensitive)
- [20. Playground 会话](#20-playground-会话)
- [21. 上游同步 (Ratio Sync)](#21-上游同步-ratio-sync)
- [22. 错误响应格式](#22-错误响应格式)

---

## 1. 通用响应结构

### Usage (用量统计)

所有 API 响应中通用的 token 用量结构体。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `prompt_tokens` | int | 输入 token 数量 |
| `completion_tokens` | int | 输出 token 数量 |
| `total_tokens` | int | 总 token 数量 |
| `prompt_cache_hit_tokens` | int | 命中缓存的输入 token 数量（可选） |
| `usage_semantic` | string | 用量语义标记（可选） |
| `usage_source` | string | 用量来源标记（可选） |
| `input_tokens` | int | 输入 token 数量（Claude 风格） |
| `output_tokens` | int | 输出 token 数量（Claude 风格） |
| `claude_cache_creation_5_m_tokens` | int | Claude 5分钟缓存创建 token 数 |
| `claude_cache_creation_1_h_tokens` | int | Claude 1小时缓存创建 token 数 |
| `cost` | any | OpenRouter 费用（可选） |

**InputTokenDetails (输入 token 详情):**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `cached_tokens` | int | 缓存命中 token 数 |
| `cached_creation_tokens` | int | 缓存创建 token 数（可选） |
| `text_tokens` | int | 文本 token 数 |
| `audio_tokens` | int | 音频 token 数 |
| `image_tokens` | int | 图像 token 数 |

**OutputTokenDetails (输出 token 详情):**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `text_tokens` | int | 文本 token 数 |
| `audio_tokens` | int | 音频 token 数 |
| `reasoning_tokens` | int | 推理 token 数 |

---

## 2. OpenAI 兼容聊天补全 (Chat Completions)

### 请求参数: `GeneralOpenAIRequest`

兼容 OpenAI `/v1/chat/completions` 接口的通用请求结构。

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `model` | string | 是 | 模型名称，如 `gpt-4o`、`claude-3-5-sonnet` |
| `messages` | Message[] | 是 | 消息数组 |
| `stream` | bool | 否 | 是否启用流式响应，默认 `false` |
| `stream_options` | StreamOptions | 否 | 流式选项配置 |
| `max_tokens` | uint | 否 | 最大输出 token 数 |
| `max_completion_tokens` | uint | 否 | 最大补全 token 数（新版参数） |
| `reasoning_effort` | string | 否 | 推理努力程度（如 `low`/`medium`/`high`） |
| `temperature` | float64 | 否 | 采样温度，范围 0-2 |
| `top_p` | float64 | 否 | 核采样概率 |
| `top_k` | int | 否 | Top-K 采样参数 |
| `stop` | any | 否 | 停止序列，字符串或字符串数组 |
| `n` | int | 否 | 生成响应数量 |
| `input` | any | 否 | 输入（用于特定端点） |
| `instruction` | string | 否 | 指令文本 |
| `frequency_penalty` | float64 | 否 | 频率惩罚 |
| `presence_penalty` | float64 | 否 | 存在惩罚 |
| `seed` | int | 随机种子 |
| `user` | string | 否 | 用户标识 |
| `tools` | Tool[] | 否 | 可用工具列表 |
| `tool_choice` | any | 否 | 工具选择策略 |
| `response_format` | ResponseFormat | 否 | 响应格式配置 |
| `logprobs` | bool | 否 | 是否返回 logprobs |
| `top_logprobs` | int | 否 | 返回的 top logprobs 数量 |
| `modalities` | string[] | 否 | 模态类型 |
| `audio` | AudioOptions | 否 | 音频选项 |
| `prediction` | json.RawMessage | 否 | 推测性解码配置 |
| `service_tier` | string | 否 | 服务层级 |
| `store` | bool | 否 | 是否存储对话 |
| `metadata` | json.RawMessage | 否 | 元数据 |
| `reasoning` | json.RawMessage | 否 | 推理配置 |
| `thinking` | json.RawMessage | 否 | 思考配置 |
| `cache_control` | json.RawMessage | 否 | 缓存控制 |
| `parallel_tool_calls` | bool | 否 | 是否允许并行工具调用 |

**Message 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `role` | string | 角色：`system`、`user`、`assistant`、`tool` |
| `content` | any | 消息内容，可以是字符串或内容数组 |
| `name` | string | 发送者名称（可选） |
| `tool_calls` | ToolCall[] | 工具调用列表（assistant 角色） |
| `tool_call_id` | string | 工具调用 ID（tool 角色） |

**ResponseFormat 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 格式类型：`text`、`json_object`、`json_schema` |
| `json_schema` | json.RawMessage | JSON Schema 定义（当 type 为 `json_schema` 时） |

**StreamOptions 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `include_usage` | bool | 流式响应中是否包含 usage 信息 |

### 请求示例

```json
{
  "model": "gpt-4o",
  "messages": [
    {"role": "system", "content": "你是一个有帮助的助手。"},
    {"role": "user", "content": "你好，请介绍一下自己。"}
  ],
  "stream": false,
  "temperature": 0.7,
  "max_tokens": 1000
}
```

### 响应参数: `OpenAITextResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 响应唯一标识 |
| `object` | string | 对象类型，固定为 `chat.completion` |
| `created` | any | 创建时间戳 |
| `model` | string | 使用的模型名称 |
| `choices` | Choice[] | 选项列表 |
| `error` | any | 错误信息（可选） |
| `usage` | Usage | 用量统计 |

**OpenAITextResponseChoice 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `index` | int | 选项索引 |
| `message` | Message | 响应消息 |
| `finish_reason` | string | 完成原因：`stop`、`length`、`tool_calls` |

### 非流式响应示例

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "created": 1700000000,
  "model": "gpt-4o",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "你好！我是一个AI助手..."
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 25,
    "completion_tokens": 50,
    "total_tokens": 75
  }
}
```

### 流式响应参数: `ChatCompletionsStreamResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 响应唯一标识 |
| `object` | string | 对象类型，固定为 `chat.completion.chunk` |
| `created` | int64 | 创建时间戳 |
| `model` | string | 模型名称 |
| `system_fingerprint` | string | 系统指纹（可选） |
| `choices` | StreamChoice[] | 流式选项列表 |
| `usage` | *Usage | 用量统计（可选，通常在最后一个 chunk 中） |

**ChatCompletionsStreamResponseChoiceDelta 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `content` | string | 增量文本内容（可选） |
| `reasoning_content` | string | 推理内容（可选） |
| `reasoning` | string | 推理内容备选字段（可选） |
| `role` | string | 角色 |
| `tool_calls` | ToolCallResponse[] | 工具调用（可选） |

### 流式响应示例

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion.chunk",
  "created": 1700000000,
  "model": "gpt-4o",
  "choices": [
    {
      "index": 0,
      "delta": {
        "content": "你好"
      },
      "finish_reason": null
    }
  ]
}
```

---

## 3. OpenAI Responses API

### 响应参数: `OpenAIResponsesResponse`

用于 `/v1/responses` 端点。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 响应 ID |
| `object` | string | 对象类型 |
| `created_at` | int | 创建时间戳 |
| `status` | json.RawMessage | 状态 |
| `error` | any | 错误信息（可选） |
| `incomplete_details` | IncompleteDetails | 未完成详情（可选） |
| `instructions` | string | 指令 |
| `max_output_tokens` | int | 最大输出 token 数 |
| `model` | string | 模型名称 |
| `output` | ResponsesOutput[] | 输出内容列表 |
| `parallel_tool_calls` | bool | 是否并行工具调用 |
| `previous_response_id` | json.RawMessage | 上一轮响应 ID |
| `reasoning` | *Reasoning | 推理配置 |
| `store` | bool | 是否存储 |
| `temperature` | float64 | 温度 |
| `tool_choice` | json.RawMessage | 工具选择 |
| `tools` | map[] | 工具列表 |
| `top_p` | float64 | Top-P |
| `truncation` | json.RawMessage | 截断配置 |
| `usage` | *Usage | 用量统计 |
| `user` | json.RawMessage | 用户标识 |
| `metadata` | json.RawMessage | 元数据 |

**ResponsesOutput 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 输出类型：`message`、`image_generation_call`、`web_search_call` |
| `id` | string | 输出 ID |
| `status` | string | 状态 |
| `role` | string | 角色 |
| `content` | ResponsesOutputContent[] | 内容列表 |
| `quality` | string | 图像质量 |
| `size` | string | 图像尺寸 |
| `call_id` | string | 工具调用 ID（可选） |
| `name` | string | 工具名称（可选） |
| `arguments` | string | 工具参数（可选） |

### 流式响应: `ResponsesStreamResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 事件类型 |
| `response` | OpenAIResponsesResponse | 完整响应（可选） |
| `delta` | string | 增量内容（可选） |
| `item` | ResponsesOutput | 输出项（可选） |
| `output_index` | int | 输出索引（可选） |
| `content_index` | int | 内容索引（可选） |
| `item_id` | string | 项目 ID（可选） |

---

## 4. Embedding 向量化接口

### 请求参数: `EmbeddingRequest`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `model` | string | 是 | 模型名称，如 `text-embedding-3-small` |
| `input` | any | 是 | 输入文本，字符串或字符串数组 |
| `encoding_format` | string | 否 | 编码格式：`float`、`base64` |
| `dimensions` | int | 否 | 输出向量维度 |
| `user` | string | 否 | 用户标识 |
| `seed` | float64 | 否 | 随机种子 |
| `temperature` | float64 | 否 | 采样温度 |
| `top_p` | float64 | 否 | 核采样概率 |
| `frequency_penalty` | float64 | 否 | 频率惩罚 |
| `presence_penalty` | float64 | 否 | 存在惩罚 |

### 请求示例

```json
{
  "model": "text-embedding-3-small",
  "input": ["你好世界", "这是一个测试"]
}
```

### 响应参数: `EmbeddingResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `object` | string | 对象类型，固定为 `list` |
| `data` | EmbeddingResponseItem[] | 向量结果列表 |
| `model` | string | 使用的模型 |
| `usage` | Usage | 用量统计 |

**EmbeddingResponseItem 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `object` | string | 对象类型，固定为 `embedding` |
| `index` | int | 索引 |
| `embedding` | float64[] | 向量数组 |

### 响应示例

```json
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "index": 0,
      "embedding": [0.0023064255, -0.009327292, ...]
    }
  ],
  "model": "text-embedding-3-small",
  "usage": {
    "prompt_tokens": 8,
    "total_tokens": 8
  }
}
```

---

## 5. 图像生成 (Image Generation)

### 请求参数: `ImageRequest`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `model` | string | 是 | 模型名称，如 `dall-e-3` |
| `prompt` | string | 是 | 图像描述提示词（带 `binding:"required"` 验证） |
| `n` | uint | 否 | 生成图像数量 |
| `size` | string | 否 | 图像尺寸：`256x256`、`512x512`、`1024x1024`、`1024x1792`、`1792x1024` |
| `quality` | string | 否 | 图像质量：`standard`、`hd` |
| `response_format` | string | 否 | 响应格式：`url`、`b64_json` |
| `style` | json.RawMessage | 否 | 风格 |
| `user` | json.RawMessage | 否 | 用户标识 |
| `background` | json.RawMessage | 否 | 背景设置 |
| `moderation` | json.RawMessage | 否 | 审核设置 |
| `output_format` | json.RawMessage | 否 | 输出格式 |
| `output_compression` | json.RawMessage | 否 | 输出压缩 |
| `partial_images` | json.RawMessage | 否 | 部分图像数量 |
| `watermark` | bool | 否 | 是否添加水印 |

**验证规则:**
- `prompt` 字段为必填项，Go struct tag: `binding:"required"`
- DALL-E 模型的价格计算基于 `size` 和 `quality` 的组合
- HD 质量 (`quality: "hd"`) 的价格倍率为 2.0（大尺寸为 1.5）

### 请求示例

```json
{
  "model": "dall-e-3",
  "prompt": "一只在月光下奔跑的白色猫咪",
  "n": 1,
  "size": "1024x1024",
  "quality": "hd"
}
```

### 响应参数: `ImageResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `data` | ImageData[] | 图像数据列表 |
| `created` | int64 | 创建时间戳 |
| `metadata` | json.RawMessage | 元数据（可选） |

**ImageData 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `url` | string | 图像 URL |
| `b64_json` | string | Base64 编码的图像数据 |
| `revised_prompt` | string | 修订后的提示词 |

### 响应示例

```json
{
  "created": 1700000000,
  "data": [
    {
      "url": "https://example.com/image.png",
      "b64_json": "",
      "revised_prompt": "A white cat running under the moonlight..."
    }
  ]
}
```

---

## 6. 视频生成 (Video Generation)

### 请求参数: `VideoRequest`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `model` | string | 否 | 模型/风格 ID，如 `kling-v1` |
| `prompt` | string | 否 | 文本提示词 |
| `image` | string | 否 | 输入图像（URL 或 Base64） |
| `duration` | float64 | 是 | 视频时长（秒） |
| `width` | int | 是 | 视频宽度 |
| `height` | int | 是 | 视频高度 |
| `fps` | int | 否 | 帧率 |
| `seed` | int | 否 | 随机种子 |
| `n` | int | 否 | 生成视频数量 |
| `response_format` | string | 否 | 响应格式 |
| `user` | string | 否 | 用户标识 |
| `metadata` | map[string]any | 否 | 供应商特定参数（如 `negative_prompt`、`style`、`quality_level` 等） |

### 请求示例

```json
{
  "model": "kling-v1",
  "prompt": "宇航员站起身走了",
  "duration": 5.0,
  "width": 512,
  "height": 512,
  "fps": 30
}
```

### 响应参数: `VideoResponse` (提交任务)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `task_id` | string | 任务 ID |
| `status` | string | 任务状态 |

### 响应参数: `VideoTaskResponse` (查询任务)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `task_id` | string | 任务 ID |
| `status` | string | 状态：`queued`、`in_progress`、`completed`、`failed` |
| `url` | string | 视频资源 URL（成功时） |
| `format` | string | 视频格式，如 `mp4` |
| `metadata` | VideoTaskMetadata | 结果元数据（可选） |
| `error` | VideoTaskError | 错误信息（可选） |

**VideoTaskMetadata 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `duration` | float64 | 实际视频时长 |
| `fps` | int | 实际帧率 |
| `width` | int | 实际宽度 |
| `height` | int | 实际高度 |
| `seed` | int | 使用的随机种子 |

**VideoTaskError 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `code` | int | 错误代码 |
| `message` | string | 错误描述 |

### OpenAI 视频格式: `OpenAIVideo`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 视频 ID |
| `task_id` | string | 任务 ID（兼容旧接口，待废弃） |
| `object` | string | 对象类型，固定为 `video` |
| `model` | string | 模型名称 |
| `status` | string | 状态：`queued`、`in_progress`、`completed`、`failed` |
| `progress` | int | 进度百分比 |
| `created_at` | int64 | 创建时间 |
| `completed_at` | int64 | 完成时间（可选） |
| `expires_at` | int64 | 过期时间（可选） |
| `seconds` | string | 视频秒数（可选） |
| `size` | string | 视频大小（可选） |
| `error` | OpenAIVideoError | 错误信息（可选） |
| `metadata` | map[string]any | 元数据（可选） |

---

## 7. 语音合成/转写 (Audio/TTS/STT)

### 请求参数: `AudioRequest`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `model` | string | 是 | 模型名称，如 `tts-1`、`whisper-1` |
| `input` | string | 是 | 输入文本或音频 |
| `voice` | string | 是 | 语音类型 |
| `instructions` | string | 否 | 指令文本 |
| `response_format` | string | 否 | 响应格式：`mp3`、`opus`、`aac`、`flac`、`wav`、`pcm` |
| `speed` | float64 | 否 | 语速 |
| `stream_format` | string | 否 | 流式格式，`sse` 表示启用流式 |
| `metadata` | json.RawMessage | 否 | 元数据 |

### 请求示例

```json
{
  "model": "tts-1",
  "input": "你好，欢迎使用我们的服务！",
  "voice": "alloy",
  "response_format": "mp3",
  "speed": 1.0
}
```

### 响应参数: `WhisperVerboseJSONResponse` (STT 转写)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `task` | string | 任务类型 |
| `language` | string | 识别的语言 |
| `duration` | float64 | 音频时长（秒） |
| `text` | string | 转写文本 |
| `segments` | Segment[] | 分段详情 |

**Segment 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | int | 段落 ID |
| `seek` | int | seek 位置 |
| `start` | float64 | 开始时间 |
| `end` | float64 | 结束时间 |
| `text` | string | 文本内容 |
| `tokens` | int[] | token 数组 |
| `temperature` | float64 | 温度 |
| `avg_logprob` | float64 | 平均 log 概率 |
| `compression_ratio` | float64 | 压缩比 |
| `no_speech_prob` | float64 | 无语音概率 |

---

## 8. Claude 消息接口

### 请求参数: `ClaudeRequest`

兼容 Anthropic Claude Messages API。

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `model` | string | 是 | 模型名称，如 `claude-3-5-sonnet-20241022` |
| `messages` | ClaudeMessage[] | 是 | 消息数组 |
| `system` | any | 否 | 系统提示词，字符串或结构化内容 |
| `max_tokens` | uint | 是 | 最大输出 token 数 |
| `max_tokens_to_sample` | uint | 否 | 最大采样 token 数（旧参数） |
| `stream` | bool | 否 | 是否流式输出 |
| `temperature` | float64 | 否 | 采样温度 |
| `top_p` | float64 | 否 | 核采样概率 |
| `top_k` | int | 否 | Top-K 采样 |
| `stop_sequences` | string[] | 否 | 停止序列 |
| `tools` | any | 否 | 工具列表 |
| `tool_choice` | any | 否 | 工具选择策略 |
| `thinking` | Thinking | 否 | 思考/推理配置 |
| `metadata` | json.RawMessage | 否 | 元数据 |
| `inference_geo` | string | 否 | 数据驻留区域（默认过滤，需渠道设置启用） |
| `service_tier` | string | 否 | 服务层级（默认过滤，需渠道设置启用） |
| `context_management` | json.RawMessage | 否 | 上下文管理 |
| `output_config` | json.RawMessage | 否 | 输出配置 |
| `output_format` | json.RawMessage | 否 | 输出格式 |
| `mcp_servers` | json.RawMessage | 否 | MCP 服务器配置 |
| `container` | json.RawMessage | 否 | 容器配置 |

**ClaudeMessage 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `role` | string | 角色：`user`、`assistant` |
| `content` | any | 内容，字符串或 `ClaudeMediaMessage[]` |

**ClaudeMediaMessage 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 内容类型：`text`、`image`、`tool_use`、`tool_result`、`thinking` |
| `text` | string | 文本内容（type=text 时） |
| `source` | ClaudeMessageSource | 媒体源（type=image 时） |
| `thinking` | string | 思考内容（type=thinking 时） |
| `signature` | string | 签名 |
| `cache_control` | json.RawMessage | 缓存控制 |
| `id` | string | 工具使用 ID |
| `name` | string | 工具名称 |
| `input` | any | 工具输入 |
| `tool_use_id` | string | 工具使用 ID（tool_result 时） |

**ClaudeMessageSource 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 源类型：`base64`、`url` |
| `media_type` | string | 媒体类型：`image/png`、`image/jpeg` 等 |
| `data` | any | Base64 数据 |
| `url` | string | URL 地址 |

**Thinking 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 类型，如 `enabled` |
| `budget_tokens` | int | 推理 token 预算 |

**Tool 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `name` | string | 工具名称 |
| `description` | string | 工具描述（可选） |
| `input_schema` | map | 输入参数 JSON Schema |

### 请求示例

```json
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 1024,
  "system": "你是一个有帮助的助手。",
  "messages": [
    {"role": "user", "content": "你好！"}
  ],
  "temperature": 0.7
}
```

### 响应参数: `ClaudeResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 响应 ID |
| `type` | string | 类型：`message` |
| `role` | string | 角色：`assistant` |
| `content` | ClaudeMediaMessage[] | 内容列表 |
| `completion` | string | 补全文本（旧格式） |
| `stop_reason` | string | 停止原因：`end_turn`、`max_tokens`、`stop_sequence`、`tool_use` |
| `model` | string | 模型名称 |
| `error` | any | 错误信息（可选） |
| `usage` | ClaudeUsage | 用量统计 |

**ClaudeUsage 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `input_tokens` | int | 输入 token 数 |
| `output_tokens` | int | 输出 token 数 |
| `cache_creation_input_tokens` | int | 缓存创建输入 token 数 |
| `cache_read_input_tokens` | int | 缓存读取输入 token 数 |
| `claude_cache_creation_5_m_tokens` | int | 5分钟缓存创建 token 数 |
| `claude_cache_creation_1_h_tokens` | int | 1小时缓存创建 token 数 |
| `server_tool_use` | ClaudeServerToolUse | 服务器工具使用统计 |

### 响应示例

```json
{
  "id": "msg_abc123",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "你好！有什么可以帮助你的吗？"
    }
  ],
  "stop_reason": "end_turn",
  "model": "claude-3-5-sonnet-20241022",
  "usage": {
    "input_tokens": 20,
    "output_tokens": 25,
    "cache_creation_input_tokens": 0,
    "cache_read_input_tokens": 0
  }
}
```

---

## 9. Gemini 聊天接口

### 请求参数: `GeminiChatRequest`

兼容 Google Gemini API。

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `contents` | GeminiChatContent[] | 是 | 内容数组 |
| `safetySettings` | GeminiChatSafetySettings[] | 否 | 安全设置 |
| `generationConfig` | GeminiChatGenerationConfig | 否 | 生成配置 |
| `tools` | json.RawMessage | 否 | 工具列表 |
| `toolConfig` | ToolConfig | 否 | 工具配置 |
| `systemInstruction` | GeminiChatContent | 否 | 系统指令（同时支持 `system_instruction` 蛇形命名） |
| `cachedContent` | string | 否 | 缓存内容名称 |

**GeminiChatContent 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `role` | string | 角色：`user`、`model` |
| `parts` | GeminiPart[] | 内容部件列表 |

**GeminiPart 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `text` | string | 文本内容 |
| `thought` | bool | 是否为思考内容 |
| `inlineData` | GeminiInlineData | 内联数据（支持 `inline_data` 蛇形命名） |
| `functionCall` | FunctionCall | 函数调用 |
| `functionResponse` | GeminiFunctionResponse | 函数响应 |
| `fileData` | GeminiFileData | 文件数据 |
| `executableCode` | GeminiPartExecutableCode | 可执行代码 |
| `codeExecutionResult` | GeminiPartCodeExecutionResult | 代码执行结果 |

**GeminiInlineData 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `mimeType` | string | MIME 类型（同时支持 `mime_type`） |
| `data` | string | Base64 编码数据 |

**GeminiChatGenerationConfig 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `temperature` | float64 | 采样温度（同时支持 `temperature`） |
| `topP` | float64 | 核采样（同时支持 `top_p`） |
| `topK` | float64 | Top-K（同时支持 `top_k`） |
| `maxOutputTokens` | uint | 最大输出 token（同时支持 `max_output_tokens`） |
| `candidateCount` | int | 候选数量（同时支持 `candidate_count`） |
| `stopSequences` | string[] | 停止序列（同时支持 `stop_sequences`） |
| `responseMimeType` | string | 响应 MIME 类型（同时支持 `response_mime_type`） |
| `responseSchema` | any | 响应 Schema（同时支持 `response_schema`） |
| `presencePenalty` | float32 | 存在惩罚（同时支持 `presence_penalty`） |
| `frequencyPenalty` | float32 | 频率惩罚（同时支持 `frequency_penalty`） |
| `responseLogprobs` | bool | 是否返回 logprobs |
| `logprobs` | int32 | logprobs 数量 |
| `seed` | int64 | 随机种子 |
| `responseModalities` | string[] | 响应模态（同时支持 `response_modalities`） |
| `thinkingConfig` | GeminiThinkingConfig | 思考配置（同时支持 `thinking_config`） |
| `speechConfig` | json.RawMessage | 语音配置 |
| `imageConfig` | json.RawMessage | 图像配置 |

> 注：Gemini 的大部分配置字段同时支持 camelCase 和 snake_case 命名，snake_case 优先级更高。

**GeminiThinkingConfig 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `includeThoughts` | bool | 是否包含思考过程 |
| `thinkingBudget` | int | 思考 token 预算 |
| `thinkingLevel` | string | 思考级别 |

### 请求示例

```json
{
  "contents": [
    {
      "role": "user",
      "parts": [{"text": "你好！"}]
    }
  ],
  "generationConfig": {
    "temperature": 0.7,
    "maxOutputTokens": 1024
  }
}
```

### 响应参数: `GeminiChatResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `candidates` | GeminiChatCandidate[] | 候选响应列表 |
| `promptFeedback` | GeminiChatPromptFeedback | 提示词反馈（可选） |
| `usageMetadata` | GeminiUsageMetadata | 用量元数据 |

**GeminiChatCandidate 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `content` | GeminiChatContent | 响应内容 |
| `finishReason` | string | 完成原因 |
| `index` | int64 | 索引 |
| `safetyRatings` | GeminiChatSafetyRating[] | 安全评级 |

**GeminiUsageMetadata 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `promptTokenCount` | int | 输入 token 数 |
| `candidatesTokenCount` | int | 输出 token 数 |
| `totalTokenCount` | int | 总 token 数 |
| `thoughtsTokenCount` | int | 思考 token 数 |
| `cachedContentTokenCount` | int | 缓存内容 token 数 |
| `toolUsePromptTokenCount` | int | 工具使用输入 token 数 |

### Gemini Embedding 请求: `GeminiEmbeddingRequest`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `model` | string | 模型名称 |
| `content` | GeminiChatContent | 内容 |
| `taskType` | string | 任务类型 |
| `title` | string | 标题 |
| `outputDimensionality` | int | 输出维度 |

---

## 10. Rerank 重排序接口

### 请求参数: `RerankRequest`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `documents` | any[] | 是 | 文档列表 |
| `query` | string | 是 | 查询文本 |
| `model` | string | 是 | 模型名称 |
| `top_n` | int | 否 | 返回前 N 个结果 |
| `return_documents` | bool | 否 | 是否返回文档内容 |
| `max_chunk_per_doc` | int | 否 | 每文档最大分块数 |
| `overlap_tokens` | int | 否 | 重叠 token 数 |

### 请求示例

```json
{
  "model": "rerank-v1",
  "query": "什么是人工智能？",
  "documents": [
    "人工智能是计算机科学的一个分支。",
    "机器学习是人工智能的子领域。",
    "今天天气很好。"
  ],
  "top_n": 2,
  "return_documents": true
}
```

### 响应参数: `RerankResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `results` | RerankResponseResult[] | 排序结果列表 |
| `usage` | Usage | 用量统计 |

**RerankResponseResult 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `document` | any | 文档内容（可选） |
| `index` | int | 原始索引 |
| `relevance_score` | float64 | 相关性分数 |

---

## 11. 任务系统 (Task)

### 通用任务响应: `TaskDto`

用于异步任务（视频生成、Midjourney、Suno 等）的通用结构。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | int64 | 任务 ID |
| `created_at` | int64 | 创建时间 |
| `updated_at` | int64 | 更新时间 |
| `task_id` | string | 任务唯一标识 |
| `platform` | string | 平台名称 |
| `user_id` | int | 用户 ID |
| `group` | string | 用户组 |
| `channel_id` | int | 渠道 ID |
| `quota` | int | 配额消耗 |
| `action` | string | 任务动作 |
| `status` | string | 任务状态 |
| `fail_reason` | string | 失败原因 |
| `result_url` | string | 任务结果 URL（可选，如视频地址） |
| `submit_time` | int64 | 提交时间 |
| `start_time` | int64 | 开始时间 |
| `finish_time` | int64 | 完成时间 |
| `progress` | string | 进度 |
| `properties` | any | 属性 |
| `username` | string | 用户名（可选） |
| `data` | json.RawMessage | 任务数据 |

### 任务查询请求: `FetchReq`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `ids` | string[] | 是 | 任务 ID 列表 |

### 泛型任务响应: `TaskResponse<T>`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `code` | string | 状态码，`success` 表示成功 |
| `message` | string | 消息 |
| `data` | T | 响应数据 |

---

## 12. Midjourney 图像生成

### 请求参数: `MidjourneyRequest`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `prompt` | string | 是 | 提示词 |
| `action` | string | 是 | 动作：`imagine`、`upsample`、`variation`、`describe` 等 |
| `customId` | string | 否 | 自定义 ID |
| `botType` | string | 否 | 机器人类型 |
| `notifyHook` | string | 否 | 回调通知 URL |
| `index` | int | 否 | 索引 |
| `state` | string | 否 | 状态 |
| `taskId` | string | 否 | 任务 ID（用于关联操作） |
| `base64Array` | string[] | 否 | Base64 图像数组 |
| `content` | string | 否 | 内容 |
| `maskBase64` | string | 否 | 遮罩 Base64（用于局部重绘） |

### 换脸请求: `SwapFaceRequest`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `sourceBase64` | string | 是 | 源人脸图像 Base64 |
| `targetBase64` | string | 是 | 目标图像 Base64 |

### Midjourney 响应: `MidjourneyResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `code` | int | 状态码 |
| `description` | string | 描述 |
| `properties` | any | 属性 |
| `result` | string | 结果（任务 ID） |

### Midjourney 任务详情: `MidjourneyDto`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | Midjourney 任务 ID |
| `action` | string | 动作 |
| `prompt` | string | 提示词 |
| `promptEn` | string | 英文提示词 |
| `description` | string | 描述 |
| `status` | string | 状态 |
| `progress` | string | 进度 |
| `imageUrl` | string | 图像 URL |
| `videoUrl` | string | 视频 URL |
| `failReason` | string | 失败原因 |
| `submitTime` | int64 | 提交时间 |
| `startTime` | int64 | 开始时间 |
| `finishTime` | int64 | 完成时间 |
| `buttons` | any | 可用按钮 |
| `properties` | Properties | 属性 |

---

## 13. Suno 音乐生成

### 请求参数: `SunoSubmitReq`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `gpt_description_prompt` | string | 否 | GPT 描述提示词 |
| `prompt` | string | 否 | 歌词/提示词 |
| `mv` | string | 否 | 模型版本 |
| `title` | string | 否 | 歌曲标题 |
| `tags` | string | 否 | 风格标签 |
| `continue_at` | float64 | 否 | 续写时间点 |
| `task_id` | string | 否 | 任务 ID（续写时使用） |
| `continue_clip_id` | string | 否 | 续写片段 ID |
| `make_instrumental` | bool | 否 | 是否纯音乐 |

### Suno 任务响应: `SunoDataResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `task_id` | string | 任务 ID |
| `action` | string | 任务类型：`song`、`lyrics`、`description-mode` |
| `status` | string | 状态：`submitted`、`queueing`、`processing`、`success`、`failed` |
| `fail_reason` | string | 失败原因 |
| `submit_time` | int64 | 提交时间 |
| `start_time` | int64 | 开始时间 |
| `finish_time` | int64 | 完成时间 |
| `data` | json.RawMessage | 歌曲数据 |

### Suno 歌曲: `SunoSong`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 歌曲 ID |
| `video_url` | string | 视频 URL |
| `audio_url` | string | 音频 URL |
| `image_url` | string | 图像 URL |
| `image_large_url` | string | 大图 URL |
| `model_name` | string | 模型名称 |
| `status` | string | 状态 |
| `title` | string | 标题 |
| `text` | string | 歌词 |
| `metadata` | SunoMetadata | 元数据 |

---

## 14. Realtime 实时语音接口

### 事件结构: `RealtimeEvent`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `event_id` | string | 事件 ID |
| `type` | string | 事件类型 |
| `session` | RealtimeSession | 会话配置（可选） |
| `item` | RealtimeItem | 内容项（可选） |
| `error` | OpenAIError | 错误信息（可选） |
| `response` | RealtimeResponse | 响应（可选） |
| `delta` | string | 增量内容（可选） |
| `audio` | string | 音频数据（可选） |

**事件类型:**

| 值 | 说明 |
|----|------|
| `error` | 错误 |
| `session.update` | 更新会话 |
| `conversation.item.create` | 创建对话项 |
| `response.create` | 创建响应 |
| `input_audio_buffer.append` | 追加音频缓冲 |
| `response.done` | 响应完成 |
| `session.updated` | 会话已更新 |
| `session.created` | 会话已创建 |
| `response.audio.delta` | 音频增量 |
| `response.audio_transcript.delta` | 转写增量 |
| `response.function_call_arguments.delta` | 函数调用参数增量 |
| `response.function_call_arguments.done` | 函数调用参数完成 |
| `conversation.item.created` | 对话项已创建 |

**RealtimeSession 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `modalities` | string[] | 模态类型 |
| `instructions` | string | 指令 |
| `voice` | string | 语音类型 |
| `input_audio_format` | string | 输入音频格式 |
| `output_audio_format` | string | 输出音频格式 |
| `input_audio_transcription` | InputAudioTranscription | 音频转写配置 |
| `turn_detection` | any | 轮次检测配置 |
| `tools` | RealTimeTool[] | 工具列表 |
| `tool_choice` | string | 工具选择 |
| `temperature` | float64 | 温度 |

**RealtimeItem 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 项目 ID |
| `type` | string | 类型 |
| `status` | string | 状态 |
| `role` | string | 角色 |
| `content` | RealtimeContent[] | 内容列表 |
| `name` | string | 名称（可选） |
| `tool_calls` | any | 工具调用（可选） |
| `call_id` | string | 调用 ID（可选） |

**RealtimeContent 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 类型：`text`、`audio` |
| `text` | string | 文本（可选） |
| `audio` | string | Base64 编码的音频（可选） |
| `transcript` | string | 转写文本（可选） |

---

## 15. 用户设置 (User Settings)

### 请求/响应参数: `UserSetting`

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `notify_type` | string | 否 | 通知类型：`email`、`webhook`、`bark`、`gotify` |
| `quota_warning_threshold` | float64 | 否 | 额度预警阈值 |
| `webhook_url` | string | 否 | Webhook 地址 |
| `webhook_secret` | string | 否 | Webhook 密钥 |
| `notification_email` | string | 否 | 通知邮箱地址 |
| `bark_url` | string | 否 | Bark 推送 URL |
| `gotify_url` | string | 否 | Gotify 服务器地址 |
| `gotify_token` | string | 否 | Gotify 应用令牌 |
| `gotify_priority` | int | 否 | Gotify 消息优先级 |
| `upstream_model_update_notify_enabled` | bool | 否 | 是否接收上游模型更新通知（仅管理员） |
| `accept_unset_model_ratio_model` | bool | 否 | 是否接受未设置价格的模型 |
| `record_ip_log` | bool | 否 | 是否记录请求和错误日志 IP |
| `sidebar_modules` | string | 否 | 左侧边栏模块配置 |
| `billing_preference` | string | 否 | 扣费策略：`subscription`（订阅）/ `wallet`（钱包） |
| `language` | string | 否 | 用户语言偏好：`zh`（中文）、`en`（英文） |

### 请求示例

```json
{
  "notify_type": "email",
  "notification_email": "user@example.com",
  "quota_warning_threshold": 100.0,
  "language": "zh",
  "billing_preference": "wallet"
}
```

---

## 16. 渠道设置 (Channel Settings)

### ChannelSettings

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `force_format` | bool | 否 | 强制格式化 |
| `thinking_to_content` | bool | 否 | 是否将思考内容转为正文 |
| `proxy` | string | 否 | 代理地址 |
| `pass_through_body_enabled` | bool | 否 | 是否启用请求体透传 |
| `system_prompt` | string | 否 | 系统提示词 |
| `system_prompt_override` | bool | 否 | 是否覆盖系统提示词 |

### ChannelOtherSettings

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `azure_responses_version` | string | Azure Responses API 版本 |
| `homepage_display_base_url` | string | 首页显示的 Base URL |
| `vertex_key_type` | string | Vertex 密钥类型：`json`、`api_key` |
| `openrouter_enterprise` | bool | 是否 OpenRouter 企业版 |
| `claude_beta_query` | bool | Claude 渠道是否强制追加 `?beta=true` |
| `allow_service_tier` | bool | 是否允许 `service_tier` 透传（默认过滤） |
| `allow_inference_geo` | bool | 是否允许 `inference_geo` 透传（默认过滤，数据驻留合规） |
| `allow_safety_identifier` | bool | 是否允许 `safety_identifier` 透传（默认过滤，隐私保护） |
| `disable_store` | bool | 是否禁用 `store` 透传 |
| `allow_include_obfuscation` | bool | 是否允许 `stream_options.include_obfuscation` 透传 |
| `aws_key_type` | string | AWS 密钥类型：`ak_sk`、`api_key` |
| `upstream_model_update_check_enabled` | bool | 是否检测上游模型更新 |
| `upstream_model_update_auto_sync_enabled` | bool | 是否自动同步上游模型更新 |
| `upstream_model_update_last_check_time` | int64 | 上次检测时间 |
| `upstream_model_update_last_detected_models` | string[] | 上次检测到的可加入模型 |
| `upstream_model_update_last_removed_models` | string[] | 上次检测到的可删除模型 |
| `upstream_model_update_ignored_models` | string[] | 手动忽略的模型 |

---

## 17. 模型定价 (Pricing)

### OpenAIModels

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 模型 ID |
| `object` | string | 对象类型，固定为 `model` |
| `created` | int | 创建时间戳 |
| `owned_by` | string | 所有者 |
| `supported_endpoint_types` | string[] | 支持的端点类型 |

### AnthropicModel

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | string | 模型 ID |
| `created_at` | string | 创建时间 |
| `display_name` | string | 显示名称 |
| `type` | string | 类型 |

### GeminiModel

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `name` | any | 模型名称 |
| `displayName` | any | 显示名称 |
| `description` | any | 描述 |
| `inputTokenLimit` | any | 输入 token 限制 |
| `outputTokenLimit` | any | 输出 token 限制 |
| `supportedGenerationMethods` | any[] | 支持的生成方法 |
| `thinking` | any | 思考能力 |
| `temperature` | any | 默认温度 |
| `maxTemperature` | any | 最大温度 |
| `topP` | any | Top-P |
| `topK` | any | Top-K |

---

## 18. 通知系统 (Notify)

### Notify 结构

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 通知类型 |
| `title` | string | 标题 |
| `content` | string | 内容，支持 `{{value}}` 占位符 |
| `values` | any[] | 值列表，用于替换占位符 |

**通知类型常量:**

| 值 | 说明 |
|----|------|
| `quota_exceed` | 配额超限 |
| `channel_update` | 渠道更新 |
| `channel_test` | 渠道测试 |

---

## 19. 敏感词检测 (Sensitive)

### 响应参数: `SensitiveResponse`

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `sensitive_words` | string[] | 命中的敏感词列表 |
| `content` | string | 处理后的内容 |

---

## 20. Playground 会话

### PlayGroundRequest

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `model` | string | 模型名称（可选） |
| `group` | string | 用户组（可选） |
| `token_id` | int | Token ID（可选） |

### PlaygroundSessionRequest

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `mode` | string | 是 | 会话模式 |
| `title` | string | 否 | 会话标题 |

---

## 21. 上游同步 (Ratio Sync)

### UpstreamDTO

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| `id` | int | 否 | 上游 ID |
| `name` | string | 是 | 上游名称（`binding:"required"`） |
| `base_url` | string | 是 | 上游 Base URL（`binding:"required"`） |
| `endpoint` | string | 否 | 端点路径 |

### UpstreamRequest

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `channel_ids` | int64[] | 渠道 ID 列表 |
| `upstreams` | UpstreamDTO[] | 上游列表 |
| `timeout` | int | 超时时间 |

### TestResult (连通性测试结果)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `name` | string | 名称 |
| `status` | string | 状态 |
| `error` | string | 错误信息（可选） |

### DifferenceItem (差异项)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `current` | any | 本地当前值 |
| `upstreams` | map[string]any | 各渠道上游值 |
| `confidence` | map[string]bool | 置信度 |

### SyncableChannel (可同步渠道)

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `id` | int | 渠道 ID |
| `name` | string | 渠道名称 |
| `base_url` | string | Base URL |
| `display_base_url` | string | 显示用 Base URL（可选） |
| `status` | int | 状态 |
| `type` | int | 渠道类型 |

---

## 22. 错误响应格式

### GeneralErrorResponse

通用错误响应结构，兼容多种上游错误格式。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `error` | json.RawMessage | 错误信息（对象或字符串） |
| `message` | string | 消息 |
| `msg` | string | 消息（备选） |
| `err` | string | 错误（备选） |
| `error_msg` | string | 错误消息（备选） |
| `metadata` | json.RawMessage | 元数据（可选） |
| `detail` | string | 详情（可选） |
| `header.message` | string | 头部消息 |
| `response.error.message` | string | 响应错误消息 |

**错误消息提取优先级:**
1. `error` 字段（对象中的 `message` 或字符串）
2. `message`
3. `msg`
4. `err`
5. `error_msg`
6. `detail`
7. `header.message`
8. `response.error.message`

### OpenAIErrorWithStatusCode

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `error` | OpenAIError | OpenAI 错误对象 |
| `status_code` | int | HTTP 状态码 |

**OpenAIError 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `message` | string | 错误消息 |
| `type` | string | 错误类型 |
| `param` | string | 相关参数 |
| `code` | any | 错误代码 |

### ClaudeErrorWithStatusCode

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `error` | ClaudeError | Claude 错误对象 |
| `status_code` | int | HTTP 状态码 |

**ClaudeError 结构:**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `type` | string | 错误类型 |
| `message` | string | 错误消息 |

### TaskError

| 字段名 | 类型 | 说明 |
|--------|------|------|
| `code` | string | 错误代码 |
| `message` | string | 错误消息 |
| `data` | any | 附加数据 |

### 错误响应示例

```json
{
  "error": {
    "message": "Invalid API key",
    "type": "authentication_error",
    "param": null,
    "code": "invalid_api_key"
  },
  "status_code": 401
}
```

---

## 验证规则汇总

| DTO | 字段 | 规则 |
|-----|------|------|
| `ImageRequest` | `prompt` | `binding:"required"` - 必填 |
| `UpstreamDTO` | `name` | `binding:"required"` - 必填 |
| `UpstreamDTO` | `base_url` | `binding:"required"` - 必填 |

## 特殊类型说明

### IntValue / BoolValue

`IntValue` 和 `BoolValue` 是自定义类型，支持从 JSON 字符串或数字/布尔值反序列化。例如：
- `IntValue` 可以接受 `42` 或 `"42"`
- `BoolValue` 可以接受 `true` 或 `"true"`

这在处理某些上游 API 返回不一致类型时非常有用。
