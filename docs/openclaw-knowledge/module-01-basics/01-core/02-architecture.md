# 核心架构拆解

> 四大核心模块，快速超越 90% 用户

理解 OpenClaw 的架构，能帮你少踩坑、更快排查问题、更好做定制。一句话概括：

**CLI 发命令 → Gateway 启动编排 → Channel 收发消息 → Routing 选 Agent/Session → Auto-Reply 调模型生成回复 → Outbound 发回通道 → Config/Session/Media 持久化。**

---

## 四大核心模块

### 1. Gateway（网关）——指挥部

**职责**：常驻进程，统一管理所有通道连接、消息路由、Agent 调度与会话存储。

- 默认监听 **18789** 端口
- 启动命令：`openclaw gateway`
- 必须持续运行，AI 才能响应消息

**类比**：像邮局，所有消息都要经过这里，再分发给对应的 AI 处理。

```bash
openclaw gateway status   # 查看是否在运行
openclaw gateway start    # 启动
openclaw gateway restart  # 重启
```

---

### 2. Channel（通道适配器）——收发入口

**职责**：对接各类聊天平台（Telegram、WhatsApp、飞书等），把平台消息转成统一格式，再把 AI 回复发回去。

- 每个通道有独立的适配器（Adapter）
- 消息格式统一，无需为每个平台重写逻辑
- 支持私聊、群聊、话题（Topic）等

**常见通道**：Telegram、Discord、Slack、飞书、WhatsApp、Signal、iMessage、MS Teams、Google Chat、Matrix 等。

```bash
openclaw channels login --channel telegram   # 配置 Telegram
openclaw channels status                     # 查看各通道连接状态
```

---

### 3. Agent（智能体）——执行引擎

**职责**：接收用户消息 → 组装上下文 → 调用大模型推理 → 执行工具（如搜索、读文件）→ 流式输出回复 → 持久化会话。

**核心流程（Agent Loop）**：

1. 接收输入
2. 上下文（Context）组装（含系统提示词、技能、历史）
3. 模型推理
4. 工具执行（如需要）
5. 流式输出回复
6. 持久化会话

**关键概念**：

- **Session（会话）**：一次对话的上下文，按通道 + 用户/群组区分
- **Skills（技能）**：注入领域知识、工具、工作流
- **Tools（工具）**：搜索、读文件、执行代码等可调用能力

---

### 4. Provider（模型提供商）——AI 大脑

**职责**：对接各类大模型 API，把 Agent 的请求转成模型调用，并处理流式返回。

**常见 Provider**：

| 提供商 | 代表模型 | 特点 |
|--------|----------|------|
| Anthropic | Claude 3.5 / 3 系列 | 官方推荐，中文好 |
| OpenAI | GPT-4o / o1 | 生态成熟 |
| DeepSeek | DeepSeek-V3 / R1 | 性价比高，国内直连 |
| 通义千问 | Qwen-Max / Plus | 国内服务，合规 |
| Ollama | 本地模型 | 完全离线，隐私优先 |

支持多模型切换、Failover（主模型失败时自动切备用）。

---

## 模块关系简图

```
用户发消息（Telegram/飞书/...）
        ↓
   Channel 适配器（接收）
        ↓
   Gateway（路由、鉴权）
        ↓
   Routing（选 Agent / Session）
        ↓
   Auto-Reply（触发 Agent）
        ↓
   Agent Loop（上下文 → 模型 → 工具 → 回复）
        ↓
   Provider（调用 Claude/GPT/DeepSeek/...）
        ↓
   Outbound（发回 Channel）
        ↓
用户收到回复
```

---

## 延伸阅读

- [智能体循环](/tutorials/concepts/agent-loop) — Agent 执行细节
- [网关使用指南](/tutorials/gateway/) — Gateway 运维
- [通道接入](/tutorials/channels/) — 各平台配置
