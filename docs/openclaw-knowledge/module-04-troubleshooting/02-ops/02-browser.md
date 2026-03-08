# 浏览器控制坑

> Chrome 扩展 / CDP 问题

浏览器工具（Browser Tool）相关问题的排查与解决。

---

## 一、Chrome/Chromium 未安装或路径错误

### 现象

```
browser.executablePath not found
Failed to start Chrome CDP on port
```

### 处理

**Linux**：推荐安装 Google Chrome deb 包，避免 Snap 版 Chromium 的沙箱冲突：

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
sudo apt-get install -f
```

**指定路径**：

```json5
{
  tools: {
    browser: {
      executablePath: "/usr/bin/google-chrome-stable"
    }
  }
}
```

---

## 二、Linux 沙箱权限问题

### 现象

Chrome 启动失败，Permission denied、沙箱相关错误。

### 处理

在**受控环境**下可临时添加（生产慎用）：

```json5
{
  tools: {
    browser: {
      launchArgs: ["--no-sandbox", "--disable-setuid-sandbox"]
    }
  }
}
```

::: warning
`--no-sandbox` 会降低隔离级别，仅建议在私有服务器或开发环境使用。
:::

---

## 三、Chrome 扩展中继未连接

### 现象

```
Chrome extension relay is running, but no tab is connected
Browser attachOnly is enabled ... not reachable
```

### 处理

1. 安装 OpenClaw Chrome 扩展
2. 在 Chrome 中打开需控制的页面（如 chat.openai.com）
3. 确保扩展已附加到该标签页
4. 若为 `attachOnly` 模式，需先手动启动浏览器并打开目标页

---

## 四、CDP 端口冲突

### 现象

多个实例同时使用浏览器，或端口被占用。

### 处理

- 确保同一时间只有一个 Agent 使用浏览器
- 或为不同 Agent 配置不同 `browser.profile`

---

## 五、诊断命令

```bash
openclaw browser status
openclaw browser profiles
openclaw doctor
```

---

## 延伸阅读

- [Linux 浏览器问题排查](/tutorials/tools/browser-linux-troubleshooting)
- [Chrome 扩展](/tutorials/tools/chrome-extension)
- [浏览器工具](/tutorials/tools/browser)
