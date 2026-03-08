# 模型切换技巧

> 降本增效

通过按场景选模型、会话级切换、Cron 覆盖等方式，在保证效果的前提下降低成本。

---

## 一、按场景选模型

| 场景 | 推荐模型 | 理由 |
|------|----------|------|
| 复杂推理、长文写作 | claude-opus, gpt-4o | 质量优先 |
| 日常对话、简单任务 | claude-sonnet, deepseek-chat | 性价比 |
| 快速响应、批量处理 | claude-haiku, gpt-4o-mini | 成本最低 |
| 完全离线、隐私场景 | ollama/qwen2.5:7b | 无 API 费用 |

---

## 二、会话内切换（/model）

在聊天中输入：

```
/model anthropic/claude-3-5-sonnet
/model deepseek/deepseek-chat
/model ollama/qwen2.5:7b
```

当前会话后续请求将使用指定模型，直到重置会话或再次切换。

---

## 三、主模型 + Fallback

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-3-5-sonnet",
        fallbacks: ["deepseek/deepseek-chat", "openai/gpt-4o-mini"]
      }
    }
  }
}
```

主模型 429 限流或超时时，自动切换到 fallbacks，无需人工干预。

---

## 四、按任务类型分配 Agent

```json5
{
  agents: {
    "heavy-agent": {
      model: { primary: "anthropic/claude-opus-4-6" }
    },
    "light-agent": {
      model: { primary: "deepseek/deepseek-chat" }
    }
  }
}
```

复杂任务路由到 heavy-agent，简单任务用 light-agent。

---

## 五、Cron 任务模型覆盖

为定时任务单独指定模型，不占用默认配置：

```json5
{
  cron: {
    jobs: [
      {
        schedule: "0 9 * * 1-5",
        agent: "daily-summary",
        message: "生成今日简报",
        model: "anthropic/claude-3-5-sonnet"  // 此任务用强模型
      },
      {
        schedule: "0 */2 * * *",
        agent: "status-check",
        message: "检查系统状态",
        model: "deepseek/deepseek-chat"  // 此任务用便宜模型
      }
    ]
  }
}
```

---

## 六、技能级模型覆盖

在 Skill 的 frontmatter 中指定：

```yaml
---
config:
  model: "anthropic/claude-opus-4-6"
---

# 技能正文...
```

该技能激活时，临时使用指定模型。

---

## 七、成本估算参考

| 模型 | 输入（/M tokens） | 输出（/M tokens） | 适用 |
|------|-------------------|-------------------|------|
| Claude Haiku | ~$0.25 | ~$1.25 | 高频、简单 |
| Claude Sonnet | ~$3 | ~$15 | 日常主力 |
| Claude Opus | ~$15 | ~$75 | 复杂任务 |
| DeepSeek | ~$0.14 | ~$0.28 | 高性价比 |
| GPT-4o-mini | ~$0.15 | ~$0.6 | 轻量 |
| Ollama | 免费 | 免费 | 本地 |

---

## 延伸阅读

- [模型故障转移](/tutorials/concepts/model-failover)
- [主流大模型对接](/openclaw-knowledge/module-03-advanced/02-models/01-providers)
- [API 调用优化](/openclaw-knowledge/module-03-advanced/02-models/04-api)
