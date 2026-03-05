---
name: wecom-broadcast
description: 企业微信群运营消息批量推送。用于推送功能更新、社区公告、运营内容到企业微信群。触发场景：发送企业微信群消息、企业微信机器人推送、群发运营通知、批量推送企业微信消息。
---

# 企业微信运营群发

批量发送群运营消息到企业微信群，通过自动化推送消息，快速将功能更新、社区信息同步给用户。

## 消息类型

支持 3 种消息类型：

| 类型 | 适用场景 | 格式 |
|------|----------|------|
| text | 基本通知消息 | 纯文本 |
| markdown | 带格式的长文本 | Markdown 语法 |
| news | 图文消息 | 标题+描述+链接+封面图 |

## 使用流程

### 1. 信息推断

从用户输入推断以下信息，若信息不完整则提醒用户补充：

- **消息类型**：text / markdown / news
- **群机器人 key**：企业微信群机器人的 webhook key
- **推送内容**：消息正文或图文信息

**注意**：一个群机器人 key 只推送一次，避免用户干扰。

### 2. 执行推送

使用 curl 发送 POST 请求到企业微信 webhook：

```
https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key={机器人key}
```

### 3. 图文消息特殊处理

若用户提供文章 URL，需要：
1. 使用 `web_fetch` 工具抓取文章内容
2. 提取 `title`、`description`、`picurl`
3. 若提取失败，提醒用户提供完整信息

## 消息格式

### 文本消息

```json
{
  "msgtype": "text",
  "text": {
    "content": "消息内容"
  }
}
```

文本消息支持 @所有人，在 `text` 中添加 `mentioned_list: ["@all"]`：

```json
{
  "msgtype": "text",
  "text": {
    "content": "这是一条@所有人的测试消息",
    "mentioned_list": ["@all"]
  }
}
```

### Markdown 消息

```json
{
  "msgtype": "markdown",
  "markdown": {
    "content": "# 标题\n正文内容，支持**加粗**、`代码`等"
  }
}
```

### 图文消息

```json
{
  "msgtype": "news",
  "news": {
    "articles": [
      {
        "title": "标题",
        "description": "描述",
        "url": "https://example.com/article",
        "picurl": "https://example.com/cover.jpg"
      }
    ]
  }
}
```

## 推送示例

```bash
curl 'https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=YOUR_KEY' \
  -H 'Content-Type: application/json' \
  -d '{"msgtype":"text","text":{"content":"测试消息"}}'
```

## 输出格式

推送完成后，生成简洁的 Markdown 报告：

```markdown
## 推送报告

| 群机器人 | 消息类型 | 状态 |
|----------|----------|------|
| key-xxx | text | ✅ 成功 |

**推送内容摘要**：{消息内容前 50 字}
```

## 注意事项

1. 确保请求 body 的 JSON 格式正确
2. 批量推送时去重机器人 key
3. Markdown 消息不支持复杂语法，参考企业微信官方文档
4. 当在沙箱中执行 curl 遇到网络请求问题时，尝试用命令行的 bash 执行
