# 实操案例

> 完整案例集（付费）

端到端案例、配置示例与效果展示，供付费用户参考与复现。

---

## 一、案例类型

| 类型 | 说明 |
|------|------|
| **部署案例** | 从零到可用的完整部署流程（本地/云/Docker） |
| **场景案例** | 单一场景的配置与效果（投研、办公、开发等） |
| **配置示例** | 通道、模型、Cron、技能等配置片段 |
| **避坑案例** | 常见报错与解决过程 |

---

## 二、部署案例

### 本地 + Ollama

- **目标**：完全本地、零 API 成本

- **步骤概要**：
  1. 安装 Node、Ollama
  2. `ollama pull qwen2.5:7b`
  3. `npm install -g openclaw@latest`，`openclaw onboard`
  4. 配置 `OLLAMA_API_KEY`，选择 Ollama 模型
  5. 接入 Telegram 等通道，测试

- **参考**：[本地部署](/openclaw-knowledge/module-01-basics/02-deploy/03-local)

### 阿里云轻量 + Claude

- **目标**：7×24 运行，使用 Claude

- **步骤概要**：
  1. 购买轻量服务器（2核2G 起）
  2. 安装 Node，`openclaw onboard`
  3. 配置 Anthropic API Key
  4. 配置通道，配对测试
  5. 使用 systemd 或 pm2 守护进程

- **参考**：[云部署](/openclaw-knowledge/module-01-basics/02-deploy/02-cloud)

---

## 三、场景案例

### A股异动监控

- **能力**：关键词/标的异动推送

- **配置要点**：
  - Cron 定时轮询或 Webhook 触发
  - 数据源接入（API 或爬虫）
  - Agent 处理逻辑与推送通道

- **参考**：[A股投研场景](/openclaw-knowledge/module-02-scenarios/01-a-share/)

### 日报自动生成

- **能力**：每日定时生成工作日报

- **配置要点**：
  - Cron 每日固定时间触发
  - Agent 收集日历、邮件、任务等
  - 生成摘要并推送至飞书/Telegram

- **参考**：[办公自动化](/openclaw-knowledge/module-02-scenarios/02-office/)

### 代码审查辅助

- **能力**：提交代码后自动审查建议

- **配置要点**：
  - Git Hook 或 CI 触发
  - Agent 调用 diff、分析逻辑
  - 输出审查意见到 MR/Issue

- **参考**：[开发运维](/openclaw-knowledge/module-02-scenarios/03-devops/)

---

## 四、配置示例

### 多模型回退

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-sonnet-4",
        fallbacks: ["openai/gpt-4o", "deepseek/deepseek-chat"]
      }
    }
  }
}
```

### Cron 每日 9 点

```json5
{
  cron: {
    timezone: "Asia/Shanghai",
    jobs: [
      {
        schedule: "0 9 * * 1-5",
        agent: "daily-report",
        message: "生成今日日报"
      }
    ]
  }
}
```

### 通道白名单

```json5
{
  channels: {
    telegram: {
      allowFrom: ["123456789"],
      groups: { "*": { requireMention: true } }
    }
  }
}
```

---

## 五、效果展示

（示例，以实际案例为准）

- **部署耗时**：本地 10–15 分钟，云 20–30 分钟
- **响应延迟**：通常 2–10 秒（取决于模型与网络）
- **成本**：Ollama 本地接近 0；API 轻度使用月均 10–50 元

---

## 六、获取更多案例

- 知识库内各场景模块的实操章节
- 专属资源包中的配置模板
- 用户交流群内的分享与问答

---

## 延伸阅读

- [核心场景实操](/openclaw-knowledge/module-02-scenarios/)
- [专属资源包](/openclaw-knowledge/module-05-benefits/02-resources)
- [配置参考](/tutorials/gateway/configuration-reference)
