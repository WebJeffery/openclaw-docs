# 定时任务坑

> Cron 配置 / 时区 / 不执行

Cron 任务不执行、重复执行或时间不对的排查与解决。

---

## 一、Cron 不执行

### 快速检查

```bash
openclaw gateway status    # 1. Gateway 必须运行
openclaw cron list         # 2. 查看下次执行时间
openclaw agents list       # 3. 确认 Agent 名称正确
openclaw cron run <job>    # 4. 手动触发验证
```

### 常见原因

| 原因 | 处理 |
|------|------|
| Gateway 未运行 | `openclaw gateway start` |
| cron 表达式错误 | 用 [crontab.guru](https://crontab.guru) 验证 |
| 时区错误 | 配置 `cron.timezone: "Asia/Shanghai"` |
| Agent 名称拼写错误 | `openclaw agents list` 确认，区分大小写 |

---

## 二、时区问题

### 现象

任务在错误时间执行，或与预期相差数小时。

### 处理

```json5
{
  cron: {
    timezone: "Asia/Shanghai",
    jobs: [
      {
        schedule: "0 9 * * 1-5",
        agent: "daily-report",
        message: "生成日报"
      }
    ]
  }
}
```

单独为某任务设置时区：

```json5
{
  schedule: "0 9 * * *",
  timezone: "America/New_York",
  agent: "my-agent",
  message: "..."
}
```

---

## 三、Cron 重复执行

### 原因

多个 Gateway 实例同时运行，每个都在调度 Cron。

### 诊断

```bash
ps aux | grep "openclaw gateway"
openclaw gateway list-instances
```

### 处理

```bash
openclaw gateway stop --all
openclaw gateway start
```

确保只运行一个 Gateway 实例。

---

## 四、Heartbeat 停止

### 现象

Heartbeat 不再发送，依赖它的流程中断。

### 排查

```bash
openclaw agents status <agent-name>
openclaw logs --filter heartbeat --agent <agent-name>
openclaw config show | grep -A 5 heartbeat
```

### 处理

```bash
openclaw agents restart <agent-name>
```

---

## 五、Webhook 401 / 404

### 401 Unauthorized

- 检查 `Authorization: Bearer <token>` 格式
- Token 前需有 `Bearer ` 空格
- 配置中的 `secret` 与请求 Token 一致

### 404 Agent Not Found

- `openclaw agents list` 确认 Agent 名称
- 配置中的 agent 名称区分大小写

---

## 六、日志与调试

```bash
openclaw logs --filter cron
openclaw logs --filter heartbeat
openclaw logs --follow
openclaw config validate
```

---

## 延伸阅读

- [自动化故障排查](/tutorials/automation/troubleshooting)
- [Cron 定时任务](/tutorials/automation/cron-jobs)
- [Hooks 事件钩子](/tutorials/automation/hooks)
