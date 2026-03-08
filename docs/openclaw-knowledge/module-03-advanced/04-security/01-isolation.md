# 环境隔离

> 降低信息泄露风险

通过沙箱（Sandbox）、工作区隔离、多 Agent 分离等方式，降低敏感信息泄露与误操作风险。

---

## 一、沙箱（Sandbox）

### 作用

工具（exec、文件读写等）在 **Docker 容器** 中运行，与宿主机隔离，限制 Agent 可访问的目录与网络。

### 配置

```json5
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main",  // off | non-main | all
        scope: "session",  // session | agent | shared
        workspaceAccess: "none"  // none | ro | rw
      }
    }
  }
}
```

| 模式 | 说明 |
|------|------|
| `off` | 不使用沙箱，工具在宿主机运行 |
| `non-main` | 非主会话（群组、Cron、子 Agent）使用沙箱 |
| `all` | 所有会话都使用沙箱 |

### 工作区访问

- `none`：工具只能访问 `~/.openclaw/sandboxes` 下的沙箱工作区
- `ro`：只读挂载宿主机工作区
- `rw`：可读写，需谨慎

---

## 二、多 Agent 隔离

不同 Agent 承担不同职责，配置独立沙箱与工具策略：

```json5
{
  agents: {
    "sensitive-agent": {
      sandbox: { mode: "all", workspaceAccess: "none" },
      tools: { exec: { allowedBinaries: [] } }
    },
    "dev-agent": {
      sandbox: { mode: "non-main", workspaceAccess: "ro" }
    }
  }
}
```

敏感任务用严格隔离的 Agent，开发任务可适当放宽。

---

## 三、会话与通道策略

### 配对（Pairing）

默认新用户需配对才能使用，避免未授权访问。

### 白名单 / 黑名单

```json5
{
  channels: {
    telegram: {
      dmPolicy: "allowlist",
      allowFrom: ["user_id_1", "user_id_2"],
      groupPolicy: "allowlist",
      groupAllowFrom: ["group_id_1"]
    }
  }
}
```

### sendPolicy

控制哪些会话可向外发送消息：

```json5
{
  session: {
    sendPolicy: {
      rules: [
        { match: { channel: "discord", chatType: "group" }, action: "deny" }
      ],
      default: "allow"
    }
  }
}
```

---

## 四、数据目录隔离

- 配置、会话、媒体默认在 `~/.openclaw`
- 可配置 `OPENCLAW_STATE_DIR` 指向独立目录
- 多环境部署时使用不同 state 目录，避免混淆

---

## 五、诊断

```bash
openclaw sandbox explain
openclaw sandbox explain --agent <agent-id>
```

查看当前生效的沙箱模式与工具策略。

---

## 延伸阅读

- [沙箱配置](/tutorials/gateway/sandboxing)
- [沙箱 vs 工具策略 vs 提权](/tutorials/gateway/sandbox-vs-tool-policy-vs-elevated)
- [密钥保护](/openclaw-knowledge/module-03-advanced/04-security/02-keys)
