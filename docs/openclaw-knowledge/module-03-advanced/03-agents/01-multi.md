# 多智能体拆分任务

> 提升效率

通过 Session Tools（sessions_spawn、sessions_send、sessions_list、sessions_history），Agent 可创建子 Agent、跨会话协作，实现任务拆分与并行。

---

## 一、Session Tools 概览

| 工具 | 用途 |
|------|------|
| **sessions_list** | 列出活跃会话及元数据 |
| **sessions_history** | 获取某会话的历史消息 |
| **sessions_send** | 向另一会话发送消息，支持 ping-pong 式回复 |
| **sessions_spawn** | 创建子 Agent 在独立会话中执行任务，完成后回传结果 |

---

## 二、sessions_spawn：子 Agent 执行

### 典型流程

1. 主 Agent 收到复杂任务
2. 调用 `sessions_spawn`，传入 `task` 和可选 `model`、`agentId`
3. 子 Agent 在独立会话中执行，主 Agent 不阻塞
4. 完成后结果通过 announce 回传到主 Agent 所在会话

### 参数要点

- `task`：子 Agent 要执行的任务描述
- `mode`：`run`（默认，执行完即结束）或 `session`（创建持久会话）
- `model`：可选，覆盖子 Agent 使用的模型
- `agentId`：可选，指定子 Agent 类型（需在 `subagents.allowAgents` 中允许）
- `cleanup`：`keep`（默认）或 `delete`，子会话是否保留

### 子 Agent 限制

- 子 Agent 不能再次调用 `sessions_spawn`（禁止嵌套）
- 子 Agent 默认不包含 session tools，避免循环依赖

---

## 三、sessions_send：跨会话协作

主 Agent 可向另一会话发送消息，并等待回复（ping-pong）：

- `sessionKey`：目标会话
- `message`：发送内容
- `timeoutSeconds`：等待超时，0 表示 fire-and-forget

适用于：主 Agent 协调多个专门 Agent，收集结果后汇总。

---

## 四、任务拆分策略

### 按类型拆分

- **研究 Agent**：负责搜集、分析
- **写作 Agent**：负责成文
- **审核 Agent**：负责检查

主 Agent 调用 `sessions_spawn` 分别执行，再汇总。

### 按并行度拆分

将大任务拆成多个独立子任务，并行 spawn，最后合并结果。

例如：分析 10 只股票，spawn 10 个子任务，每个分析 1 只。

---

## 五、配置要点

### 允许子 Agent

```json5
{
  agents: {
    "main-agent": {
      subagents: {
        allowAgents: ["research-agent", "write-agent"]  // 或 ["*"] 允许任意
      }
    }
  }
}
```

### 会话可见性

```json5
{
  tools: {
    sessions: {
      visibility: "tree"  // self | tree | agent | all
    }
  }
}
```

- `tree`：当前会话 + 其 spawn 的子会话
- `all`：所有会话（需配合 `agents.tools.agentToAgent`）

---

## 六、注意事项

- **sessions_spawn 非阻塞**：返回 `{ status: "accepted", runId, childSessionKey }` 后立即返回，结果通过 announce 异步回传
- **ACP 模式**：若使用 ACP runtime，存在已知问题（如会话历史未持久化），可考虑用 `/claude` 或 `acpx` CLI 作为替代
- **资源**：每个 spawn 会占用模型调用和会话资源，不宜无限制并发

---

## 延伸阅读

- [Session Tools 官方文档](https://docs.openclaw.ai/concepts/session-tool)
- [命令队列](/tutorials/concepts/queue)
- [定时任务](/openclaw-knowledge/module-03-advanced/03-agents/02-cron)
