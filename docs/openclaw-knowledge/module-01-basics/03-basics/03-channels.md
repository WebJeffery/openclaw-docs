# 通讯渠道配对

> 飞书 / 微信 / Telegram 等关联

将聊天软件（通道）接入 OpenClaw 后，才能在这些平台里与 AI 对话。不同通道的配置方式略有差异。

---

## 一、通用流程概览

1. **获取通道凭证**（Bot Token、App ID 等）
2. **执行登录命令**：`openclaw channels login --channel <通道名>`
3. **填入凭证**
4. **重启 Gateway**：`openclaw gateway restart`
5. **首次对话时完成配对**：`openclaw pairing approve`

---

## 二、Telegram（推荐新手）

### 获取 Token

1. 在 Telegram 搜索 `@BotFather`
2. 发送 `/newbot`
3. 按提示设置名称和用户名（须以 `bot` 结尾）
4. 复制返回的 Token（形如 `7123456789:AAEBcd...`）

### 配置

```bash
openclaw channels login --channel telegram
# 按提示粘贴 Token

openclaw gateway restart
```

### 首次使用

1. 在 Telegram 搜索你的 Bot，发送任意消息
2. 会收到配对码，在终端执行：

```bash
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

3. 再发一条消息，即可收到 AI 回复

---

## 三、Discord

### 获取 Token

1. 打开 [Discord Developer Portal](https://discord.com/developers/applications)
2. 创建 Application → Bot → Reset Token 并复制
3. 开启 Message Content Intent 等所需权限
4. OAuth2 生成邀请链接，将 Bot 加入服务器

### 配置

```bash
openclaw channels login --channel discord
openclaw gateway restart
```

---

## 四、飞书（Feishu）

### 获取凭证

1. 登录 [飞书开放平台](https://open.feishu.cn/)
2. 创建企业自建应用
3. 获取 App ID、App Secret
4. 配置权限（发消息、接收消息等）
5. 发布版本并启用

### 配置

参考 [飞书接入教程](/tutorials/channels/feishu)，在配置中填入 `appId`、`appSecret` 等。

---

## 五、其他通道

| 通道 | 登录命令 | 说明 |
|------|----------|------|
| Slack | `openclaw channels login --channel slack` | 需 Bot Token 或 App 凭证 |
| WhatsApp | 见 [WhatsApp 教程](/tutorials/channels/) | 需 Meta 开发者配置 |
| Signal | `openclaw channels login --channel signal` | 需配对设备 |
| iMessage | 见对应文档 | 仅 macOS |

---

## 六、配对管理

### 查看待审批

```bash
openclaw pairing list telegram
openclaw pairing list discord
```

### 批准 / 拒绝

```bash
openclaw pairing approve telegram <CODE>
openclaw pairing reject telegram <CODE>
```

### 黑名单

若需禁止某用户，可在配置中设置 `blocklist`，或通过 Dashboard 管理。

---

## 七、通道状态检查

```bash
openclaw channels status
openclaw channels status --probe   # 带探针检测
```

若有报错，参考 [通道故障排查](/tutorials/channels/troubleshooting)。

---

## 延伸阅读

- [接入 Telegram](/tutorials/channels/telegram)
- [通道故障排查](/tutorials/channels/troubleshooting)
- [消息不回](/openclaw-knowledge/module-04-troubleshooting/02-ops/01-no-reply)
