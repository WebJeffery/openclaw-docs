# 消息不回

> 频道配对 / 黑名单排查

发消息后 Agent 不回复，按以下顺序排查。

---

## 一、快速检查顺序

```bash
openclaw gateway status      # 1. Gateway 是否运行
openclaw channels status     # 2. 通道是否连接
openclaw pairing list <channel>  # 3. 是否有待审批配对
openclaw logs --follow       # 4. 看实时日志
```

---

## 二、未配对（最常见）

### 现象

首次在 Telegram/飞书 等发消息时，Bot 不回复，或提示需要配对。

### 处理

```bash
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

将 `<CODE>` 替换为实际配对码。批准后再发一条消息即可。

---

## 三、通道策略过滤

### 群组消息被忽略

- **提及要求**：部分通道需 @ 提及 Bot 才会响应
- **群组白名单**：群组 ID 可能不在 `groupAllowFrom` 中
- **隐私模式**：Telegram Bot 若开启隐私模式，无法收到未 @ 的群消息

### 处理

- 在群组中 @ Bot 再发消息
- 检查配置中 `requireMention`、`groupAllowFrom`
- Telegram：在 @BotFather 中关闭隐私模式（若需接收所有群消息）

---

## 四、黑名单 / 白名单

### 现象

日志中出现 `blocked`、`allowlist`、`drop guild message` 等。

### 处理

- 检查 `channels.<name>.allowFrom`、`blocklist`
- 若使用 `dmPolicy: "allowlist"`，需将用户 ID 加入 `allowFrom`
- 运行 `openclaw security audit` 检查策略

---

## 五、通道断连

### 现象

`openclaw channels status` 显示某通道 `disconnected` 或 `error`。

### 处理

```bash
openclaw channels login --channel telegram
openclaw gateway restart
```

若为 OAuth 通道，检查 Token 是否过期：`openclaw auth status`，必要时 `openclaw auth refresh`。

---

## 六、API 限流或模型错误

若通道正常、已配对，仍无回复，可能是后端问题：

- 查看日志中的 429、5xx、model error
- 检查 API Key 是否有效
- 配置 `model.fallbacks` 做故障转移

---

## 延伸阅读

- [通道故障排查](/tutorials/channels/troubleshooting)
- [配对管理](/tutorials/gateway/pairing)
- [认证坑](/openclaw-knowledge/module-04-troubleshooting/01-deploy/03-auth)
