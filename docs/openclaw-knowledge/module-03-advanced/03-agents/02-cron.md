# 定时任务

> Cron 配置 + 故障排查

Cron 让 Agent 在指定时间自动执行，无需人工触发。掌握配置与排查可避免「任务不执行」等常见问题。

---

## 一、基本配置

```json5
{
  cron: {
    timezone: "Asia/Shanghai",
    jobs: [
      {
        schedule: "0 9 * * 1-5",
        agent: "daily-summary",
        message: "生成今日工作总结"
      }
    ]
  }
}
```

### Cron 表达式（5 段）

```
分(0-59) 时(0-23) 日(1-31) 月(1-12) 周(0-7)
```

| 示例 | 含义 |
|------|------|
| `0 9 * * 1-5` | 工作日 9:00 |
| `0 */2 * * *` | 每 2 小时 |
| `30 8 1 * *` | 每月 1 日 8:30 |
| `*/15 * * * *` | 每 15 分钟 |

---

## 二、执行模式

### 主会话模式（isolated: false）

复用 Agent 已有会话，保持上下文。适合需要记忆的场景。

### 隔离模式（isolated: true）

每次执行创建新会话，互不影响。适合独立批量任务。

```json5
{
  cron: {
    jobs: [
      {
        schedule: "0 2 * * *",
        agent: "report-agent",
        message: "生成昨日报告",
        isolated: true
      }
    ]
  }
}
```

---

## 三、模型与投递

### 模型覆盖

```json5
{
  schedule: "0 9 * * 1-5",
  agent: "analysis-agent",
  message: "执行深度分析",
  model: "anthropic/claude-opus-4-6"
}
```

### 投递到通道

```json5
{
  delivery: {
    channel: "slack",
    target: "#daily-reports"
  }
}
```

---

## 四、CLI 命令

```bash
openclaw cron list          # 列出所有任务及下次执行时间
openclaw cron run <job-name>  # 手动触发

# 验证配置
openclaw config validate
openclaw agents list        # 确认 Agent 存在
```

---

## 五、故障排查

### 任务不执行

| 步骤 | 操作 |
|------|------|
| 1 | `openclaw gateway status` 确认 Gateway 在运行 |
| 2 | `openclaw cron list` 查看 NEXT RUN 是否合理 |
| 3 | 检查 `timezone`，默认可能为 UTC |
| 4 | 用 [crontab.guru](https://crontab.guru) 验证表达式 |
| 5 | `openclaw cron run <job-name>` 手动触发，排除调度问题 |

### 时区错误

```json5
{
  cron: {
    timezone: "Asia/Shanghai"  // 必须显式设置
  }
}
```

### 任务重复执行

确保只运行一个 Gateway 实例，多实例会导致 Cron 重复触发。

---

## 六、日志查看

```bash
openclaw logs --filter cron
openclaw logs --follow
```

手动触发后观察日志，确认 Agent 是否被正确调用。

---

## 延伸阅读

- [Cron 定时任务](/tutorials/automation/cron-jobs)
- [自动化故障排查](/tutorials/automation/troubleshooting)
- [任务优先级设置](/openclaw-knowledge/module-03-advanced/03-agents/03-priority)
