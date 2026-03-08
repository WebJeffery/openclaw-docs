# API Key 获取教程

> 各平台 Key 申请（付费）

OpenAI、Anthropic、通义、DeepSeek 等主流模型提供商的 API Key 申请流程。

---

## 一、申请流程概览

| 提供商 | 控制台 | 申请步骤概要 |
|--------|--------|--------------|
| **Anthropic (Claude)** | [console.anthropic.com](https://console.anthropic.com) | 注册 → API Keys → Create Key |
| **OpenAI (GPT)** | [platform.openai.com](https://platform.openai.com) | 注册 → API Keys → Create New |
| **DeepSeek** | [platform.deepseek.com](https://platform.deepseek.com) | 注册 → API Keys → 创建 |
| **通义千问 (Qwen)** | [dashscope.aliyun.com](https://dashscope.aliyun.com) | 阿里云账号 → 开通 DashScope → 创建 Key |
| **Moonshot (Kimi)** | [platform.moonshot.cn](https://platform.moonshot.cn) | 注册 → API 管理 → 创建 |
| **OpenRouter** | [openrouter.ai](https://openrouter.ai) | 注册 → Keys → Create Key |

---

## 二、Anthropic (Claude)

1. 访问 [console.anthropic.com](https://console.anthropic.com)
2. 注册/登录
3. 进入 **API Keys** → **Create Key**
4. 复制密钥（以 `sk-ant-` 开头）
5. 在 OpenClaw 中：`openclaw configure` 或配置 `env.ANTHROPIC_API_KEY`

::: tip
新用户有免费额度，按量计费后费用通常为每次对话几分到几毛人民币。
:::

---

## 三、OpenAI (GPT)

1. 访问 [platform.openai.com](https://platform.openai.com)
2. 注册/登录
3. 进入 **API Keys** → **Create new secret key**
4. 复制密钥（以 `sk-` 开头）
5. 在 OpenClaw 中：`openclaw configure` 或配置 `env.OPENAI_API_KEY`

::: warning
密钥仅显示一次，请妥善保存。若泄露需立即撤销并重新创建。
:::

---

## 四、DeepSeek

1. 访问 [platform.deepseek.com](https://platform.deepseek.com)
2. 注册/登录
3. 进入 **API Keys** → 创建密钥
4. 在 OpenClaw 中配置 `env.DEEPSEEK_API_KEY` 或对应 provider

---

## 五、通义千问 (Qwen)

1. 访问 [dashscope.aliyun.com](https://dashscope.aliyun.com)
2. 使用阿里云账号登录
3. 开通 DashScope 服务
4. 进入 **API-KEY 管理** → 创建新 Key
5. 在 OpenClaw 中配置通义 provider 的 `apiKey`

---

## 六、Ollama（本地，无需 Key）

- Ollama 本地运行，无需 API Key
- 在 OpenClaw 中设置 `OLLAMA_API_KEY="ollama-local"`（任意值即可）以启用
- 详见 [Ollama 配置](/tutorials/providers/ollama)

---

## 七、安全建议

- **勿硬编码**：不要将 Key 写在配置文件中提交到 Git
- **用环境变量**：通过 `.env` 或 `env` 块 + `${VAR}` 引用
- **定期轮换**：发现泄露立即撤销并重新创建
- **权限最小化**：若支持，仅授予必要权限

详见 [环境变量](/tutorials/help/environment)。

---

## 延伸阅读

- [模型提供商选择](/tutorials/providers/)
- [配置参考](/tutorials/gateway/configuration-reference)
- [认证坑](/openclaw-knowledge/module-04-troubleshooting/01-deploy/03-auth)
