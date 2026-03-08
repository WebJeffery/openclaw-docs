# 整体状态检查

> 核心命令

快速判断 OpenClaw 运行状态的核心命令与健康基准。

---

## 一、openclaw doctor

综合健康检查，推荐首选：

```bash
openclaw doctor
```

输出包含：

- 运行时状态
- RPC 探测
- 通道连接
- 配置校验
- 常见问题修复建议

带自动修复：

```bash
openclaw doctor --fix
```

---

## 二、Gateway 状态

```bash
openclaw gateway status
```

健康基准：`Runtime: running`。

若未运行：

```bash
openclaw gateway start
```

---

## 三、openclaw status

```bash
openclaw status
```

汇总 Gateway、通道、Agent 等整体状态。

---

## 四、RPC 探测

```bash
openclaw gateway status
```

健康基准：`RPC probe: ok`。

若 RPC 失败，通常需重启 Gateway 或检查端口占用。

---

## 五、通道探测

```bash
openclaw channels status --probe
```

验证各通道是否真正连接、可收发消息。输出中应显示已连接/就绪。

---

## 六、配置校验

```bash
openclaw config validate
```

检查配置文件语法与结构，避免因配置错误导致启动失败。

---

## 七、快速决策表

| 现象 | 优先检查 |
|------|----------|
| Agent 不回复 | API Key、Gateway 状态、网络 |
| 仪表板无法连接 | Gateway 是否运行、端口占用 |
| Gateway 启动失败 | 配置文件语法、端口冲突 |
| 通道断连 | Token 是否过期、重新登录 |
| Cron 不触发 | 时区设置、cron 表达式 |

---

## 延伸阅读

- [日志分析](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/02-logs)
- [频道与节点检查](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/03-channels)
- [故障排查总览](/tutorials/help/troubleshooting)
