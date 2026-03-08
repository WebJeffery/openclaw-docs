# 频道与节点检查

> 命令实操

channel list、node status、配对验证等常用诊断命令。

---

## 一、通道状态

```bash
openclaw channels status
```

查看各通道连接状态。健康基准：显示已连接/就绪。

带探测（验证实际连通性）：

```bash
openclaw channels status --probe
```

---

## 二、配对列表

首次使用或私信无回复时，检查是否有待审批配对：

```bash
openclaw pairing list telegram
openclaw pairing list discord
openclaw pairing list slack
openclaw pairing list whatsapp
# ... 其他通道同理
```

批准配对：

```bash
openclaw pairing approve <channel> <CODE>
```

---

## 三、通道登录

通道断连或 Token 过期时：

```bash
openclaw channels login --channel <channel-name>
```

按提示完成 OAuth 或 Token 配置。

---

## 四、节点状态

```bash
openclaw nodes status
```

查看已连接的 Node 及其能力、审批状态。

---

## 五、审批请求

节点工具调用需审批时：

```bash
openclaw approvals get --node <id>
```

在 Dashboard 或 CLI 中批准/拒绝。

---

## 六、安全审计

检查白名单、黑名单、配对策略：

```bash
openclaw security audit
```

升级后若白名单阻止自己，可运行 `openclaw doctor --fix` 或将 `@username` 替换为数字发送者 ID。

---

## 七、通道专属故障特征

| 通道 | 常见症状 | 最快检查 |
|------|----------|----------|
| Telegram | 群组静默、私信无回复 | `openclaw pairing list telegram` |
| Discord | 公会无回复 | `openclaw channels status --probe` |
| Slack | Socket 已连无响应 | 验证 app token + bot token |
| WhatsApp | 群组消息被忽略 | `requireMention` + 提及模式 |
| iMessage | 无法接收 | macOS TCC 权限、BlueBubbles 状态 |

详见 [通道故障排查](/tutorials/channels/troubleshooting)。

---

## 延伸阅读

- [消息不回](/openclaw-knowledge/module-04-troubleshooting/02-ops/01-no-reply)
- [通道故障排查](/tutorials/channels/troubleshooting)
- [配对管理](/tutorials/gateway/pairing)
