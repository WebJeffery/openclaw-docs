# 远程运维

> 服务器状态 / 日志清理

通过 OpenClaw Agent + exec 工具，在 Telegram/飞书 中执行远程运维操作：查状态、看日志、清理等。

---

## 一、前置条件

- OpenClaw 部署在可访问目标服务器的环境（如跳板机、同一内网）
- 或通过 SSH 在 Agent 可调用的机器上执行命令
- exec 工具已启用，并配置了 `ssh`、`tail`、`df` 等命令白名单

---

## 二、服务器状态检查

### 常用命令

Agent 可执行（需在白名单中）：

```bash
# 磁盘使用
df -h

# 内存使用
free -m

# 负载
uptime

# 进程
ps aux | head -20

# 端口监听
ss -tlnp  # 或 netstat
```

### Cron 定时巡检

```json5
{
  cron: {
    jobs: [
      {
        schedule: "0 */2 * * *",
        agent: "ops-check",
        message: "检查服务器状态：执行 df -h、free -m、uptime，若磁盘>90%或内存>95%则告警，结果推送到 session ops-alerts"
      }
    ]
  }
}
```

---

## 三、日志查看与清理

### 查看日志

```
查看 /var/log/nginx/error.log 最后 50 行，分析是否有异常
```

Agent 通过 `read_file` 或 exec `tail` 获取内容并分析。

### 日志清理

```
清理 /var/log/app 下超过 7 天的 .log 文件
```

需在 exec 白名单中允许 `find`、`rm`，且建议先用 `find ... -print` 确认再删除。**生产环境慎用自动清理，建议人工审批。**

---

## 四、SSH 远程执行

若 OpenClaw 运行在跳板机，Agent 可执行：

```bash
ssh user@target-server "df -h && free -m"
```

需配置 SSH 免密登录，并将 `ssh` 加入 exec 白名单。

---

## 五、运维 Agent 配置

```json5
{
  agents: {
    "ops-check": {
      model: { primary: "deepseek/deepseek-chat" },
      systemPrompt: {
        content: |
          你是运维巡检助手。任务：
          1. 执行系统状态命令（df、free、uptime 等）
          2. 解析输出，判断是否超过阈值（磁盘>90%、内存>95%）
          3. 若超阈值，在结果中标注「⚠️ 需关注」
          4. 输出简洁的 Markdown 报告
      }
    }
  }
}
```

---

## 六、安全建议

- exec 白名单严格限制，避免 `rm -rf`、`chmod 777` 等
- 敏感操作使用 elevated 模式，需人工确认
- 生产环境日志清理、重启等服务操作建议人工执行
- SSH 使用密钥认证，避免密码出现在命令中

---

## 延伸阅读

- [执行工具](/tutorials/tools/exec)
- [提升模式](/tutorials/tools/elevated)
- [Cron 定时任务](/tutorials/automation/cron-jobs)
