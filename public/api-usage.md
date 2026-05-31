# DogAPI 使用指南

本指南帮助你快速上手 DogAPI，从发送第一个请求到处理各种场景，一步步带你完成集成。

---

## 1. 快速开始（第一个 API 请求）

DogAPI 兼容 OpenAI 的接口格式，如果你用过 OpenAI 的 API，几乎可以直接迁移过来。只需三步：

1. 注册账号并获取 API Key
2. 将 API Key 放入请求头
3. 发送请求

下面是一个最简单的示例，用 curl 发送一条聊天消息：

```bash
curl https://api.example.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-api-key" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {"role": "user", "content": "你好，请用一句话介绍自己。"}
    ]
  }'
```

如果一切正常，你会收到类似这样的响应：

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "model": "gpt-4o",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "你好！我是一个 AI 助手，随时为你提供帮助。"
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

---

## 2. 获取 API Key

要调用 DogAPI，你需要一个 API Key（以 `sk-` 开头的字符串）。

**获取步骤：**

1. 登录 DogAPI 控制台
2. 进入「令牌管理」页面
3. 点击「创建新令牌」，填写名称（如 "我的项目"）
4. 创建完成后，复制完整的 API Key

**注意事项：**

- API Key 只在创建时显示一次，请立即保存好
- 可以为不同项目创建不同的 Key，方便管理和追踪用量
- 每个 Key 可以单独设置额度上限和过期时间
- 如果 Key 泄露了，可以随时禁用或删除它

**验证你的 Key 是否可用：**

```bash
curl https://api.example.com/v1/models \
  -H "Authorization: Bearer sk-your-api-key"
```

如果返回了模型列表，说明你的 Key 是有效的。

---

## 3. 发送聊天请求

### 3.1 使用 curl

**基本请求：**

```bash
curl https://api.example.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-api-key" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {"role": "system", "content": "你是一个专业的翻译助手。"},
      {"role": "user", "content": "请把下面这句话翻译成英文：今天天气真不错。"}
    ],
    "temperature": 0.7,
    "max_tokens": 500
  }'
```

**消息格式说明：**

每条消息包含两个字段：

| 字段 | 说明 |
|------|------|
| `role` | 消息角色：`system`（系统指令）、`user`（用户输入）、`assistant`（AI 回复） |
| `content` | 消息内容 |

你可以发送多条消息来构建对话历史：

```json
{
  "messages": [
    {"role": "system", "content": "你是一个美食推荐助手。"},
    {"role": "user", "content": "推荐一道简单的家常菜。"},
    {"role": "assistant", "content": "推荐番茄炒蛋，简单又美味。"},
    {"role": "user", "content": "具体怎么做？"}
  ]
}
```

**常用参数：**

| 参数 | 类型 | 说明 |
|------|------|------|
| `model` | string | 要使用的模型名称，如 `gpt-4o`、`claude-3-5-sonnet` |
| `messages` | array | 消息数组（必填） |
| `temperature` | number | 采样温度，0-2 之间。值越低回复越确定，值越高越有创意。默认 1 |
| `max_tokens` | number | 最大输出 token 数量 |
| `top_p` | number | 核采样概率，0-1 之间 |
| `stream` | boolean | 是否启用流式输出，默认 `false` |
| `stop` | string/array | 停止生成的标记 |
| `response_format` | object | 响应格式，如 `{"type": "json_object"}` 可强制返回 JSON |

### 3.2 使用 Python

首先安装 OpenAI 官方 SDK：

```bash
pip install openai
```

**基本用法：**

```python
from openai import OpenAI

# Initialize the client with your API key
client = OpenAI(
    api_key="sk-your-api-key",
    base_url="https://api.example.com/v1"  # DogAPI base URL
)

# Send a chat request
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "你是一个有帮助的助手。"},
        {"role": "user", "content": "用三句话介绍量子计算。"}
    ],
    temperature=0.7,
    max_tokens=500
)

# Print the response
print(response.choices[0].message.content)

# Print token usage
print(f"Input tokens: {response.usage.prompt_tokens}")
print(f"Output tokens: {response.usage.completion_tokens}")
```

**多轮对话：**

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-api-key",
    base_url="https://api.example.com/v1"
)

# Maintain conversation history
messages = [
    {"role": "system", "content": "你是一个编程导师。"}
]

def chat(user_input):
    messages.append({"role": "user", "content": user_input})

    response = client.chat.completions.create(
        model="gpt-4o",
        messages=messages
    )

    assistant_reply = response.choices[0].message.content
    messages.append({"role": "assistant", "content": assistant_reply})

    return assistant_reply

# First turn
print(chat("Python 中列表和元组有什么区别？"))

# Second turn (the model remembers the context)
print(chat("那什么时候应该用元组而不是列表？"))
```

### 3.3 使用 Node.js

首先安装 OpenAI 官方 SDK：

```bash
npm install openai
```

**基本用法：**

```javascript
import OpenAI from "openai";

// Initialize the client with your API key
const client = new OpenAI({
  apiKey: "sk-your-api-key",
  baseURL: "https://api.example.com/v1", // DogAPI base URL
});

// Send a chat request
const response = await client.chat.completions.create({
  model: "gpt-4o",
  messages: [
    { role: "system", content: "你是一个有帮助的助手。" },
    { role: "user", content: "用三句话介绍量子计算。" },
  ],
  temperature: 0.7,
  max_tokens: 500,
});

// Print the response
console.log(response.choices[0].message.content);

// Print token usage
console.log(`Input tokens: ${response.usage.prompt_tokens}`);
console.log(`Output tokens: ${response.usage.completion_tokens}`);
```

**TypeScript 写法：**

```typescript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-your-api-key",
  baseURL: "https://api.example.com/v1",
});

async function main() {
  const response = await client.chat.completions.create({
    model: "gpt-4o",
    messages: [
      { role: "system", content: "你是一个翻译助手。" },
      { role: "user", content: "翻译成英文：今天天气真不错。" },
    ],
  });

  console.log(response.choices[0].message.content);
}

main();
```

---

## 4. 流式输出

流式输出可以让 AI 的回复逐字显示，而不是等全部生成完毕后一次性返回。这对提升用户体验非常重要，尤其是在回复较长时。

### 4.1 curl 流式请求

```bash
curl https://api.example.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-api-key" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {"role": "user", "content": "写一首关于春天的短诗。"}
    ],
    "stream": true
  }'
```

流式响应是一系列以 `data: ` 开头的行，每个 chunk 包含一小段内容：

```
data: {"id":"chatcmpl-abc","choices":[{"delta":{"content":"春"},"finish_reason":null}]}

data: {"id":"chatcmpl-abc","choices":[{"delta":{"content":"风"},"finish_reason":null}]}

data: {"id":"chatcmpl-abc","choices":[{"delta":{"content":"拂"},"finish_reason":null}]}

...

data: [DONE]
```

当收到 `data: [DONE]` 时表示生成结束。

### 4.2 Python 流式示例

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-api-key",
    base_url="https://api.example.com/v1"
)

# Enable streaming
stream = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "user", "content": "详细解释什么是机器学习。"}
    ],
    stream=True
)

# Print each chunk as it arrives
for chunk in stream:
    content = chunk.choices[0].delta.content
    if content is not None:
        print(content, end="", flush=True)

print()  # New line after streaming is done
```

### 4.3 Node.js 流式示例

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-your-api-key",
  baseURL: "https://api.example.com/v1",
});

// Enable streaming
const stream = await client.chat.completions.create({
  model: "gpt-4o",
  messages: [
    { role: "user", content: "详细解释什么是机器学习。" },
  ],
  stream: true,
});

// Print each chunk as it arrives
for await (const chunk of stream) {
  const content = chunk.choices[0]?.delta?.content;
  if (content) {
    process.stdout.write(content);
  }
}

console.log(); // New line after streaming is done
```

### 4.4 流式模式下获取用量统计

如果你想在流式响应中也获取 token 用量信息，可以加上 `stream_options` 参数：

```json
{
  "model": "gpt-4o",
  "messages": [{"role": "user", "content": "你好"}],
  "stream": true,
  "stream_options": {
    "include_usage": true
  }
}
```

这样在最后一个 chunk 中会包含 `usage` 字段。

---

## 5. 使用图片/多模态

DogAPI 支持发送图片给支持视觉的模型（如 `gpt-4o`、`claude-3-5-sonnet`），让模型理解图片内容。

### 5.1 通过 URL 发送图片

```bash
curl https://api.example.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-api-key" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {
        "role": "user",
        "content": [
          {"type": "text", "text": "这张图片里有什么？"},
          {
            "type": "image_url",
            "image_url": {
              "url": "https://example.com/photo.jpg"
            }
          }
        ]
      }
    ]
  }'
```

### 5.2 通过 Base64 发送图片

如果你的图片是本地文件，可以将其编码为 Base64 后发送：

```python
from openai import OpenAI
import base64

client = OpenAI(
    api_key="sk-your-api-key",
    base_url="https://api.example.com/v1"
)

# Read and encode a local image
with open("photo.jpg", "rb") as f:
    image_data = base64.b64encode(f.read()).decode("utf-8")

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "请描述这张图片的内容。"},
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{image_data}"
                    }
                }
            ]
        }
    ]
)

print(response.choices[0].message.content)
```

### 5.3 多图分析

你可以在一条消息中发送多张图片：

```json
{
  "model": "gpt-4o",
  "messages": [
    {
      "role": "user",
      "content": [
        {"type": "text", "text": "对比这两张图片有什么不同？"},
        {
          "type": "image_url",
          "image_url": {"url": "https://example.com/before.jpg"}
        },
        {
          "type": "image_url",
          "image_url": {"url": "https://example.com/after.jpg"}
        }
      ]
    }
  ]
}
```

### 5.4 图像生成

DogAPI 还支持用 DALL-E 等模型生成图片：

```bash
curl https://api.example.com/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-api-key" \
  -d '{
    "model": "dall-e-3",
    "prompt": "一只在月光下奔跑的白色猫咪，水彩画风格",
    "n": 1,
    "size": "1024x1024",
    "quality": "hd"
  }'
```

响应中会包含生成图片的 URL：

```json
{
  "created": 1700000000,
  "data": [
    {
      "url": "https://example.com/generated-image.png",
      "revised_prompt": "A white cat running under the moonlight..."
    }
  ]
}
```

**支持的图片尺寸：**

| 尺寸 | 说明 |
|------|------|
| `256x256` | 小尺寸 |
| `512x512` | 中尺寸 |
| `1024x1024` | 标准尺寸 |
| `1024x1792` | 竖版 |
| `1792x1024` | 横版 |

---

## 6. 常见模型切换

DogAPI 支持多种 AI 模型，你只需修改请求中的 `model` 参数即可切换。

### 6.1 查看可用模型

```bash
curl https://api.example.com/v1/models \
  -H "Authorization: Bearer sk-your-api-key"
```

返回结果示例：

```json
{
  "data": [
    {"id": "gpt-4o", "owned_by": "openai"},
    {"id": "gpt-4o-mini", "owned_by": "openai"},
    {"id": "claude-3-5-sonnet-20241022", "owned_by": "anthropic"},
    {"id": "gemini-pro", "owned_by": "google"}
  ]
}
```

### 6.2 使用不同厂商的模型

**OpenAI 模型：**

```json
{
  "model": "gpt-4o",
  "messages": [{"role": "user", "content": "你好"}]
}
```

**Claude 模型：**

```json
{
  "model": "claude-3-5-sonnet-20241022",
  "messages": [{"role": "user", "content": "你好"}]
}
```

**Gemini 模型：**

```json
{
  "model": "gemini-pro",
  "messages": [{"role": "user", "content": "你好"}]
}
```

请求格式完全一样，只需要改 `model` 名称。DogAPI 会自动处理不同模型之间的格式差异。

### 6.3 模型选择建议

| 场景 | 推荐模型 | 特点 |
|------|----------|------|
| 日常对话 | `gpt-4o-mini` | 速度快，成本低 |
| 复杂推理 | `gpt-4o` | 能力强，适合复杂任务 |
| 代码生成 | `claude-3-5-sonnet` | 代码质量高 |
| 长文本处理 | `claude-3-5-sonnet` | 支持长上下文 |
| 图片理解 | `gpt-4o` | 多模态能力强 |

### 6.4 Embedding 向量化

如果你需要将文本转换为向量（用于搜索、相似度计算等），可以使用 Embedding 接口：

```bash
curl https://api.example.com/v1/embeddings \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-your-api-key" \
  -d '{
    "model": "text-embedding-3-small",
    "input": ["你好世界", "这是一个测试"]
  }'
```

返回结果中每个输入文本对应一个向量数组：

```json
{
  "data": [
    {"index": 0, "embedding": [0.0023, -0.0093, ...]},
    {"index": 1, "embedding": [0.0156, 0.0234, ...]}
  ],
  "usage": {
    "prompt_tokens": 8,
    "total_tokens": 8
  }
}
```

---

## 7. 错误处理

调用 API 时可能会遇到各种错误，了解常见错误类型和处理方式可以帮助你更快地解决问题。

### 7.1 常见 HTTP 状态码

| 状态码 | 含义 | 处理方式 |
|--------|------|----------|
| 200 | 成功 | 正常处理响应 |
| 400 | 请求参数错误 | 检查请求体格式和必填字段 |
| 401 | 认证失败 | 检查 API Key 是否正确或已过期 |
| 403 | 权限不足 | 确认你的 Key 有权限访问该模型 |
| 404 | 资源不存在 | 检查请求的 URL 和模型名称 |
| 429 | 请求过于频繁 | 降低请求频率或等待一段时间后重试 |
| 500 | 服务器内部错误 | 稍后重试，如持续出现请联系支持 |
| 502 | 上游服务错误 | 模型提供商可能暂时不可用，稍后重试 |

### 7.2 错误响应格式

当请求失败时，响应体中会包含错误信息：

```json
{
  "error": {
    "message": "Invalid API key",
    "type": "authentication_error",
    "param": null,
    "code": "invalid_api_key"
  }
}
```

主要字段说明：

| 字段 | 说明 |
|------|------|
| `error.message` | 错误的具体描述，告诉你哪里出了问题 |
| `error.type` | 错误类型，方便程序判断 |
| `error.code` | 错误代码，可用于精确匹配 |

### 7.3 Python 错误处理示例

```python
from openai import OpenAI
from openai import (
    AuthenticationError,
    RateLimitError,
    BadRequestError,
    APIError,
)

client = OpenAI(
    api_key="sk-your-api-key",
    base_url="https://api.example.com/v1"
)

try:
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": "你好"}]
    )
    print(response.choices[0].message.content)

except AuthenticationError:
    # API Key is invalid or expired
    print("认证失败，请检查你的 API Key。")

except RateLimitError:
    # Too many requests
    print("请求过于频繁，请稍后重试。")

except BadRequestError as e:
    # Invalid request parameters
    print(f"请求参数错误：{e}")

except APIError as e:
    # Other API errors
    print(f"API 错误：{e}")
```

### 7.4 Node.js 错误处理示例

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-your-api-key",
  baseURL: "https://api.example.com/v1",
});

try {
  const response = await client.chat.completions.create({
    model: "gpt-4o",
    messages: [{ role: "user", content: "你好" }],
  });
  console.log(response.choices[0].message.content);

} catch (error) {
  if (error instanceof OpenAI.AuthenticationError) {
    // API Key is invalid or expired
    console.error("认证失败，请检查你的 API Key。");
  } else if (error instanceof OpenAI.RateLimitError) {
    // Too many requests
    console.error("请求过于频繁，请稍后重试。");
  } else if (error instanceof OpenAI.BadRequestError) {
    // Invalid request parameters
    console.error(`请求参数错误：${error.message}`);
  } else {
    // Other errors
    console.error(`请求失败：${error.message}`);
  }
}
```

### 7.5 重试策略建议

对于临时性错误（如 429 限流、502 上游错误），建议实现指数退避重试：

```python
import time
from openai import OpenAI, RateLimitError, APIError

client = OpenAI(
    api_key="sk-your-api-key",
    base_url="https://api.example.com/v1"
)

def call_with_retry(messages, model="gpt-4o", max_retries=3):
    for attempt in range(max_retries):
        try:
            response = client.chat.completions.create(
                model=model,
                messages=messages
            )
            return response.choices[0].message.content

        except (RateLimitError, APIError):
            if attempt < max_retries - 1:
                wait_time = 2 ** attempt  # 1s, 2s, 4s...
                print(f"Retrying in {wait_time}s... (attempt {attempt + 1}/{max_retries})")
                time.sleep(wait_time)
            else:
                raise

# Usage
result = call_with_retry([
    {"role": "user", "content": "你好"}
])
print(result)
```

### 7.6 常见问题排查

**问题：返回 401 Unauthorized**
- 检查 API Key 是否正确复制（以 `sk-` 开头）
- 确认 Key 没有被禁用或删除
- 检查 Key 是否已过期

**问题：返回 403 Forbidden**
- 你的 Key 可能没有权限访问所请求的模型
- 尝试换一个模型，或在控制台检查 Key 的模型限制设置

**问题：返回 429 Too Many Requests**
- 你的请求频率超过了限制
- 减少并发请求数量
- 实现指数退避重试

**问题：返回模型不存在**
- 检查模型名称是否拼写正确
- 用 `/v1/models` 接口查看当前可用的模型列表
- 确认你的 Key 有权限访问该模型

**问题：响应速度慢**
- 尝试使用更快的模型（如 `gpt-4o-mini`）
- 减少 `max_tokens` 的值
- 使用流式输出来改善用户体验
