# DogAPI 用户 API 参考文档

本文档为 DogAPI 平台面向用户的 API 接口参考。所有接口均以 `/api` 为前缀（管理接口和 Relay 接口除外）。

---

## 目录

- [1. 认证与用户管理](#1-认证与用户管理)
- [2. 令牌(Token)管理](#2-令牌token管理)
- [3. 充值与支付](#3-充值与支付)
- [4. 订阅套餐](#4-订阅套餐)
- [5. 计费信息](#5-计费信息)
- [6. 模型列表](#6-模型列表)
- [7. 签到](#7-签到)
- [8. 日志查询](#8-日志查询)
- [9. 使用量统计](#9-使用量统计)
- [10. 用户设置](#10-用户设置)
- [11. 两步验证(2FA)](#11-两步验证2fa)
- [12. 推广与邀请](#12-推广与邀请)
- [13. 分组信息](#13-分组信息)
- [14. 公共信息](#14-公共信息)
- [15. Relay API (OpenAI 兼容)](#15-relay-api-openai-兼容)
- [16. Playground](#16-playground)
- [17. 兑换码(管理员)](#17-兑换码管理员)
- [18. 通用响应格式](#18-通用响应格式)
- [19. 错误码参考](#19-错误码参考)

---

## 1. 认证与用户管理

### 1.1 用户注册

创建新用户账号。

```
POST /api/user/register
```

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `username` | string | 是 | 用户名 |
| `password` | string | 是 | 密码 |
| `email` | string | 条件 | 开启邮箱验证时必填 |
| `verification_code` | string | 条件 | 开启邮箱验证时必填 |
| `aff_code` | string | 否 | 邀请码 |

**响应示例：**

```json
{
  "success": true,
  "message": ""
}
```

**注意事项：**
- 需要管理员开启注册功能
- 如果系统启用了邮箱验证，需要先调用邮箱验证接口获取验证码
- 注册成功后可能会自动生成默认令牌（取决于系统配置）

---

### 1.2 用户登录

使用用户名和密码登录。

```
POST /api/user/login
```

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `username` | string | 是 | 用户名 |
| `password` | string | 是 | 密码 |

**响应示例（正常登录）：**

```json
{
  "message": "",
  "success": true,
  "data": {
    "id": 1,
    "username": "testuser",
    "display_name": "testuser",
    "role": 100,
    "status": 1,
    "group": "default"
  }
}
```

**响应示例（需要2FA验证）：**

```json
{
  "message": "需要两步验证",
  "success": true,
  "data": {
    "require_2fa": true
  }
}
```

当返回 `require_2fa: true` 时，需要调用 2FA 验证接口完成登录。

---

### 1.3 2FA 登录验证

当登录接口返回需要2FA时，使用此接口完成登录。

```
POST /api/user/login/2fa
```

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `code` | string | 是 | TOTP验证码或备用码 |

**响应示例：**

```json
{
  "message": "",
  "success": true,
  "data": {
    "id": 1,
    "username": "testuser",
    "display_name": "testuser",
    "role": 100,
    "status": 1,
    "group": "default"
  }
}
```

---

### 1.4 用户登出

```
GET /api/user/logout
```

**认证要求：** 无需认证

**响应示例：**

```json
{
  "message": "",
  "success": true
}
```

---

### 1.5 获取当前用户信息

获取当前登录用户的详细信息。

```
GET /api/user/self
```

**认证要求：** 需要用户登录（Session 或 Token）

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "id": 1,
    "username": "testuser",
    "display_name": "testuser",
    "role": 100,
    "status": 1,
    "email": "user@example.com",
    "github_id": "",
    "discord_id": "",
    "wechat_id": "",
    "telegram_id": "",
    "group": "default",
    "quota": 1000000,
    "used_quota": 50000,
    "request_count": 120,
    "aff_code": "ABCD",
    "aff_count": 3,
    "aff_quota": 5000,
    "aff_history_quota": 10000,
    "inviter_id": 0,
    "setting": {},
    "permissions": {
      "sidebar_settings": true,
      "sidebar_modules": {
        "admin": false
      }
    }
  }
}
```

**字段说明：**

| 字段 | 说明 |
|------|------|
| `id` | 用户ID |
| `username` | 用户名 |
| `display_name` | 显示名称 |
| `role` | 角色（100=普通用户, 1000=管理员, 10000=超级管理员） |
| `status` | 状态（1=正常, 2=禁用） |
| `quota` | 总额度 |
| `used_quota` | 已使用额度 |
| `request_count` | 请求次数 |
| `aff_code` | 推广邀请码 |
| `aff_quota` | 可提现推广佣金 |
| `aff_history_quota` | 历史推广佣金总额 |

---

### 1.6 更新当前用户信息

```
PUT /api/user/self
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `display_name` | string | 否 | 新的显示名称 |
| `password` | string | 否 | 新密码 |
| `original_password` | string | 条件 | 修改密码时需要提供原密码 |

**响应示例：**

```json
{
  "success": true,
  "message": ""
}
```

---

### 1.7 删除当前用户账号

```
DELETE /api/user/self
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": ""
}
```

**注意事项：** 超级管理员账号无法自行删除。

---

### 1.8 发送邮箱验证码

发送邮箱验证码用于注册或绑定邮箱。

```
GET /api/verification?email=user@example.com
```

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `email` | string | 是 | 目标邮箱地址 |

**响应示例：**

```json
{
  "success": true,
  "message": ""
}
```

---

### 1.9 发送密码重置邮件

```
GET /api/reset_password?email=user@example.com
```

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `email` | string | 是 | 注册时使用的邮箱 |

**响应示例：**

```json
{
  "success": true,
  "message": ""
}
```

**注意事项：** 无论邮箱是否存在，均返回成功（防止用户枚举）。

---

### 1.10 重置密码

使用邮件中的重置链接重置密码。

```
POST /api/user/reset
```

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `email` | string | 是 | 邮箱地址 |
| `token` | string | 是 | 重置令牌 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": "新密码字符串"
}
```

---

### 1.11 获取用户可访问模型列表

```
GET /api/user/models
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": ["gpt-4o", "gpt-4o-mini", "claude-3-5-sonnet-20241022"]
}
```

---

## 2. 令牌(Token)管理

令牌是调用 Relay API 的凭证。每个用户可以创建多个令牌，令牌可以设置额度、过期时间、模型限制等。

### 2.1 获取所有令牌

```
GET /api/token/
```

**认证要求：** 需要用户登录

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `p` | int | 否 | 页码，默认1 |
| `size` | int | 否 | 每页数量，默认10 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "total": 5,
    "page": 1,
    "size": 10,
    "items": [
      {
        "id": 1,
        "user_id": 1,
        "name": "我的令牌",
        "key": "sk-xxxx****xxxx",
        "created_time": 1700000000,
        "accessed_time": 1700000000,
        "expired_time": -1,
        "remain_quota": 1000000,
        "used_quota": 50000,
        "unlimited_quota": false,
        "model_limits_enabled": false,
        "model_limits": "",
        "group": "default",
        "status": 1
      }
    ]
  }
}
```

---

### 2.2 搜索令牌

```
GET /api/token/search?keyword=关键词&token=sk-xxx
```

**认证要求：** 需要用户登录

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `keyword` | string | 否 | 按名称搜索 |
| `token` | string | 否 | 按令牌key搜索 |
| `p` | int | 否 | 页码 |
| `size` | int | 否 | 每页数量 |

---

### 2.3 获取单个令牌详情

```
GET /api/token/:id
```

**认证要求：** 需要用户登录

**路径参数：**

| 参数 | 类型 | 说明 |
|------|------|------|
| `id` | int | 令牌ID |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "id": 1,
    "user_id": 1,
    "name": "我的令牌",
    "key": "sk-xxxx****xxxx",
    "remain_quota": 1000000,
    "used_quota": 50000,
    "unlimited_quota": false,
    "expired_time": -1,
    "model_limits_enabled": false,
    "model_limits": "",
    "group": "default",
    "status": 1
  }
}
```

---

### 2.4 获取令牌完整Key

获取令牌的完整密钥（用于首次查看或复制）。

```
POST /api/token/:id/key
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "key": "sk-xxxxxxxxxxxxxxxxxxxxxxxx"
  }
}
```

---

### 2.5 批量获取令牌Key

```
POST /api/token/batch/keys
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `ids` | int[] | 是 | 令牌ID数组（最多100个） |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "keys": {
      "1": "sk-xxxxxxxxxxxxxxxx",
      "2": "sk-yyyyyyyyyyyyyyyy"
    }
  }
}
```

---

### 2.6 创建令牌

```
POST /api/token/
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | 令牌名称（最长50字符） |
| `expired_time` | int64 | 否 | 过期时间戳（秒），-1表示永不过期 |
| `remain_quota` | int | 否 | 初始额度 |
| `unlimited_quota` | bool | 否 | 是否无限额度 |
| `model_limits_enabled` | bool | 否 | 是否启用模型限制 |
| `model_limits` | string | 否 | 模型限制列表（JSON字符串） |
| `allow_ips` | string | 否 | IP白名单 |
| `group` | string | 否 | 分组，默认"default" |
| `cross_group_retry` | bool | 否 | 是否跨分组重试 |

**响应示例：**

```json
{
  "success": true,
  "message": ""
}
```

**注意事项：**
- 每个用户有最大令牌数量限制
- 非无限额度时，额度值不能为负数且有上限

---

### 2.7 更新令牌

```
PUT /api/token/
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：** 与创建令牌相同，额外包含 `id` 字段。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | int | 是 | 令牌ID |
| `name` | string | 否 | 令牌名称 |
| `expired_time` | int64 | 否 | 过期时间 |
| `remain_quota` | int | 否 | 剩余额度 |
| `unlimited_quota` | bool | 否 | 是否无限额度 |
| `model_limits_enabled` | bool | 否 | 是否启用模型限制 |
| `model_limits` | string | 否 | 模型限制 |
| `allow_ips` | string | 否 | IP白名单 |
| `group` | string | 否 | 分组 |
| `cross_group_retry` | bool | 否 | 跨分组重试 |
| `status` | int | 否 | 状态（仅 `status_only` 查询参数非空时生效） |

**查询参数：**

| 参数 | 类型 | 说明 |
|------|------|------|
| `status_only` | string | 非空时仅更新状态字段 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": { /* 令牌对象 */ }
}
```

---

### 2.8 删除令牌

```
DELETE /api/token/:id
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": ""
}
```

---

### 2.9 批量删除令牌

```
POST /api/token/batch
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `ids` | int[] | 是 | 要删除的令牌ID数组 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": 3
}
```

`data` 字段返回实际删除的数量。

---

### 2.10 查询令牌使用状态（通过 Bearer Token）

使用令牌自身的 Bearer Token 查询该令牌的使用情况。

```
GET /api/usage/token/
```

**认证要求：** Bearer Token 认证（`Authorization: Bearer sk-xxx`）

**响应示例：**

```json
{
  "code": true,
  "message": "ok",
  "data": {
    "object": "token_usage",
    "name": "我的令牌",
    "total_granted": 1000000,
    "total_used": 50000,
    "total_available": 950000,
    "unlimited_quota": false,
    "model_limits": {},
    "model_limits_enabled": false,
    "expires_at": 0
  }
}
```

---

## 3. 充值与支付

### 3.1 获取充值信息

获取当前系统支持的支付方式和充值配置。

```
GET /api/user/topup/info
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "enable_online_topup": true,
    "enable_stripe_topup": true,
    "enable_creem_topup": false,
    "enable_waffo_topup": false,
    "pay_methods": [
      {
        "name": "支付宝",
        "type": "alipay",
        "color": "rgba(var(--semi-blue-5), 1)",
        "min_topup": "1"
      }
    ],
    "min_topup": 1,
    "stripe_min_topup": 1,
    "amount_options": [1, 5, 10, 20, 50, 100],
    "discount": {}
  }
}
```

---

### 3.2 兑换充值码

使用兑换码为账户充值额度。

```
POST /api/user/topup
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `key` | string | 是 | 兑换码 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": 100000
}
```

`data` 字段返回充值的额度数量。

---

### 3.3 在线支付（易支付）

发起在线支付订单。

```
POST /api/user/pay
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `amount` | int64 | 是 | 充值数量（额度单位） |
| `payment_method` | string | 是 | 支付方式（如 "alipay", "wxpay"） |

**响应示例：**

```json
{
  "message": "success",
  "data": { /* 支付参数 */ },
  "url": "https://pay.example.com/checkout"
}
```

---

### 3.4 查询支付金额

查询指定充值数量对应的实际支付金额。

```
POST /api/user/amount
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `amount` | int64 | 是 | 充值数量 |

**响应示例：**

```json
{
  "message": "success",
  "data": "10.00"
}
```

---

### 3.5 Stripe 支付

发起 Stripe 支付。

```
POST /api/user/stripe/pay
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `amount` | int64 | 是 | 充值数量 |

---

### 3.6 Creem 支付

发起 Creem 支付。

```
POST /api/user/creem/pay
```

**认证要求：** 需要用户登录

---

### 3.7 Waffo 支付

发起 Waffo 全球支付。

```
POST /api/user/waffo/pay
```

**认证要求：** 需要用户登录

---

### 3.8 获取用户充值记录

```
GET /api/user/topup/self
```

**认证要求：** 需要用户登录

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `keyword` | string | 否 | 搜索关键词 |
| `p` | int | 否 | 页码 |
| `size` | int | 否 | 每页数量 |

---

## 4. 订阅套餐

### 4.1 获取可用订阅套餐列表

```
GET /api/subscription/plans
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": [
    {
      "plan": {
        "id": 1,
        "title": "基础套餐",
        "subtitle": "适合个人用户",
        "description": "每月100万额度",
        "price_amount": 9.99,
        "currency": "USD",
        "duration_unit": "month",
        "duration_value": 1,
        "total_amount": 1000000,
        "enabled": true
      }
    }
  ]
}
```

---

### 4.2 获取当前用户订阅信息

```
GET /api/subscription/self
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "billing_preference": "quota",
    "subscriptions": [
      {
        "id": 1,
        "plan_id": 1,
        "plan_title": "基础套餐",
        "start_time": 1700000000,
        "end_time": 1702592000,
        "total_amount": 1000000,
        "used_amount": 50000,
        "status": "active"
      }
    ],
    "all_subscriptions": []
  }
}
```

---

### 4.3 更新订阅计费偏好

```
PUT /api/subscription/self/preference
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `billing_preference` | string | 是 | 计费偏好（"quota" 或 "subscription"） |

---

### 4.4 订阅支付（易支付）

```
POST /api/subscription/epay/pay
```

**认证要求：** 需要用户登录

---

### 4.5 订阅支付（Stripe）

```
POST /api/subscription/stripe/pay
```

**认证要求：** 需要用户登录

---

### 4.6 订阅支付（Creem）

```
POST /api/subscription/creem/pay
```

**认证要求：** 需要用户登录

---

## 5. 计费信息

以下接口兼容 OpenAI 的计费 API 格式。

### 5.1 获取订阅信息（OpenAI 兼容）

```
GET /v1/dashboard/billing/subscription
```

**认证要求：** Bearer Token 认证

**响应示例：**

```json
{
  "object": "billing_subscription",
  "has_payment_method": true,
  "soft_limit_usd": 10.0,
  "hard_limit_usd": 10.0,
  "system_hard_limit_usd": 10.0,
  "access_until": 0
}
```

---

### 5.2 获取使用量（OpenAI 兼容）

```
GET /v1/dashboard/billing/usage
```

**认证要求：** Bearer Token 认证

**响应示例：**

```json
{
  "object": "list",
  "total_usage": 500.0
}
```

---

## 6. 模型列表

### 6.1 获取可用模型列表（OpenAI 兼容）

```
GET /v1/models
```

**认证要求：** Bearer Token 认证

**响应示例：**

```json
{
  "success": true,
  "data": [
    {
      "id": "gpt-4o",
      "object": "model",
      "created": 1626777600,
      "owned_by": "openai",
      "supported_endpoint_types": ["chat", "completions"]
    },
    {
      "id": "claude-3-5-sonnet-20241022",
      "object": "model",
      "created": 1626777600,
      "owned_by": "anthropic"
    }
  ],
  "object": "list"
}
```

**注意事项：**
- 返回的模型列表根据用户的分组和令牌的模型限制动态过滤
- 支持 OpenAI、Anthropic、Gemini 等多种格式的响应

---

### 6.2 获取单个模型信息

```
GET /v1/models/:model
```

**认证要求：** Bearer Token 认证

**响应示例：**

```json
{
  "id": "gpt-4o",
  "object": "model",
  "created": 1626777600,
  "owned_by": "openai"
}
```

---

## 7. 签到

### 7.1 获取签到状态

获取当月签到状态和历史记录。

```
GET /api/user/checkin
```

**认证要求：** 需要用户登录

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `month` | string | 否 | 月份（格式：2024-01），默认当前月 |

**响应示例：**

```json
{
  "success": true,
  "data": {
    "enabled": true,
    "min_quota": 100,
    "max_quota": 1000,
    "stats": {
      "total_days": 15,
      "total_quota": 5000
    }
  }
}
```

---

### 7.2 执行签到

```
POST /api/user/checkin
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "签到成功",
  "data": {
    "quota_awarded": 500,
    "checkin_date": "2024-01-15"
  }
}
```

**注意事项：** 每天只能签到一次，签到可获得随机额度奖励。

---

## 8. 日志查询

### 8.1 获取用户日志

获取当前用户的 API 调用日志。

```
GET /api/log/self
```

**认证要求：** 需要用户登录

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `p` | int | 否 | 页码 |
| `size` | int | 否 | 每页数量 |
| `type` | int | 否 | 日志类型 |
| `start_timestamp` | int64 | 否 | 开始时间戳 |
| `end_timestamp` | int64 | 否 | 结束时间戳 |
| `token_name` | string | 否 | 令牌名称筛选 |
| `model_name` | string | 否 | 模型名称筛选 |
| `group` | string | 否 | 分组筛选 |
| `request_id` | string | 否 | 请求ID筛选 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "total": 100,
    "page": 1,
    "size": 10,
    "items": [
      {
        "id": 1,
        "type": 2,
        "user_id": 1,
        "token_id": 1,
        "token_name": "我的令牌",
        "model_name": "gpt-4o",
        "quota": 100,
        "prompt_tokens": 50,
        "completion_tokens": 50,
        "use_time": 1500,
        "created_at": 1700000000
      }
    ]
  }
}
```

---

### 8.2 获取用户日志统计

```
GET /api/log/self/stat
```

**认证要求：** 需要用户登录

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `type` | int | 否 | 日志类型 |
| `start_timestamp` | int64 | 否 | 开始时间戳 |
| `end_timestamp` | int64 | 否 | 结束时间戳 |
| `token_name` | string | 否 | 令牌名称筛选 |
| `model_name` | string | 否 | 模型名称筛选 |
| `group` | string | 否 | 分组筛选 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "quota": 50000,
    "rpm": 10,
    "tpm": 5000
  }
}
```

**字段说明：**

| 字段 | 说明 |
|------|------|
| `quota` | 总使用额度 |
| `rpm` | 每分钟请求数 |
| `tpm` | 每分钟Token数 |

---

### 8.3 通过令牌查询日志

使用 Bearer Token 查询该令牌的调用日志。

```
GET /api/log/token
```

**认证要求：** Bearer Token 认证（只读）

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": [ /* 日志列表 */ ]
}
```

---

## 9. 使用量统计

### 9.1 获取用户使用量按日期统计

```
GET /api/data/self
```

**认证要求：** 需要用户登录

**查询参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `start_timestamp` | int64 | 否 | 开始时间戳 |
| `end_timestamp` | int64 | 否 | 结束时间戳 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": [
    {
      "date": "2024-01-15",
      "quota": 5000,
      "request_count": 20
    }
  ]
}
```

**注意事项：** 时间跨度不能超过1个月。

---

## 10. 用户设置

### 10.1 更新用户设置

```
PUT /api/user/setting
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `notify_type` | string | 是 | 通知类型：`email`, `webhook`, `bark`, `gotify` |
| `quota_warning_threshold` | float64 | 是 | 额度预警阈值（必须大于0） |
| `webhook_url` | string | 条件 | Webhook 通知地址（notify_type 为 webhook 时必填） |
| `webhook_secret` | string | 否 | Webhook 签名密钥 |
| `notification_email` | string | 否 | 通知邮箱地址 |
| `bark_url` | string | 条件 | Bark 推送地址（notify_type 为 bark 时必填） |
| `gotify_url` | string | 条件 | Gotify 推送地址（notify_type 为 gotify 时必填） |
| `gotify_token` | string | 条件 | Gotify Token（notify_type 为 gotify 时必填） |
| `gotify_priority` | int | 否 | Gotify 优先级（0-10，默认5） |
| `accept_unset_model_ratio_model` | bool | 否 | 是否接受未设置倍率的模型 |
| `record_ip_log` | bool | 否 | 是否记录IP日志 |

**响应示例：**

```json
{
  "success": true,
  "message": "设置已保存"
}
```

---

### 10.2 更新用户侧边栏配置

```
PUT /api/user/self
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

```json
{
  "sidebar_modules": "{\"chat\":{\"enabled\":true},\"console\":{\"enabled\":true}}"
}
```

---

### 10.3 更新语言偏好

```
PUT /api/user/self
```

**请求参数（JSON Body）：**

```json
{
  "language": "zh-CN"
}
```

---

## 11. 两步验证(2FA)

### 11.1 获取2FA状态

```
GET /api/user/2fa/status
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "enabled": true,
    "locked": false,
    "backup_codes_remaining": 8
  }
}
```

---

### 11.2 初始化2FA设置

生成 TOTP 密钥和二维码数据。

```
POST /api/user/2fa/setup
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "2FA设置初始化成功，请使用认证器扫描二维码并输入验证码完成设置",
  "data": {
    "secret": "JBSWY3DPEHPK3PXP",
    "qr_code_data": "otpauth://totp/DogAPI:user@example.com?secret=JBSWY3DPEHPK3PXP&issuer=DogAPI",
    "backup_codes": ["ABCD1234", "EFGH5678", "IJKL9012"]
  }
}
```

**注意事项：**
- 请妥善保管备用码，每个备用码只能使用一次
- 备用码用于在无法使用认证器时恢复访问

---

### 11.3 启用2FA

使用 TOTP 验证码完成2FA启用。

```
POST /api/user/2fa/enable
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `code` | string | 是 | TOTP 验证码（6位数字） |

**响应示例：**

```json
{
  "success": true,
  "message": "两步验证启用成功"
}
```

---

### 11.4 禁用2FA

```
POST /api/user/2fa/disable
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `code` | string | 是 | TOTP 验证码或备用码 |

---

### 11.5 重新生成备用码

```
POST /api/user/2fa/backup_codes
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `code` | string | 是 | TOTP 验证码 |

**响应示例：**

```json
{
  "success": true,
  "message": "备用码重新生成成功",
  "data": {
    "backup_codes": ["NEWCODE1", "NEWCODE2", "NEWCODE3"]
  }
}
```

---

## 12. 推广与邀请

### 12.1 获取推广邀请码

```
GET /api/user/aff
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": "ABCD"
}
```

**注意事项：** 首次调用会自动生成邀请码。

---

### 12.2 提现推广佣金

将推广佣金转入可用额度。

```
POST /api/user/aff_transfer
```

**认证要求：** 需要用户登录

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `quota` | int | 是 | 提现额度数量 |

**响应示例：**

```json
{
  "success": true,
  "message": "转账成功"
}
```

---

## 13. 分组信息

### 13.1 获取用户可用分组

```
GET /api/user/self/groups
```

**认证要求：** 需要用户登录

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "default": {
      "ratio": 1.0,
      "desc": "默认分组"
    },
    "vip": {
      "ratio": 0.8,
      "desc": "VIP分组"
    },
    "auto": {
      "ratio": "自动",
      "desc": "自动选择最优分组"
    }
  }
}
```

**字段说明：**

| 字段 | 说明 |
|------|------|
| `ratio` | 分组倍率（影响计费） |
| `desc` | 分组描述 |

---

### 13.2 获取公开分组列表

```
GET /api/user/groups
```

**认证要求：** 无需认证

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "default": {
      "ratio": 1.0,
      "desc": "默认分组"
    }
  }
}
```

---

## 14. 公共信息

### 14.1 获取系统状态

```
GET /api/status
```

**认证要求：** 无需认证

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": {
    "version": "v1.0.0",
    "start_time": 1700000000,
    "system_name": "DogAPI",
    "logo": "",
    "email_verification": true,
    "github_oauth": true,
    "github_client_id": "xxx",
    "telegram_oauth": false,
    "wechat_login": false,
    "turnstile_check": false,
    "quota_per_unit": 500000,
    "display_in_currency": false,
    "quota_display_type": "tokens",
    "invite_commission_enabled": true,
    "invite_commission_rate": 0.1,
    "checkin_enabled": true,
    "passkey_login": false,
    "setup": true,
    "user_agreement_enabled": true,
    "privacy_policy_enabled": true
  }
}
```

---

### 14.2 获取公告

```
GET /api/notice
```

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": "系统公告内容（支持HTML）"
}
```

---

### 14.3 获取关于页面

```
GET /api/about
```

---

### 14.4 获取用户协议

```
GET /api/user-agreement
```

---

### 14.5 获取隐私政策

```
GET /api/privacy-policy
```

---

### 14.6 获取首页内容

```
GET /api/home_page_content
```

---

### 14.7 获取定价信息

```
GET /api/pricing
```

**认证要求：** 可选认证（TryUserAuth）

---

### 14.8 获取倍率配置

```
GET /api/ratio_config
```

---

## 15. Relay API (OpenAI 兼容)

Relay API 提供与 OpenAI 完全兼容的接口，支持多种 AI 模型提供商。

### 15.1 Chat Completions

```
POST /v1/chat/completions
```

**认证要求：** Bearer Token 认证（`Authorization: Bearer sk-xxx`）

**请求格式：** 完全兼容 OpenAI Chat Completions API

**请求示例：**

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

**响应格式：** 与 OpenAI API 完全一致

---

### 15.2 Completions

```
POST /v1/completions
```

**认证要求：** Bearer Token 认证

---

### 15.3 Responses API

```
POST /v1/responses
```

**认证要求：** Bearer Token 认证

---

### 15.4 Embeddings

```
POST /v1/embeddings
```

**认证要求：** Bearer Token 认证

---

### 15.5 图像生成

```
POST /v1/images/generations
```

**认证要求：** Bearer Token 认证

---

### 15.6 图像编辑

```
POST /v1/images/edits
```

**认证要求：** Bearer Token 认证

---

### 15.7 音频转录

```
POST /v1/audio/transcriptions
```

**认证要求：** Bearer Token 认证

---

### 15.8 音频翻译

```
POST /v1/audio/translations
```

**认证要求：** Bearer Token 认证

---

### 15.9 语音合成

```
POST /v1/audio/speech
```

**认证要求：** Bearer Token 认证

---

### 15.10 Rerank

```
POST /v1/rerank
```

**认证要求：** Bearer Token 认证

---

### 15.11 Claude Messages API

```
POST /v1/messages
```

**认证要求：** Bearer Token 认证

**请求格式：** 兼容 Anthropic Claude Messages API

---

### 15.12 Gemini API

```
POST /v1beta/models/:model:generateContent
POST /v1beta/models/:model:streamGenerateContent
```

**认证要求：** Bearer Token 或 Google API Key

---

### 15.13 Realtime API (WebSocket)

```
GET /v1/realtime
```

**认证要求：** Bearer Token 认证

**协议：** WebSocket，子协议为 `realtime`

---

### 15.14 Midjourney API

```
POST /mj/submit/imagine
POST /mj/submit/action
POST /mj/submit/change
POST /mj/task/:id/fetch
```

**认证要求：** Bearer Token 认证

---

### 15.15 Suno API

```
POST /suno/submit/:action
POST /suno/fetch
GET /suno/fetch/:id
```

**认证要求：** Bearer Token 认证

---

## 16. Playground

Playground 提供交互式测试界面的后端支持。

### 16.1 获取 Playground 模型列表

```
GET /api/playground/models
```

**认证要求：** Token 或用户认证

---

### 16.2 Playground Chat Completions

```
POST /pg/chat/completions
```

**认证要求：** Token 或用户认证

**请求格式：** 与 OpenAI Chat Completions 相同

---

### 16.3 Playground Image Generation

```
POST /pg/images/generations
```

**认证要求：** Token 或用户认证

---

### 16.4 管理 Playground 会话

```
GET    /api/playground/sessions          # 获取会话列表
POST   /api/playground/sessions          # 创建会话
DELETE /api/playground/sessions/:id      # 删除会话
GET    /api/playground/sessions/:id/records  # 获取会话记录
POST   /api/playground/sessions/:id/chat-sync  # 同步聊天
```

**认证要求：** Token 或用户认证

---

## 17. 兑换码（管理员）

以下接口需要管理员权限。

### 17.1 获取所有兑换码

```
GET /api/redemption/
```

**认证要求：** 管理员

---

### 17.2 搜索兑换码

```
GET /api/redemption/search?keyword=关键词
```

---

### 17.3 创建兑换码

```
POST /api/redemption/
```

**请求参数（JSON Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | 名称（1-20字符） |
| `count` | int | 是 | 创建数量（1-100） |
| `quota` | int | 是 | 每个兑换码的额度 |
| `expired_time` | int64 | 否 | 过期时间戳，0表示永不过期 |

**响应示例：**

```json
{
  "success": true,
  "message": "",
  "data": ["uuid-1", "uuid-2", "uuid-3"]
}
```

---

## 18. 通用响应格式

### 成功响应

```json
{
  "success": true,
  "message": "",
  "data": { /* 业务数据 */ }
}
```

### 分页响应

```json
{
  "success": true,
  "message": "",
  "data": {
    "total": 100,
    "page": 1,
    "size": 10,
    "items": [ /* 数据列表 */ ]
  }
}
```

### 错误响应

```json
{
  "success": false,
  "message": "错误描述信息"
}
```

### OpenAI 兼容错误响应

```json
{
  "error": {
    "message": "错误描述",
    "type": "invalid_request_error",
    "param": "model",
    "code": "model_not_found"
  }
}
```

---

## 19. 错误码参考

### HTTP 状态码

| 状态码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 400 | 请求参数错误 |
| 401 | 未认证或认证失败 |
| 403 | 权限不足 |
| 404 | 资源不存在 |
| 413 | 请求体过大 |
| 429 | 请求过于频繁（限流） |
| 500 | 服务器内部错误 |
| 502 | 上游服务错误 |
| 503 | 服务不可用 |

### 业务错误类型

| 错误类型 | 说明 |
|----------|------|
| `invalid_request_error` | 请求参数无效 |
| `authentication_error` | 认证失败 |
| `permission_error` | 权限不足 |
| `not_found_error` | 资源不存在 |
| `rate_limit_error` | 请求频率超限 |
| `upstream_error` | 上游服务错误 |
| `new_api_error` | 平台内部错误 |

### 常见业务错误消息

| 错误消息 | 说明 |
|----------|------|
| `令牌名称不能超过50个字符` | 令牌名称过长 |
| `令牌额度不能为负数` | 额度值无效 |
| `已达到最大令牌数量限制` | 令牌数量达上限 |
| `已过期的令牌无法启用` | 令牌已过期 |
| `已耗尽额度的令牌无法启用` | 令牌额度为0 |
| `充值数量不能小于 X` | 充值金额不足最低限额 |
| `签到功能未启用` | 签到功能关闭 |
| `用户已启用2FA` | 2FA已开启 |
| `验证码或备用码错误` | 2FA验证失败 |

---

## 附录：认证方式说明

### Session 认证

用于 Web 前端，通过 Cookie 维持会话状态。登录后自动设置 Session。

### Bearer Token 认证

用于 API 调用，在请求头中携带：

```
Authorization: Bearer sk-xxxxxxxxxxxxxxxxxxxxxxxx
```

令牌可以设置以下限制：
- **额度限制**：总可用额度
- **过期时间**：令牌有效期
- **模型限制**：允许使用的模型列表
- **IP 白名单**：限制访问来源IP
- **分组**：影响可用模型和计费倍率

### Access Token 认证

用于某些特殊场景的长期访问令牌，通过 `GET /api/user/token` 生成。

---

## 附录：分页参数

所有支持分页的接口都使用以下通用查询参数：

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `p` | int | 1 | 页码 |
| `size` | int | 10 | 每页数量 |

---

## 附录：额度单位说明

系统中的额度（Quota）为内部计量单位，与货币的换算关系由 `quota_per_unit` 配置决定。

- `quota_display_type` 为 `tokens` 时：额度直接表示 Token 数量
- `quota_display_type` 为 `usd` 时：额度除以 `quota_per_unit` 得到美元金额
- `quota_display_type` 为 `cny` 时：额度先转USD再乘以汇率得到人民币金额

默认情况下，`quota_per_unit` 的值可通过 `GET /api/status` 接口获取。
