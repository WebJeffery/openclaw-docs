# 高风险操作管控

> 防误操作

通过 exec 白名单、提升模式、工具策略等，控制 Agent 可执行的操作，降低误删、误改、越权等风险。

---

## 一、Exec 工具策略

### 白名单（allowedBinaries）

只允许指定命令自动执行，其余需人工确认：

```json5
{
  tools: {
    exec: {
      enabled: true,
      allowedBinaries: ["git", "npm", "python3", "ls", "cat"]
    }
  }
}
```

### 拒绝列表（deny）

明确禁止危险命令：

```json5
{
  tools: {
    exec: {
      deny: ["rm -rf", "chmod 777", "format", "shutdown"]
    }
  }
}
```

::: warning
`deny` 策略**无法被提升模式绕过**。被拒绝的命令在任何模式下都不会执行。
:::

---

## 二、提升模式（Elevated）

`/elevated on` 临时放宽 exec 审批，让更多命令自动执行。适合你全程监督的复杂任务。

- 完成任务后及时 `/elevated off`
- 默认新会话不开启
- 可配置 `requireConfirmation: true`，启用时需二次确认

---

## 三、沙箱 + 工具策略

启用沙箱后，可进一步用 `tools.sandbox.tools` 控制沙箱内可用工具：

```json5
{
  tools: {
    sandbox: {
      tools: {
        allow: ["read_file", "write_file"],
        deny: ["run_terminal_cmd", "exec"]
      }
    }
  }
}
```

---

## 四、按 Agent 差异化

```json5
{
  agents: {
    "restricted-agent": {
      tools: {
        exec: { enabled: false }
      }
    },
    "dev-agent": {
      tools: {
        exec: {
          allowedBinaries: ["git", "npm", "python3"]
        }
      }
    }
  }
}
```

敏感场景用 restricted-agent，开发场景用 dev-agent。

---

## 五、审批流程

对于非白名单命令，Agent 会请求审批。在支持确认的通道（如 Telegram、Discord）中，用户可批准或拒绝每次执行。

---

## 六、诊断

```bash
openclaw sandbox explain --agent <agent-id>
```

查看当前生效的工具策略与沙箱配置。

---

## 延伸阅读

- [提升模式](/tutorials/tools/elevated)
- [沙箱 vs 工具策略 vs 提权](/tutorials/gateway/sandbox-vs-tool-policy-vs-elevated)
- [执行工具](/tutorials/tools/exec)
