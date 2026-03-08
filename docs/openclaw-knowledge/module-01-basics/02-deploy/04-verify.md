# 部署后验证

> 故障排查

部署完成后，按以下步骤验证，可快速发现并修复常见问题。

---

## 一、一键诊断：openclaw doctor

**首选命令**，会自动检查网关、通道、配置等：

```bash
openclaw doctor
```

按输出提示逐项修复即可。

---

## 二、分项检查

### 1. 网关状态

```bash
openclaw gateway status
```

**期望**：`Runtime: running`

**若为 stopped**：

```bash
openclaw gateway start
# 或
openclaw gateway restart
```

**端口占用**：

```bash
openclaw gateway --force    # 强制重启
# 或换端口
openclaw gateway --port 18790
```

---

### 2. 通道连接

```bash
openclaw channels status
```

**可选加探针**：

```bash
openclaw channels status --probe
```

**若某通道报错**：参考 [通道故障排查](/tutorials/channels/troubleshooting) 或对应通道文档（如 [Telegram](/tutorials/channels/telegram)）。

---

### 3. 配对状态（首次使用必查）

首次在 Telegram/飞书 等发消息时，需先审批配对：

```bash
openclaw pairing list telegram
```

若有待审批请求：

```bash
openclaw pairing approve telegram <CODE>
```

---

### 4. 模型与 API

**API Key 无效**：检查环境变量或 `config.json` 中的密钥是否正确、是否过期。

**429 限流**：降低并发或切换备用模型。

**Ollama 连接失败**：

```bash
curl http://127.0.0.1:11434/api/tags   # 检查 Ollama 是否在运行
ollama list                             # 确认模型已拉取
```

---

### 5. 日志排查

```bash
openclaw logs --follow
```

发一条测试消息，观察日志中是否有报错、超时、鉴权失败等。

---

## 三、常见问题速查

| 现象 | 可能原因 | 处理 |
|------|----------|------|
| 消息无回复 | 未配对、Gateway 未运行、通道断开 | `pairing approve`、`gateway status`、`channels status` |
| 启动失败 | 端口占用、配置错误 | `gateway --force`、检查 config |
| 401/403 | API Key 错误或鉴权配置问题 | 检查密钥、`gateway.auth` |
| 429 | API 限流 | 降并发、换模型 |
| Ollama 超时 | 模型未拉取、显存不足 | `ollama pull`、换小模型 |

---

## 四、验证清单

部署后可逐项确认：

- [ ] `openclaw doctor` 无报错
- [ ] `openclaw gateway status` 显示 running
- [ ] `openclaw channels status` 目标通道正常
- [ ] 已执行 `pairing approve`（若为首次）
- [ ] 在对应聊天软件中发消息能收到 AI 回复

---

## 延伸阅读

- [网关故障排查](/tutorials/gateway/troubleshooting)
- [消息不回](/openclaw-knowledge/module-04-troubleshooting/02-ops/01-no-reply)
- [必备诊断命令](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/01-status)
