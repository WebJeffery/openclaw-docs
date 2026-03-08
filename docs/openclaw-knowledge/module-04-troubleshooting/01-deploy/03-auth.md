# 认证坑

> unauthorized / 429 错误

API Key 无效、限流、OAuth 过期等认证相关问题的排查与处理。

---

## 一、401 Unauthorized

### 现象

日志中出现 `401 Unauthorized`，Agent 不回复或报认证失败。

### 原因

- API Key 错误、过期或已撤销
- 环境变量未生效（未 export 或重启后丢失）
- 配置文件中的 Key 被覆盖或写错

### 排查

```bash
openclaw config show
openclaw models status
```

检查 `ANTHROPIC_API_KEY`、`OPENAI_API_KEY` 等是否配置，以及 `models status` 是否显示已认证。

### 处理

1. 到对应 Provider 控制台重新创建 API Key
2. 更新环境变量或配置：`openclaw configure --section model`
3. 重启 Gateway：`openclaw gateway restart`

---

## 二、429 Too Many Requests（限流）

### 现象

请求被拒绝，返回 429，或日志提示 rate limit。

### 原因

- 请求频率超过 Provider 限制
- 单 Key 并发过高

### 处理

1. **降频**：减少 Cron 频率、合并请求
2. **Fallback**：配置 `model.fallbacks`，主模型限流时自动切换
3. **多 Key 轮换**：在 `auth.profiles` 中配置多个 Key，自动轮换
4. **升级套餐**：部分 Provider 支持提高限额

---

## 三、Gateway 认证（Token/Password）

### 现象

Dashboard、Webhook、远程访问时报 `unauthorized`。

### 排查

```bash
openclaw config get gateway.auth.mode
openclaw config get gateway.auth.token
```

### 处理

- 确认 `gateway.auth.token` 或 `gateway.auth.password` 已配置
- 2026 变更：若同时配置 token 和 password，**必须**设置 `gateway.auth.mode: "token"` 或 `"password"`
- 请求时携带正确 Header：`Authorization: Bearer <token>`

---

## 四、OAuth Token 过期

### 现象

Gmail、Slack 等通道突然断连，日志提示 token expired。

### 排查

```bash
openclaw auth status
```

### 处理

```bash
openclaw auth refresh --channel gmail
openclaw auth refresh --all
```

若刷新失败，需重新登录：

```bash
openclaw channels login --channel gmail --force
```

建议配置 [OAuth 认证监控](/tutorials/automation/auth-monitoring)，定期检查并刷新。

---

## 五、SecretRef 相关

若使用 SecretRef 管理密钥，需确认：

- 引用的 secret 服务可达
- 密钥路径或 ID 正确
- 权限允许 OpenClaw 读取

---

## 延伸阅读

- [模型故障转移](/tutorials/concepts/model-failover)
- [OAuth 认证监控](/tutorials/automation/auth-monitoring)
- [Gateway 认证](/tutorials/gateway/authentication)
