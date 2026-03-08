# 任务优先级设置

> 避资源冲突

通过命令队列、并发控制、会话串行化等机制，避免多任务同时抢占资源导致冲突或限流。

---

## 一、命令队列（Command Queue）

OpenClaw 按会话串行化入站消息，避免同一会话内多请求并发冲突。

### 队列模式

| 模式 | 行为 |
|------|------|
| **collect** | 合并排队消息为单次后续轮次（默认） |
| **steer** | 立即注入当前运行，在下一工具边界后取消待处理工具 |
| **followup** | 当前运行结束后排入下一轮 |
| **steer-backlog** | 立即注入并保留后续轮次 |

### 配置

```json5
{
  messages: {
    queue: {
      mode: "collect",
      debounceMs: 1000,
      cap: 20,
      drop: "summarize",
      byChannel: { discord: "collect" }
    }
  }
}
```

---

## 二、并发控制

### 全局并发上限

```json5
{
  agents: {
    defaults: {
      maxConcurrent: 4  // 主通道默认 4，子 Agent 默认 8
    }
  }
}
```

### 每通道并发

未配置的通道默认为 1，即同一通道内串行。可通过 `messages.queue.byChannel` 等调整。

---

## 三、会话串行化

按 `sessionKey` 入队，同一会话仅有一个活跃运行。不同会话可并行，但总并行度受 `maxConcurrent` 限制。

---

## 四、Cron 与人工消息

Cron 触发的任务使用 `cron:` 前缀的 session，与人工消息的 `main` 等会话隔离，互不抢占。但共享模型资源，高并发时可能触发 API 限流。

### 建议

- 错峰 Cron：避免在业务高峰与 Cron 重叠
- 重要 Cron 用独立 Agent，减少与日常对话的竞争

---

## 五、子 Agent 优先级

`sessions_spawn` 创建的子 Agent 有独立会话，其运行会占用全局并发槽位。若并发上限较小，大量 spawn 可能导致排队。

---

## 六、诊断

```bash
openclaw logs --follow
```

若排队超过约 2 秒，会发出简短通知。可据此调整 `maxConcurrent` 或队列模式。

---

## 延伸阅读

- [命令队列](/tutorials/concepts/queue)
- [定时任务](/openclaw-knowledge/module-03-advanced/03-agents/02-cron)
- [多智能体拆分任务](/openclaw-knowledge/module-03-advanced/03-agents/01-multi)
