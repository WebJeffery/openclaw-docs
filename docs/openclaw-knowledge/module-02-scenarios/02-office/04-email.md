# 邮件自动化

> 筛选 / 汇总 / 自动回复

通过 OpenClaw 的 Gmail 集成、Cron 和 Agent，实现邮件筛选、汇总与自动回复。

---

## 一、Gmail 集成

OpenClaw 支持 Gmail 通道，可：

- 接收新邮件并触发 Agent 处理
- 通过 Gmail PubSub 实时接收推送，无需轮询

详见 [Gmail PubSub](/tutorials/automation/gmail-pubsub) 文档。

---

## 二、邮件筛选与汇总

### 思路

1. Agent 通过 Gmail 通道或 API 获取邮件列表
2. 按规则筛选（发件人、主题、标签、时间）
3. 生成摘要或汇总报告
4. 推送到指定会话（Telegram/飞书/邮件）

### Cron 定时汇总

```json5
{
  cron: {
    jobs: [
      {
        schedule: "0 9,18 * * 1-5",
        agent: "email-summary",
        message: "汇总今日重要邮件：筛选来自 boss@、urgent 标签的邮件，生成摘要推送到 session work-summary"
      }
    ]
  }
}
```

---

## 三、自动回复

### 规则型回复

通过 Skill 定义回复规则，例如：

- 主题含「请假」→ 回复「已收到，请抄送 HR」
- 发件人为某名单 → 自动回复固定模板

### Agent 智能回复

Agent 可阅读邮件内容，生成个性化回复。需注意：

- 敏感邮件建议人工审核后再发送
- 可配置为「草稿」模式，由用户确认后发送

---

## 四、OAuth 认证维护

Gmail 使用 OAuth，Token 会过期。建议配置 [OAuth 认证监控](/tutorials/automation/auth-monitoring)：

```bash
openclaw auth status
openclaw auth refresh --channel gmail
```

并设置 Cron 定期检查，避免 Token 过期导致服务中断。

---

## 五、配置要点

- 启用 Gmail 通道并完成 OAuth 授权
- 配置 `agents.defaults` 或专用 Agent 处理邮件任务
- 若需访问其他邮箱（如 Outlook），需通过 IMAP/API 桥接或第三方集成

---

## 延伸阅读

- [Gmail PubSub](/tutorials/automation/gmail-pubsub)
- [OAuth 认证监控](/tutorials/automation/auth-monitoring)
- [Cron 定时任务](/tutorials/automation/cron-jobs)
