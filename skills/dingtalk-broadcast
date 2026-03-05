---
name: dingtalk-broadcast
description: 钉钉群运营消息批量推送。用于推送功能更新、社区公告、运营内容到钉钉群。触发场景：发送钉钉群消息、钉钉机器人推送、群发运营通知、批量推送钉钉消息。
---

# 钉钉运营群发

批量发送群运营消息到钉钉群，通过自动化推送消息，快速将功能更新、社区信息同步给用户。

## 消息类型

支持 4 种消息类型：

| 类型 | 适用场景 | 格式 |
|------|----------|------|
| text | 基本通知消息 | 纯文本 |
| markdown | 带格式的长文本 | Markdown 语法 |
| link | 链接消息 | 标题+描述+链接+封面图 |
| actionCard | 按钮卡片消息 | 标题+描述+按钮列表 |

## 使用流程

### 1. 信息推断

从用户输入推断以下信息，若信息不完整则提醒用户补充：

- **消息类型**：text / markdown / link / actionCard
- **群机器人 access_token**：钉钉群机器人的 webhook access_token
- **推送内容**：消息正文或图文信息

**注意**：一个群机器人 access_token 只推送一次，避免用户干扰。

### 2. 执行推送

使用 curl 发送 POST 请求到钉钉 webhook：

```bash
curl -X POST 'https://oapi.dingtalk.com/robot/send?access_token={access_token}' \
  -H 'Content-Type: application/json' \
  -d '{消息JSON内容}'
```

**示例 - 发送文本消息**：

```bash
curl -X POST 'https://oapi.dingtalk.com/robot/send?access_token=YOUR_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"msgtype":"text","text":{"content":"测试消息"}}'
```

### 3. 链接消息特殊处理

若用户提供文章 URL，需要：
1. 使用 `web_fetch` 工具抓取文章内容
2. 提取 `title`、`description`、`picUrl`
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

文本消息支持 @所有人，在 `at` 中添加 `isAtAll: true`：

```json
{
  "msgtype": "text",
  "text": {
    "content": "这是一条@所有人的测试消息"
  },
  "at": {
    "isAtAll": true
  }
}
```

### Markdown 消息

```json
{
  "msgtype": "markdown",
  "markdown": {
    "title": "标题",
    "text": "## 标题\n正文内容，支持**加粗**、`代码`等"
  }
}
```

### Link 消息

```json
{
  "msgtype": "link",
  "link": {
    "title": "标题",
    "text": "描述",
    "messageUrl": "https://example.com/article",
    "picUrl": "https://example.com/cover.jpg"
  }
}
```

### ActionCard 消息

```json
{
  "msgtype": "actionCard",
  "actionCard": {
    "title": "标题",
    "text": "描述内容",
    "singleTitle": "查看详情",
    "singleURL": "https://example.com"
  }
}
```

或多个按钮：

```json
{
  "msgtype": "actionCard",
  "actionCard": {
    "title": "标题",
    "text": "描述内容",
    "btnOrientation": "0",
    "btns": [
      {
        "title": "按钮1",
        "actionURL": "https://example.com/1"
      },
      {
        "title": "按钮2",
        "actionURL": "https://example.com/2"
      }
    ]
  }
}
```

## 推送示例

```bash
curl 'https://oapi.dingtalk.com/robot/send?access_token=YOUR_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"msgtype":"text","text":{"content":"测试消息"}}'
```

## 输出格式

推送完成后，生成简洁的 Markdown 报告：

```markdown
## 推送报告

| 群机器人 | 消息类型 | 状态 |
|----------|----------|------|
| token-xxx | text | ✅ 成功 |

**推送内容摘要**：{消息内容前 50 字}
```

## 注意事项

1. 确保请求 body 的 JSON 格式正确
2. 批量推送时去重 access_token
3. 钉钉机器人需要安全设置（签名、关键词或 IP 地址），请确保机器人已正确配置
4. Markdown 消息的 `title` 字段会显示在通知栏，`text` 字段为消息正文
5. 当在沙箱中执行 curl 遇到网络请求问题时，尝试用命令行的 bash 执行
