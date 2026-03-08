# 跨工具协同

> WPS / 企业微信 / 飞书集成

OpenClaw 本身不直接对接 WPS、企业微信，但可通过 Webhook、API 桥接、通道接入等方式实现跨工具协同。

---

## 一、飞书集成

### 原生支持

OpenClaw 支持飞书通道，可直接在飞书群组、私聊中与 Agent 对话。

- 配置：`openclaw channels login --channel feishu`
- 详见 [飞书接入教程](/tutorials/channels/feishu)

### 典型应用

- 在飞书群 @ Bot 生成会议纪要
- 在飞书群接收 Cron 定时报告
- 通过飞书审批流程触发 Agent 任务（需 Webhook 桥接）

---

## 二、企业微信集成

### 方式一：企业微信机器人 Webhook

企业微信群机器人可配置 Webhook URL。可搭建桥接服务：

1. OpenClaw 的 Webhook 接收外部触发
2. 桥接服务将企业微信的 @ 消息转发给 OpenClaw
3. Agent 回复后，桥接服务通过企业微信 Webhook 发回群内

### 方式二：企业微信应用消息

若企业微信有应用开发能力，可：

1. 应用接收用户消息
2. 调用 OpenClaw Gateway 的 RPC 或 HTTP 接口
3. 将 Agent 回复通过应用消息发送给用户

---

## 三、WPS 集成

WPS 无官方 OpenClaw 插件，但可通过以下方式协同：

### 文件桥接

1. 用户在 WPS 中编辑文档后保存到共享目录
2. Cron 或 Webhook 检测到新文件，触发 Agent
3. Agent 读取文件并处理（如生成摘要、提取要点）
4. 将结果写入另一文件或推送到飞书/Telegram

### 脚本调用

通过 WPS 的宏或脚本，在保存时调用本地 HTTP 接口，触发 OpenClaw Webhook 执行后续处理。

---

## 四、通用 Webhook 桥接架构

```
外部系统（企业微信/WPS/钉钉）
        ↓
  桥接服务（自建）
        ↓
  OpenClaw Webhook
        ↓
  Agent 执行
        ↓
  桥接服务将结果发回外部系统
```

桥接服务可用 Node.js、Python 等实现，核心逻辑：

- 接收外部请求，转换为 OpenClaw 的 Webhook 格式
- 调用 `POST /hooks/agent` 触发 Agent
- 轮询或 WebSocket 获取 Agent 回复
- 将回复格式化为外部系统所需格式并发送

---

## 五、配置示例

### Webhook 启用

```json5
{
  webhooks: {
    enabled: true,
    secret: "your-secret-token",
    agents: {
      "bridge-agent": {
        sessionKey: "bridge-session"
      }
    }
  }
}
```

### 桥接调用

```bash
curl -X POST https://your-gateway/hooks/agent \
  -H "Authorization: Bearer your-secret-token" \
  -H "Content-Type: application/json" \
  -d '{"agent": "bridge-agent", "message": "处理来自企业微信的请求：..."}'
```

---

## 延伸阅读

- [Webhook 外部触发](/tutorials/automation/webhook)
- [飞书接入](/tutorials/channels/feishu)
- [通道故障排查](/tutorials/channels/troubleshooting)
