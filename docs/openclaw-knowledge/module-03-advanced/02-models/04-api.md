# API 调用优化

> 避限流 + 降成本

合理配置可减少 429 限流、降低 API 费用、提升稳定性。

---

## 一、限流与重试

### 认证配置轮换

OpenClaw 支持同一 Provider 多 API Key 轮换。在 `auth.profiles` 中配置多个 profile，限流时自动切换。

### 模型 Fallback

主模型 429 时，自动切换到 `fallbacks` 中的下一个模型，避免单点故障。

### 重试策略

部分 Provider 支持自动重试。可配置 `retries`、`backoff` 等（具体以各 Provider 文档为准）。

---

## 二、降成本策略

### 1. 按任务选模型

简单任务用便宜模型（Haiku、DeepSeek、mini），复杂任务用强模型。

### 2. 控制上下文长度

- 压缩（Compaction）会裁剪历史，减少 Token
- 避免无意义的长系统提示词
- 技能内容精简，按需启用

### 3. 缓存与复用

- 相同请求可考虑本地缓存结果
- 会话保持可复用上下文，减少重复注入

### 4. 批量与合并

- 多条消息用 `collect` 模式合并为一次请求
- Cron 任务合并相似逻辑，减少调用次数

---

## 三、多 Key 轮换

```json5
{
  auth: {
    profiles: [
      { id: "anthropic-1", provider: "anthropic", type: "api_key", key: "sk-ant-xxx1" },
      { id: "anthropic-2", provider: "anthropic", type: "api_key", key: "sk-ant-xxx2" }
    ],
    order: {
      anthropic: ["anthropic-1", "anthropic-2"]
    }
  }
}
```

按顺序轮换，单 Key 限流时自动切下一个。

---

## 四、监控与告警

### 用量查看

部分 Provider 控制台可查看调用量、费用。建议定期检查。

### 异常告警

若配置了 Cron 或 Webhook，可在 Agent 中检测 429、5xx 等错误，并推送到告警通道。

---

## 五、OpenRouter / 代理

通过 OpenRouter、LiteLLM 等统一入口，可：

- 多 Provider 聚合
- 统一计费与监控
- 自定义路由规则

配置方式见 [OpenRouter 文档](/tutorials/providers/openrouter)。

---

## 延伸阅读

- [模型故障转移](/tutorials/concepts/model-failover)
- [模型切换技巧](/openclaw-knowledge/module-03-advanced/02-models/02-switch)
- [认证配置](/tutorials/concepts/oauth)
