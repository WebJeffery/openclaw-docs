# 全市场异动监控

> 自定义指令 + 实时推送

通过 Cron 定时任务 + Webhook/通道推送，实现全市场异动监控，并在 Telegram、飞书等渠道实时通知。

---

## 一、整体架构

```
Cron（定时触发）
    ↓
Agent（执行监控逻辑）
    ↓
数据获取（API / web_search）
    ↓
异动判断（涨幅、成交量、资金流向等）
    ↓
推送（Telegram / 飞书 / Webhook）
```

---

## 二、Cron 定时监控

### 配置示例

```json5
{
  cron: {
    timezone: "Asia/Shanghai",
    jobs: [
      {
        schedule: "0 9,10,11,14,15 * * 1-5",  // 交易时段每整点
        agent: "market-monitor",
        message: "执行全市场异动监控：1) 获取今日涨幅前20、成交额前20；2) 筛选异动标的；3) 将结果推送到 Telegram 会话 market-alerts"
      },
      {
        schedule: "30 9 * * 1-5",  // 开盘 30 分钟
        agent: "market-monitor",
        message: "执行开盘异动扫描，重点关注高开、放量、板块轮动"
      }
    ]
  }
}
```

---

## 三、异动监控 Agent 设计

### 专用 Agent 配置

```json5
{
  agents: {
    "market-monitor": {
      model: { primary: "deepseek/deepseek-chat" },
      systemPrompt: {
        content: |
          你是 A股市场异动监控助手。任务：
          1. 通过可用工具/脚本获取今日涨幅、成交额、资金流向等数据
          2. 按预设规则筛选异动标的（如涨幅>5%、成交额>1亿）
          3. 将结果整理为简洁的 Markdown 列表
          4. 通过 message 工具发送到指定会话（sessionKey: market-alerts）
      }
    }
  }
}
```

### 使用 message 工具推送

在 Skill 或系统提示词中说明：异动结果需通过 `message` 工具发送到 `market-alerts` 会话，该会话可绑定 Telegram 群或飞书群，实现实时推送。

---

## 四、推送渠道配置

### Telegram 群组

1. 创建 Telegram 群，将 Bot 拉入
2. 配对并批准 Bot
3. 在 Agent 中指定 `sessionKey` 为该群对应的会话键
4. Agent 调用 `message` 工具即可向该群发送异动报告

### 飞书群组

1. 在飞书创建群，添加 Bot 应用
2. 获取群会话 ID，配置到 OpenClaw
3. Agent 通过飞书通道的 message 接口发送

### Webhook 二次推送

若需推送到企业微信、钉钉等，可：

1. Agent 将异动结果写入本地文件或调用内部 API
2. 由外部脚本监听文件/API，再调用企业微信/钉钉 Webhook 发送

---

## 五、异动规则示例（Skill 注入）

```markdown
# ~/.openclaw/skills/market-monitor/SKILL.md

# 异动监控规则

## 指令
异动筛选标准（可根据需求调整）：
- 涨幅：当日涨幅 > 5%
- 成交额：当日成交额 > 1 亿元
- 量比：量比 > 2
- 板块：所属板块当日涨幅居前

输出格式：
## 今日异动标的
| 代码 | 名称 | 涨幅 | 成交额 | 异动原因 |
|------|------|------|--------|----------|
...
```

---

## 六、注意事项

- **数据延迟**：免费数据源常有 15 分钟延迟，实盘需使用 Level-2 等实时源
- **频率控制**：Cron 不宜过密，避免 API 限流与资源占用
- **合规**：异动监控结果仅供个人参考，不得作为投资建议对外传播

---

## 延伸阅读

- [Cron 定时任务](/tutorials/automation/cron-jobs)
- [Webhook 外部触发](/tutorials/automation/webhook)
- [合规数据接入](/openclaw-knowledge/module-02-scenarios/01-a-share/01-data)
