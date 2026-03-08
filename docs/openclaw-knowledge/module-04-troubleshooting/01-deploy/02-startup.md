# 启动坑

> Gateway 失败 / 端口占用等

Gateway 启动失败、端口冲突、进程残留等常见问题与排查方法。

---

## 一、端口占用（EADDRINUSE）

### 现象

```
Error: listen EADDRINUSE: address already in use :::18789
another gateway instance is already listening
```

### 排查

```bash
# 检查 18789 端口（默认）被谁占用
lsof -i :18789   # macOS/Linux
netstat -ano | findstr 18789   # Windows

# 查看是否有多个 Gateway 进程
ps aux | grep "openclaw gateway"
```

### 处理

```bash
# 强制重启（会终止占用进程）
openclaw gateway --force

# 或换端口
openclaw gateway --port 18790
```

配置中永久改端口：

```json5
{
  gateway: {
    port: 18790
  }
}
```

---

## 二、绑定与认证错误

### 现象

```
refusing to bind gateway ... without auth
Gateway start blocked: set gateway.mode=local
```

### 处理

- **非 loopback 绑定**（如 `lan`、`tailnet`）必须配置 `gateway.auth.token` 或 `gateway.auth.password`
- **本地模式**：在配置中设置 `gateway.mode: "local"`，或运行 `openclaw configure`

---

## 三、配置文件语法错误

### 现象

启动时报 JSON/JSON5 解析错误。

### 排查

```bash
openclaw config validate
```

### 处理

- 检查 `~/.openclaw/openclaw.json` 的括号、逗号、引号
- 可用 [JSON5 校验器](https://json5.org/) 在线验证
- 若配置损坏，备份后运行 `openclaw reset` 重新配置（会清空配置）

---

## 四、服务已安装但未运行

### 现象

`openclaw gateway status` 显示 `Runtime: stopped`。

### 排查

```bash
openclaw gateway status --deep
openclaw logs --follow
```

### 处理

```bash
openclaw gateway start
# 若为服务模式
openclaw gateway install --force
openclaw gateway restart
```

---

## 五、进程残留

多次启动/停止后，可能有僵尸进程占用端口。

### 处理

```bash
# 查找并终止
pkill -f "openclaw gateway"
# 或
killall node  # 谨慎，会终止所有 Node 进程
```

---

## 延伸阅读

- [网关故障排除](/tutorials/gateway/troubleshooting)
- [Gateway 配置](/tutorials/gateway/configuration)
- [认证坑](/openclaw-knowledge/module-04-troubleshooting/01-deploy/03-auth)
