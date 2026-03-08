# 密钥保护

> API Key / Token 管理

API Key、Gateway Token 等敏感凭证需妥善管理，避免泄露与滥用。

---

## 一、环境变量

优先使用环境变量，避免写入配置文件：

```bash
export ANTHROPIC_API_KEY="sk-ant-xxx"
export OPENCLAW_GATEWAY_TOKEN="your-gateway-token"
```

或使用 `.env` 文件（不要提交到 Git）。

---

## 二、SecretRef

OpenClaw 支持 SecretRef，从外部密钥管理服务读取：

```json5
{
  gateway: {
    auth: {
      mode: "token",
      token: { $ref: "secret://my-vault/gateway-token" }
    }
  }
}
```

具体格式依赖部署环境与插件支持。

---

## 三、Gateway 鉴权

### Token 模式

```json5
{
  gateway: {
    auth: {
      mode: "token",
      token: "your-secure-token"
    }
  }
}
```

从外网访问时，请求需带 `Authorization: Bearer <token>`。

### 2026 重要变更

当同时配置 `gateway.auth.token` 和 `gateway.auth.password` 时，**必须**显式设置 `gateway.auth.mode` 为 `token` 或 `password`，否则可能启动失败。

---

## 四、密钥轮换

- 定期更换 API Key、Gateway Token
- 更换后更新环境变量或配置，并重启 Gateway
- 若使用多 Key 轮换，可逐个替换，减少中断

---

## 五、访问控制

- 配置文件权限：`chmod 600 ~/.openclaw/openclaw.json`
- 避免在日志、错误信息中输出完整密钥
- 生产环境禁用 `--allow-unconfigured`，强制完整鉴权

---

## 六、OAuth Token

Gmail、Slack 等通道使用 OAuth，Token 会过期。建议配置 [OAuth 认证监控](/tutorials/automation/auth-monitoring)，定期检查并刷新。

---

## 延伸阅读

- [认证与安全](/tutorials/gateway/authentication)
- [OAuth 认证监控](/tutorials/automation/auth-monitoring)
- [环境隔离](/openclaw-knowledge/module-03-advanced/04-security/01-isolation)
