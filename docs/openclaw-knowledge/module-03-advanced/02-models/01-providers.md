# 主流大模型对接

> GPT-4o / 通义千问等配置

OpenClaw 支持多种 AI 提供商，可按场景选型并配置主备模型。

---

## 一、主流 Provider 速查

| 提供商 | 代表模型 | 配置方式 | 特点 |
|--------|----------|----------|------|
| **Anthropic** | claude-3-5-sonnet, claude-opus-4-6 | `ANTHROPIC_API_KEY` | 官方推荐，综合能力强 |
| **OpenAI** | gpt-4o, gpt-4o-mini, o1 | `OPENAI_API_KEY` | 生态成熟，插件多 |
| **DeepSeek** | deepseek-chat, deepseek-r1 | `DEEPSEEK_API_KEY` | 性价比高，国内直连 |
| **通义千问** | qwen-max, qwen-plus | 阿里云百炼 API Key | 国内服务，合规 |
| **Google** | gemini-1.5-pro, gemini-2.0 | `GOOGLE_GENERATIVE_AI_API_KEY` | 多模态，免费额度 |
| **Ollama** | 本地模型 | `OLLAMA_BASE_URL` | 完全离线，隐私优先 |

---

## 二、配置方式

### 向导配置

```bash
openclaw onboard
# 选择 provider，粘贴 API Key
```

### 环境变量

```bash
export ANTHROPIC_API_KEY="sk-ant-xxx"
export DEEPSEEK_API_KEY="sk-xxx"
```

### 配置文件

```json5
{
  env: {
    ANTHROPIC_API_KEY: "sk-ant-xxx",
    DEEPSEEK_API_KEY: "sk-xxx"
  },
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-3-5-sonnet",
        fallbacks: ["deepseek/deepseek-chat"]
      }
    }
  }
}
```

::: tip 密钥安全
建议用环境变量或 SecretRef，避免将密钥写入配置文件并提交到 Git。
:::

---

## 三、模型 ID 格式

通用格式：`provider/model-name`

| Provider | 示例 |
|----------|------|
| anthropic | `anthropic/claude-3-5-sonnet` |
| openai | `openai/gpt-4o` |
| deepseek | `deepseek/deepseek-chat` |
| qwen | `qwen/qwen-max` |
| ollama | `ollama/qwen2.5:7b` |
| openrouter | `openrouter/meta-llama/llama-3.1-70b-instruct` |

---

## 四、多 Provider 并存

可同时配置多个 Provider，通过 `primary` + `fallbacks` 实现故障转移：

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-3-5-sonnet",
        fallbacks: [
          "deepseek/deepseek-chat",
          "openai/gpt-4o-mini"
        ]
      }
    }
  }
}
```

主模型失败时自动切换到 fallbacks 中的下一个。

---

## 五、按 Agent 指定模型

```json5
{
  agents: {
    "dev-agent": {
      model: { primary: "anthropic/claude-opus-4-6" }
    },
    "simple-bot": {
      model: { primary: "deepseek/deepseek-chat" }
    }
  }
}
```

---

## 六、验证配置

```bash
openclaw models status
```

输出各 Provider 的认证状态。

---

## 延伸阅读

- [Anthropic 配置](/tutorials/providers/anthropic)
- [DeepSeek 配置](/tutorials/providers/)
- [模型故障转移](/tutorials/concepts/model-failover)
- [模型切换技巧](/openclaw-knowledge/module-03-advanced/02-models/02-switch)
