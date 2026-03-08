# 常见认知误区

> 避坑指南

新手容易产生的误解，会直接影响使用体验和预期。提前了解可少走弯路。

---

## 误区一：OpenClaw = 聊天机器人框架

**误解**：以为只是做一个「自动回复 Bot」。

**实际**：OpenClaw 是 **AI 智能体框架**，不仅能聊天，还能：

- 调用工具（搜索、读文件、执行代码）
- 管理会话上下文与记忆
- 执行定时任务、Webhook
- 多智能体协同（sessions_spawn、sessions_send 等）

把它当成「可执行任务的 AI 助手平台」更准确。

---

## 误区二：装好就能用，不需要配置

**误解**：安装完就能在 Telegram 里聊天。

**实际**：至少需要完成：

1. **API 密钥**：配置 Anthropic、OpenAI、DeepSeek 等至少一个 Provider
2. **通道登录**：`openclaw channels login --channel telegram` 等
3. **配对审批**：首次对话需在终端执行 `openclaw pairing approve`
4. **Gateway 常驻**：Gateway 必须持续运行，否则收不到消息

建议按 [快速开始](/tutorials/getting-started/getting-started) 和 [安装后配置](/tutorials/getting-started/setup) 完整走一遍。

---

## 误区三：和 ChatGPT 一样，打开网页就能用

**误解**：期待有类似 chat.openai.com 的网页界面。

**实际**：

- OpenClaw 主要入口是 **你已有的聊天软件**（Telegram、飞书等）
- 有 Web 控制台（`openclaw dashboard`），但主要用于配置、配对、查看状态，不是主聊天界面
- 聊天发生在 Telegram / 飞书等 App 里，不是独立网页

---

## 误区四：数据会自动同步到 OpenAI/Anthropic

**误解**：担心对话内容被厂商收集。

**实际**：

- 对话会发给所选 Provider 的 API（如 Anthropic、OpenAI）用于生成回复
- 若使用 **Ollama 本地模型**，数据完全不出本机
- 会话、配置、媒体文件存储在 **你指定的目录**（默认 `~/.openclaw`），不在厂商服务器

隐私策略取决于你选择的 Provider 和部署方式。

---

## 误区五：一个 Bot 只能连一个聊天软件

**误解**：接了 Telegram 就不能接飞书。

**实际**：**同一个 Gateway 可同时接入多个通道**。Telegram、飞书、Discord、WhatsApp 等可并存，共用同一套 Agent 和模型配置。

---

## 误区六：必须会写代码才能用

**误解**：只有开发者才能部署。

**实际**：

- **macOS 用户**：可下载 .dmg 图形安装包，图形界面完成配置
- **命令行用户**：`openclaw onboard` 向导一步步引导，无需写代码
- 高级定制（Skills、Plugins）才需要一定技术背景

---

## 误区七：消息不回就是坏了

**误解**：发消息没回复就认为系统故障。

**实际**：常见原因包括：

1. **未配对**：首次使用需 `openclaw pairing approve`
2. **Gateway 未运行**：`openclaw gateway status` 检查
3. **通道未连接**：`openclaw channels status` 检查
4. **被加入黑名单**：检查配对与权限配置

先跑 `openclaw doctor` 做一次全面诊断。

---

## 误区八：ClawdBot 和 OpenClaw 是两套东西

**误解**：以为是不同项目。

**实际**：**OpenClaw 是 ClawdBot 的品牌升级版**，同一套代码和架构，功能延续。之前用 ClawdBot 的，直接迁移到 OpenClaw 即可。

---

## 延伸阅读

- [部署后验证](/openclaw-knowledge/module-01-basics/02-deploy/04-verify) — 故障排查
- [消息不回](/openclaw-knowledge/module-04-troubleshooting/02-ops/01-no-reply) — 频道与配对排查
