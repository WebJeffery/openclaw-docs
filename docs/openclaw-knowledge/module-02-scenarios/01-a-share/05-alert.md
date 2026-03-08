# 持仓风险预警

> 飞书 / 微信 / Telegram 推送

通过 Cron 定时检查持仓标的，在触发风险条件时自动推送到飞书、Telegram 等渠道。

---

## 一、预警逻辑

1. **持仓列表**：在配置或 Skill 中维护关注标的
2. **风险规则**：跌幅超阈值、跌破均线、异常放量等
3. **定时执行**：Cron 在指定时间触发 Agent
4. **推送**：通过 message 工具发送到绑定飞书/Telegram 的会话

---

## 二、Cron 配置

```json5
{
  cron: {
    timezone: "Asia/Shanghai",
    jobs: [
      {
        schedule: "30 10,14 * * 1-5",
        agent: "position-alert",
        message: "检查持仓风险：持仓列表 [000001.SZ, 600036.SH]，规则：单日跌幅>5%或跌破20日均线则预警，结果推送到 session risk-alerts"
      }
    ]
  }
}
```

---

## 三、风险预警 Agent

```json5
{
  agents: {
    "position-alert": {
      model: { primary: "deepseek/deepseek-chat" },
      systemPrompt: {
        content: |
          你是持仓风险预警助手。任务：
          1. 获取持仓列表中各标的的当日涨跌、均线、成交量
          2. 按规则筛选触发预警的标的
          3. 若无预警，输出「今日无风险预警」
          4. 若有预警，按「代码 名称 触发原因 当前价 跌幅」格式列表
          5. 通过 message 工具发送到 sessionKey: risk-alerts
      }
    }
  }
}
```

---

## 四、飞书推送配置

1. **创建飞书群**，添加 Bot 应用
2. **获取会话 ID**，在 OpenClaw 中配置对应 session
3. Agent 使用 `message` 工具时指定该 session，即可发到飞书群

### 企业微信 / 钉钉

若 OpenClaw 未直接支持，可：

1. Agent 将预警内容写入文件或调用内部 API
2. 外部脚本监听后，调用企业微信/钉钉 Webhook 发送

---

## 五、Telegram 推送

1. 创建 Telegram 群或使用私聊
2. 将 Bot 拉入群或私聊配对
3. 配置 `risk-alerts` 会话绑定该群/私聊
4. Agent 通过 message 工具发送即可

---

## 六、预警规则示例（Skill）

```markdown
# ~/.openclaw/skills/position-alert/SKILL.md

# 持仓风险预警规则

## 指令
预警条件（满足任一即触发）：
- 单日跌幅 > 5%
- 跌破 20 日均线
- 成交量较 5 日均量放大 > 200%
- 北向资金单日净流出该股 > 5000 万（若有数据）

输出格式：
🚨 持仓风险预警
- 000001.SZ 平安银行：单日跌幅 -6.2%，触发跌幅预警
- 600036.SH 招商银行：跌破 20 日均线，当前价 35.2
```

---

## 七、注意事项

- 持仓列表需手动维护或通过配置注入
- 数据延迟可能影响预警及时性
- 预警仅供参考，不构成投资建议

---

## 延伸阅读

- [Cron 定时任务](/tutorials/automation/cron-jobs)
- [通讯渠道配对](/openclaw-knowledge/module-01-basics/03-basics/03-channels)
- [全市场异动监控](/openclaw-knowledge/module-02-scenarios/01-a-share/02-monitor)
