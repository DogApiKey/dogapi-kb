# API 端点说明

## 基础信息

- Base URL: `https://api.dogapi.com/v1`
- 认证方式: Bearer Token
- 请求格式: JSON
- 响应格式: JSON

## 聊天补全

### POST /v1/chat/completions

创建聊天补全请求。

**请求参数:**

| 参数 | 类型 | 必填 | 说明 |
|---|---|---|---|
| model | string | 是 | 模型名称 |
| messages | array | 是 | 消息列表 |
| temperature | number | 否 | 温度参数 (0-2) |
| max_tokens | integer | 否 | 最大生成 token 数 |
| stream | boolean | 否 | 是否流式输出 |

**示例请求:**

```json
{
  "model": "gpt-4o",
  "messages": [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Hello!"}
  ],
  "stream": false
}
```

**示例响应:**

```json
{
  "id": "chatcmpl-xxx",
  "object": "chat.completion",
  "created": 1234567890,
  "model": "gpt-4o",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Hello! How can I help you today?"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 10,
    "completion_tokens": 9,
    "total_tokens": 19
  }
}
```

## 模型列表

### GET /v1/models

获取可用模型列表。

**响应:**

```json
{
  "object": "list",
  "data": [
    {
      "id": "gpt-4o",
      "object": "model",
      "created": 1234567890,
      "owned_by": "openai"
    }
  ]
}
```
