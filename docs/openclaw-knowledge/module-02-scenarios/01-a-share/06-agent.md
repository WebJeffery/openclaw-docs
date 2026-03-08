# 专属短线分析师智能体

> 每日简报生成

通过专用 Agent + Cron，每日自动生成市场简报，推送到 Telegram、飞书等渠道。

---

## 一、简报内容结构

1. **大盘概览**：指数涨跌、成交额、北向资金
2. **板块主线**：领涨/领跌板块
3. **异动标的**：涨幅居前、成交额放大
4. **持仓简评**：若有持仓，简要点评
5. **今日关注**：重要事件、财报、政策

---

## 二、专用 Agent 配置

```json5
{
  agents: {
    "daily-brief": {
      model: { primary: "anthropic/claude-3-5-sonnet" },
      systemPrompt: {
        content: |
          你是 A股每日市场简报助手。每个交易日收盘后生成简报，包含：
          1. 大盘：主要指数涨跌、成交额、北向资金
          2. 板块：领涨/领跌前 5
          3. 异动：涨幅前 10、成交额前 10
          4. 若用户配置了持仓，增加持仓简评
          5. 今日关注：重要公告、政策、外围市场
          数据通过可用工具获取，不足部分用 web_search 补充。
          输出简洁 Markdown，控制在 500 字以内。
      }
    }
  }
}
```

---

## 三、Cron 定时生成

```json5
{
  cron: {
    timezone: "Asia/Shanghai",
    jobs: [
      {
        schedule: "30 15 * * 1-5",  // 收盘后 15:30
        agent: "daily-brief",
        message: "生成今日 A股市场简报，推送到 session daily-brief"
      }
    ]
  }
}
```

---

## 四、Skill 注入简报规范

```markdown
# ~/.openclaw/skills/daily-brief/SKILL.md

# 每日简报规范

## 指令
简报格式：
## 📊 今日市场简报 (YYYY-MM-DD)
### 大盘
- 上证：涨跌 X%，成交 X 亿
- 北向：净流入/出 X 亿
### 板块
- 领涨：板块A X%，板块B X%
- 领跌：...
### 异动
| 代码 | 名称 | 涨幅 | 成交额 |
### 今日关注
- 事件1
- 事件2
```

---

## 五、推送配置

将 `daily-brief` 会话绑定到 Telegram 群或飞书群，Agent 通过 message 工具发送即可实现每日自动推送。

---

## 六、进阶：多版本简报

可配置多个 Cron，生成不同侧重点的简报：

- **早盘版**（9:30）：隔夜外围、今日关注
- **午盘版**（12:00）：上午走势、异动
- **收盘版**（15:30）：全日总结、明日关注

---

## 延伸阅读

- [Cron 定时任务](/tutorials/automation/cron-jobs)
- [板块主线追踪](/openclaw-knowledge/module-02-scenarios/01-a-share/04-sector)
- [全市场异动监控](/openclaw-knowledge/module-02-scenarios/01-a-share/02-monitor)
