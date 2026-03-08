# 多终端适配

> 手机端配对与权限设置

OpenClaw 支持在手机、平板、电脑等多终端使用同一套 AI 助手，核心是 **Gateway 常驻 + 通道多端登录**。

---

## 一、多终端如何工作

- **Gateway**：运行在某一台机器（本机或云服务器）
- **通道**：Telegram、飞书等本身支持多端登录
- **会话**：按「通道 + 用户/群组」区分，同一会话在多端同步

因此，只要 Gateway 在线、通道已配置，在任意设备的 Telegram/飞书 里都能和同一个 AI 对话。

---

## 二、手机端使用

### 方式一：Gateway 在云上（推荐）

Gateway 部署在 Fly.io、VPS 等，手机通过 4G/5G/WiFi 连接：

- 手机打开 Telegram/飞书，直接与 Bot 对话
- 无需手机常驻后台，体验与电脑一致

### 方式二：Gateway 在家庭网络

- 手机和电脑在同一 WiFi 下，可访问内网 Gateway
- 外出时需内网穿透（如 Tailscale）才能从手机访问

### 方式三：iOS App（2026 支持）

- 安装 OpenClaw iOS App（`ai.openclaw.client`）
- 在 App 内配置并连接远程 Gateway
- 支持 Watch 端

---

## 三、配对与权限

### 首次配对

每个新用户/新群组首次发消息时，需在 **运行 Gateway 的机器** 上审批：

```bash
openclaw pairing list telegram
openclaw pairing approve telegram <CODE>
```

若 Gateway 在云上，通过 SSH 登录执行上述命令。

### 黑名单与白名单

可在配置中限制哪些用户/群组可使用：

```json5
{
  channels: {
    telegram: {
      allowlist: ["user_id_1", "user_id_2"],   // 仅允许
      blocklist: ["user_id_x"]                 // 或禁止
    }
  }
}
```

---

## 四、后台运行与保活

### 手机作为 Gateway（不推荐）

手机做 Gateway 需常驻后台，易被系统杀进程，一般不推荐。

### 推荐架构

```
手机/平板/电脑（客户端）
        ↓
  Telegram / 飞书 / Discord（通道）
        ↓
  Gateway（云服务器或家中电脑）
        ↓
  AI 模型（API 或 Ollama）
```

---

## 五、远程访问家中 Gateway

### Tailscale

1. 在运行 Gateway 的电脑上安装 Tailscale
2. 在手机上也安装 Tailscale，加入同一 Tailscale 网络
3. 配置 OpenClaw 使用 Tailscale 内网地址
4. 手机通过 Tailscale 网络访问家中 Gateway

详见 [认证与安全](/tutorials/gateway/authentication)。

---

## 六、常见问题

| 问题 | 处理 |
|------|------|
| 手机收不到回复 | 检查 Gateway 是否在线、通道是否正常、是否已配对 |
| 外出无法访问 | 将 Gateway 部署到云端，或配置 Tailscale 等穿透 |
| 多设备会话不同步 | 会话按通道+用户区分，同一 Telegram 账号多端本身同步 |

---

## 延伸阅读

- [部署后验证](/openclaw-knowledge/module-01-basics/02-deploy/04-verify)
- [消息不回](/openclaw-knowledge/module-04-troubleshooting/02-ops/01-no-reply)
- [手机节点坑](/openclaw-knowledge/module-04-troubleshooting/02-ops/03-mobile)
