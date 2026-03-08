# 基础命令汇总

> 常用命令速查（免费）

OpenClaw 核心命令速查表，便于日常排查与操作。

---

## 一、Gateway 与状态

| 命令 | 说明 |
|------|------|
| `openclaw gateway start` | 启动 Gateway |
| `openclaw gateway stop` | 停止 Gateway |
| `openclaw gateway restart` | 重启 Gateway |
| `openclaw gateway status` | 查看运行状态、RPC 探测 |
| `openclaw status` | 整体状态汇总 |
| `openclaw doctor` | 综合健康检查 |
| `openclaw doctor --fix` | 健康检查并尝试自动修复 |

---

## 二、配置

| 命令 | 说明 |
|------|------|
| `openclaw configure` | 交互式配置向导 |
| `openclaw config show` | 查看当前配置 |
| `openclaw config validate` | 校验配置语法 |
| `openclaw config get <path>` | 获取指定配置项 |
| `openclaw config set <path> <value>` | 设置配置项 |

---

## 三、通道

| 命令 | 说明 |
|------|------|
| `openclaw channels status` | 查看通道连接状态 |
| `openclaw channels status --probe` | 带探测的通道状态 |
| `openclaw channels login --channel <name>` | 登录指定通道 |
| `openclaw pairing list <channel>` | 查看待审批配对 |
| `openclaw pairing approve <channel> <CODE>` | 批准配对 |

---

## 四、Agent 与模型

| 命令 | 说明 |
|------|------|
| `openclaw agents list` | 列出所有 Agent |
| `openclaw agents status <name>` | 查看 Agent 状态 |
| `openclaw agents restart <name>` | 重启 Agent |
| `openclaw models list` | 列出可用模型 |
| `openclaw models status` | 模型提供商状态 |

---

## 五、自动化

| 命令 | 说明 |
|------|------|
| `openclaw cron list` | 列出 Cron 任务及下次执行时间 |
| `openclaw cron run <job>` | 手动触发 Cron 任务 |
| `openclaw hooks list` | 列出 Hooks |
| `openclaw hooks run <name>` | 手动执行 Hook |

---

## 六、技能与插件

| 命令 | 说明 |
|------|------|
| `openclaw skills list` | 列出已加载技能 |
| `openclaw skills search <keyword>` | 搜索技能 |
| `openclaw skills install <id>` | 安装技能 |
| `openclaw plugins list` | 列出插件 |
| `openclaw plugins diagnose` | 插件诊断、冲突检查 |

---

## 七、日志与诊断

| 命令 | 说明 |
|------|------|
| `openclaw logs --limit N` | 最近 N 条日志 |
| `openclaw logs --follow` | 实时跟踪日志 |
| `openclaw logs --filter <module>` | 按模块过滤 |
| `openclaw logs --agent <name>` | 按 Agent 过滤 |
| `openclaw logs --since "YYYY-MM-DD HH:mm:ss"` | 按时间范围 |

---

## 八、安装与向导

| 命令 | 说明 |
|------|------|
| `npm install -g openclaw@latest` | 安装 / 更新 OpenClaw |
| `openclaw onboard` | 交互式配置向导 |
| `openclaw onboard --install-daemon` | 向导并安装系统服务 |

---

## 九、其他

| 命令 | 说明 |
|------|------|
| `openclaw nodes status` | 节点状态 |
| `openclaw security audit` | 安全审计 |
| `openclaw auth status` | 认证状态 |
| `openclaw auth refresh --all` | 刷新 OAuth Token |

---

## 延伸阅读

- [整体状态检查](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/01-status)
- [日志分析](/openclaw-knowledge/module-04-troubleshooting/04-diagnosis/02-logs)
- [故障排查](/tutorials/help/troubleshooting)
