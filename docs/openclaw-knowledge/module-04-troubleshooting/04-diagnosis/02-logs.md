# 日志分析

> 快速定位问题

日志位置、关键字段与常见错误码速查。

---

## 一、日志命令

```bash
# 最近 N 条
openclaw logs --limit 100

# 实时跟踪（类似 tail -f）
openclaw logs --follow

# 按模块过滤
openclaw logs --filter cron
openclaw logs --filter heartbeat
openclaw logs --filter webhook
openclaw logs --filter auth
openclaw logs --filter gateway

# 按 Agent 过滤
openclaw logs --agent <agent-name>

# 按时间范围
openclaw logs --since "2026-02-20 00:00:00"

# JSON 格式（便于分析）
openclaw logs --json | jq '.[] | select(.level == "error")'
```

---

## 二、日志位置

默认日志由 OpenClaw 管理，可通过环境变量或配置调整：

- 标准输出：`openclaw gateway start` 时输出到终端
- 持久化：取决于部署方式（systemd、Docker 等），通常为 `~/.openclaw/logs` 或系统日志目录

---

## 三、关键字段

| 字段 | 含义 |
|------|------|
| `level` | error / warn / info / debug |
| `module` | 模块名（gateway、channel、agent 等） |
| `message` | 日志内容 |
| `agent` | 关联的 Agent 名称 |
| `channel` | 关联的通道 |
| `error` / `stack` | 错误详情与堆栈 |

---

## 四、常见错误码

| 错误 / 现象 | 可能原因 |
|-------------|----------|
| `401 Unauthorized` | API Key 无效或过期 |
| `429 Too Many Requests` | API 限流 |
| `ECONNREFUSED` | 服务未启动或端口错误 |
| `blocked` / `allowlist` | 黑名单/白名单策略拦截 |
| `pairing required` | 未配对或待审批 |
| `NODE_BACKGROUND_UNAVAILABLE` | 手机节点退到后台 |
| `SYSTEM_RUN_DENIED` | exec 白名单未包含该命令 |

---

## 五、排查流程

1. `openclaw logs --follow` 实时观察，复现问题
2. `openclaw logs --filter <module>` 按模块缩小范围
3. `openclaw logs --json` 导出后用 jq 筛选 error 级别
4. 结合 [整体状态检查](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/01-status) 与 [频道检查](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/03-channels) 综合判断

---

## 延伸阅读

- [整体状态检查](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/01-status)
- [自动化日志速查](/tutorials/automation/troubleshooting#日志查看命令速查)
- [故障排查总览](/tutorials/help/troubleshooting)
