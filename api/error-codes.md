# DogAPI 错误码与状态码参考手册

本文档整理自 DogAPI 项目的 `types/error.go`、`dto/error.go`、`service/error.go`、`constant/finish_reason.go`、`constant/midjourney.go` 及相关源文件。

---

## 1. 错误类型（ErrorType）

错误类型定义了错误的来源分类，用于标识错误属于哪个子系统。

| 常量值 | 说明 | 使用场景 |
|--------|------|----------|
| `new_api_error` | DogAPI 内部错误 | 系统内部处理过程中产生的错误，如 Token 计费失败、渠道获取失败等 |
| `openai_error` | OpenAI 兼容格式错误 | 上游返回 OpenAI 格式的错误响应时使用 |
| `claude_error` | Claude/Anthropic 格式错误 | 上游返回 Anthropic Claude 格式的错误响应时使用 |
| `midjourney_error` | Midjourney 错误 | Midjourney 代理相关任务的错误 |
| `gemini_error` | Gemini 格式错误 | Google Gemini API 返回的错误响应 |
| `rerank_error` | Rerank 重排序错误 | Jina Rerank 等重排序接口的错误 |
| `upstream_error` | 上游通用错误 | 无法识别具体格式时的上游通用错误 |

---

## 2. 错误码（ErrorCode）

### 2.1 通用请求错误

| 错误码 | 说明 | 常见原因 | 解决方案 |
|--------|------|----------|----------|
| `invalid_request` | 无效请求 | 请求参数格式不正确或缺少必要参数 | 检查请求体格式，确保必填字段完整 |
| `sensitive_words_detected` | 检测到敏感词 | 用户输入或生成内容包含敏感词 | 修改提示词，避免敏感内容 |
| `violation_fee.grok.csam` | Grok CSAM 违规计费 | Grok 模型检测到违规内容并计费 | 检查输入内容合规性 |
| `bad_request_body` | 请求体格式错误 | JSON 解析失败或字段类型不匹配 | 验证 JSON 格式和字段类型 |

### 2.2 内部处理错误（new_api_error）

| 错误码 | 说明 | 常见原因 | 解决方案 |
|--------|------|----------|----------|
| `count_token_failed` | Token 计数失败 | 无法正确计算请求的 Token 数量 | 检查模型名称是否正确，确认 Token 计数器配置 |
| `model_price_error` | 模型定价错误 | 请求的模型没有配置价格 | 在管理后台为该模型设置价格 |
| `invalid_api_type` | 无效的 API 类型 | 渠道类型映射到不支持的 API 类型 | 检查渠道配置的类型是否正确 |
| `json_marshal_failed` | JSON 序列化失败 | 内部数据结构序列化为 JSON 时出错 | 检查请求参数是否有不支持的数据类型 |
| `do_request_failed` | 发送请求失败 | 无法连接上游 API 服务 | 检查网络连通性、上游地址是否正确 |
| `get_channel_failed` | 获取渠道失败 | 没有可用的渠道处理该请求 | 检查是否有启用且余额充足的渠道 |
| `gen_relay_info_failed` | 生成中继信息失败 | 内部构建中继请求信息时出错 | 检查渠道配置完整性 |

### 2.3 渠道相关错误（channel:*）

| 错误码 | 说明 | 常见原因 | 解决方案 |
|--------|------|----------|----------|
| `channel:no_available_key` | 无可用密钥 | 渠道内所有 API Key 均不可用 | 检查渠道密钥状态，补充或更换密钥 |
| `channel:param_override_invalid` | 参数覆盖无效 | 渠道的参数覆盖配置格式错误 | 检查渠道高级设置中的参数覆盖 JSON |
| `channel:header_override_invalid` | 请求头覆盖无效 | 渠道的请求头覆盖配置格式错误 | 检查渠道高级设置中的请求头覆盖 JSON |
| `channel:model_mapped_error` | 模型映射错误 | 渠道的模型映射配置有误 | 检查模型映射表，确保目标模型名称正确 |
| `channel:aws_client_error` | AWS 客户端错误 | AWS Bedrock 客户端初始化或调用失败 | 检查 AWS 凭证和区域配置 |
| `channel:invalid_key` | 无效密钥 | API Key 格式错误或已失效 | 更换有效的 API Key |
| `channel:response_time_exceeded` | 响应超时 | 上游 API 响应时间超过设定阈值 | 增加超时时间或更换更快的上游 |

### 2.4 客户端请求错误

| 错误码 | 说明 | 常见原因 | 解决方案 |
|--------|------|----------|----------|
| `read_request_body_failed` | 读取请求体失败 | 请求体过大或连接中断 | 检查请求体大小限制 |
| `convert_request_failed` | 转换请求失败 | 请求格式转换为上游格式时出错 | 检查请求参数与目标 API 的兼容性 |
| `access_denied` | 访问被拒绝 | Token 无效、额度不足或权限不足 | 检查 Token 状态和余额 |

### 2.5 响应处理错误

| 错误码 | 说明 | 常见原因 | 解决方案 |
|--------|------|----------|----------|
| `read_response_body_failed` | 读取响应体失败 | 上游响应体读取时连接中断 | 检查网络稳定性，适当增加超时 |
| `bad_response_status_code` | 异常响应状态码 | 上游返回非 2xx 状态码 | 查看上游具体错误信息，检查 API 版本 |
| `bad_response` | 响应格式异常 | 上游返回的数据格式不符合预期 | 确认上游 API 版本是否匹配 |
| `bad_response_body` | 响应体异常 | 响应体内容无法解析 | 检查上游 API 是否正常 |
| `empty_response` | 空响应 | 上游返回空响应体 | 检查上游服务状态 |
| `aws_invoke_error` | AWS 调用错误 | AWS Bedrock 模型调用失败 | 检查模型 ARN、权限和区域设置 |
| `model_not_found` | 模型未找到 | 请求的模型名称在上游不存在 | 检查模型名称拼写，确认上游是否支持 |
| `prompt_blocked` | 提示词被阻止 | 上游安全策略阻止了提示词 | 修改提示词内容，避免触发安全过滤 |

### 2.6 数据库错误

| 错误码 | 说明 | 常见原因 | 解决方案 |
|--------|------|----------|----------|
| `query_data_error` | 查询数据失败 | 数据库查询操作出错 | 检查数据库连接状态 |
| `update_data_error` | 更新数据失败 | 数据库更新操作出错 | 检查数据是否存在及数据库连接 |

### 2.7 配额错误

| 错误码 | 说明 | 常见原因 | 解决方案 |
|--------|------|----------|----------|
| `insufficient_user_quota` | 用户额度不足 | 用户账户余额不足以完成请求 | 充值或联系管理员增加额度 |
| `pre_consume_token_quota_failed` | Token 预扣费失败 | Token 的预消费额度不足 | 检查 Token 额度设置，增加预消费额度 |

---

## 3. OpenAI 标准错误结构

DogAPI 兼容 OpenAI 的错误响应格式：

```json
{
  "error": {
    "message": "错误描述信息",
    "type": "错误类型",
    "param": "相关参数",
    "code": "错误代码"
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `message` | string | 人类可读的错误描述 |
| `type` | string | 错误类型（对应 ErrorCode 或 ErrorType） |
| `param` | string | 导致错误的请求参数（如有） |
| `code` | any | 错误代码，可以是字符串或数字 |
| `metadata` | object | 可选的元数据（OpenRouter 等渠道会返回额外信息） |

---

## 4. Claude/Anthropic 错误结构

```json
{
  "type": "错误类型",
  "message": "错误描述信息"
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `type` | string | 错误类型标识 |
| `message` | string | 错误描述信息 |

---

## 5. 通用错误响应结构（GeneralErrorResponse）

DogAPI 能够解析多种上游格式的错误响应，兼容字段包括：

| 字段路径 | 说明 |
|----------|------|
| `error` | OpenAI 格式错误对象或纯文本错误 |
| `message` | 通用错误消息 |
| `msg` | 部分国内 API 使用的错误消息字段 |
| `err` | 简写错误字段 |
| `error_msg` | 另一种错误消息变体 |
| `detail` | 详细错误描述（FastAPI 等框架使用） |
| `header.message` | 嵌套在 header 中的消息 |
| `response.error.message` | 嵌套在 response 中的错误消息 |

系统会按以下优先级提取错误信息：
1. `error` 对象中的 `message`
2. `message` 字段
3. `msg` 字段
4. `err` 字段
5. `error_msg` 字段
6. `detail` 字段
7. `header.message` 字段
8. `response.error.message` 字段

---

## 6. 请求完成原因（FinishReason）

用于标识模型生成响应的结束方式。

| 完成原因 | 说明 | 使用场景 |
|----------|------|----------|
| `stop` | 正常结束 | 模型自然生成完成，遇到停止标记 |
| `tool_calls` | 工具调用 | 模型请求调用外部工具/函数 |
| `length` | 长度限制 | 生成内容达到最大 Token 限制 |
| `function_call` | 函数调用（旧版） | 旧版函数调用格式，已被 `tool_calls` 替代 |
| `content_filter` | 内容过滤 | 生成内容被安全过滤器拦截 |

---

## 7. Midjourney 错误码与任务动作

### 7.1 错误码

| 错误码 | 值 | 说明 |
|--------|-----|------|
| `MjErrorUnknown` | 5 | Midjourney 未知错误 |
| `MjRequestError` | 4 | Midjourney 请求错误 |

### 7.2 任务动作

| 动作 | 值 | 说明 |
|------|-----|------|
| `IMAGINE` | 图像生成 | 根据文本提示生成图像 |
| `DESCRIBE` | 图像描述 | 根据图像生成文本描述 |
| `BLEND` | 图像混合 | 将多张图像混合 |
| `UPSCALE` | 放大 | 放大指定图像 |
| `VARIATION` | 变体 | 生成图像变体 |
| `REROLL` | 重新生成 | 重新生成图像 |
| `INPAINT` | 局部重绘 | 对图像指定区域重绘 |
| `MODAL` | 模态操作 | 模态交互确认 |
| `ZOOM` | 缩放 | 对图像进行缩放 |
| `CUSTOM_ZOOM` | 自定义缩放 | 自定义缩放参数 |
| `SHORTEN` | 缩短提示 | 分析并缩短提示词 |
| `HIGH_VATION` | 高变化 | 高强度变体 |
| `LOW_VARIATION` | 低变化 | 低强度变体 |
| `PAN` | 平移 | 对图像进行平移扩展 |
| `SWAP_FACE` | 换脸 | 交换人脸 |
| `UPLOAD` | 上传 | 上传图像素材 |
| `VIDEO` | 视频 | 生成视频 |
| `EDITS` | 编辑 | 编辑图像 |

### 7.3 模型到动作映射

| 模型名称 | 对应动作 |
|----------|----------|
| `mj_imagine` | IMAGINE |
| `mj_describe` | DESCRIBE |
| `mj_blend` | BLEND |
| `mj_upscale` | UPSCALE |
| `mj_variation` | VARIATION |
| `mj_reroll` | REROLL |
| `mj_modal` | MODAL |
| `mj_inpaint` | INPAINT |
| `mj_zoom` | ZOOM |
| `mj_custom_zoom` | CUSTOM_ZOOM |
| `mj_shorten` | SHORTEN |
| `mj_high_variation` | HIGH_VARIATION |
| `mj_low_variation` | LOW_VARIATION |
| `mj_pan` | PAN |
| `swap_face` | SWAP_FACE |
| `mj_upload` | UPLOAD |
| `mj_video` | VIDEO |
| `mj_edits` | EDITS |

---

## 8. 任务平台与动作

### 8.1 任务平台

| 平台标识 | 说明 |
|----------|------|
| `image` | 图像生成平台 |
| `suno` | Suno 音乐生成平台 |
| `mj` | Midjourney 平台 |

### 8.2 Suno 动作

| 动作 | 说明 |
|------|------|
| `MUSIC` | 生成音乐 |
| `LYRICS` | 生成歌词 |

### 8.3 Suno 模型映射

| 模型名称 | 对应动作 |
|----------|----------|
| `suno_music` | MUSIC（生成音乐） |
| `suno_lyrics` | LYRICS（生成歌词） |

### 8.4 通用任务动作

| 动作 | 说明 |
|------|------|
| `generate` | 通用生成 |
| `textGenerate` | 文本生成 |
| `firstTailGenerate` | 首尾生成 |
| `referenceGenerate` | 参考生成 |
| `remixGenerate` | 混音生成 |

---

## 9. 状态码映射与重置

DogAPI 支持通过 `status_code_mapping` 配置将上游返回的 HTTP 状态码映射为自定义状态码。

**配置格式：** JSON 对象，key 为上游状态码（字符串），value 为目标状态码。

```json
{
  "429": "503",
  "500": "502"
}
```

**使用场景：**
- 将上游的 `429`（请求过多）映射为 `503`（服务不可用），避免客户端重试逻辑误判
- 统一不同上游的错误状态码，便于前端统一处理

**注意事项：**
- 状态码 `200`（成功）不会被映射
- 映射值支持字符串、整数、浮点数和 JSON Number 类型
- 无效的映射值会被静默忽略

---

## 10. 敏感信息脱敏

以下错误码的消息会被自动脱敏处理，不直接暴露给客户端：

- `count_token_failed`：此错误码的消息不会被脱敏（特殊处理）
- 其他所有错误码：错误消息中的敏感信息（如 API Key、内部地址等）会被掩码处理

**脱敏规则：**
- 系统自动检测并替换错误消息中的敏感信息
- 开发模式（`DebugEnabled`）下会打印原始错误用于调试
