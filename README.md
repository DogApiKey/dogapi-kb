# DogAPI 知识库

DogAPI 客服机器人的知识库文档仓库。

## 📁 目录结构

```
dogapi-kb/
├── api/              # API 使用文档
│   ├── endpoints.md  # API 端点说明
│   ├── auth.md       # 认证方式
│   └── errors.md     # 错误码说明
├── pricing/          # 价格相关
│   └── models.md     # 模型定价表
├── guides/           # 使用教程
│   ├── quickstart.md # 快速开始
│   └── faq.md        # 常见问题
├── changelog/        # 更新日志
└── qq-faq/           # QQ 群整理的 FAQ
```

## 🔄 文档更新流程

1. 在对应目录下添加或修改 Markdown 文件
2. Commit 并 Push 到仓库
3. CSBot 会自动检测变更并更新知识库（或手动触发重新索引）

## 📝 文档编写规范

- 使用 Markdown 格式
- 每个文档聚焦一个主题
- 标题层级清晰（H1 → H2 → H3）
- 代码示例使用代码块
- 避免敏感信息（密钥、内部地址等）

## 🤖 自动生成

部分文档可从源码自动生成：

```bash
# 从 DogAPI 源码生成 API 文档
node scripts/generate-api-docs.js

# 从 QQ 聊天记录提取 FAQ
node scripts/extract-qq-faq.js
```
