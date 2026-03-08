# 部署前准备

> 硬件 / 凭证 / 工具

部署 OpenClaw 前，准备好以下内容可大幅减少踩坑。

---

## 一、硬件与环境

### 最低要求

| 项目 | 要求 |
|------|------|
| **操作系统** | macOS、Linux、Windows（建议 WSL2） |
| **Node.js** | 18.x 或更高（推荐 20.x LTS） |
| **内存** | 建议 2GB+，本地跑 Ollama 需 8GB+ |
| **磁盘** | 约 500MB（不含模型与媒体文件） |

### 云端部署额外考虑

- **Fly.io**：免费套餐可用，建议 `shared-cpu-2x` 及以上
- **自有 VPS**：1 核 1G 可跑，2 核 2G 更稳
- **持久化**：需挂载卷或对象存储存放配置、会话、媒体

---

## 二、AI 凭证（API Key）

OpenClaw 需要至少一个 AI 提供商的 API 密钥（使用 Ollama 本地模型可跳过）。

### 推荐选择

| 提供商 | 适用场景 | 获取方式 |
|--------|----------|----------|
| **Anthropic（Claude）** | 综合质量优先 | [console.anthropic.com](https://console.anthropic.com) → API Keys |
| **DeepSeek** | 国内用户、性价比 | [platform.deepseek.com](https://platform.deepseek.com) → API Keys |
| **OpenAI（GPT）** | 生态成熟 | [platform.openai.com](https://platform.openai.com) → API Keys |
| **通义千问** | 国内、合规 | [bailian.console.aliyun.com](https://bailian.console.aliyun.com) |
| **Google Gemini** | 免费额度试用 | [aistudio.google.com](https://aistudio.google.com) |

### 注意事项

- 密钥只显示一次，创建后立即复制保存
- 建议用环境变量或 SecretRef 管理，不要写进配置文件提交到 Git

---

## 三、通道凭证（按需准备）

根据要接入的聊天平台准备对应凭证：

| 通道 | 所需凭证 | 获取方式 |
|------|----------|----------|
| **Telegram** | Bot Token | 找 @BotFather 发送 `/newbot` |
| **Discord** | Bot Token | Discord Developer Portal → Application → Bot |
| **飞书** | App ID + App Secret | 飞书开放平台创建应用 |
| **WhatsApp** | 需 Meta 开发者账号 | 使用官方 Cloud API 或第三方桥接 |
| **Slack** | Bot Token / App 凭证 | Slack API 创建应用 |

新手建议先接 **Telegram**，配置最简单。

---

## 四、必要工具

### 命令行（跨平台安装）

```bash
# 安装 OpenClaw（全局）
npm install -g openclaw@latest

# 验证安装
openclaw --version
```

### macOS 用户（图形安装）

- 从 [GitHub Releases](https://github.com/openclaw/openclaw/releases) 下载 `.dmg`
- 双击安装，按向导完成配置

### Windows 用户

- 建议使用 **WSL2**（Ubuntu）
- 或在 Windows 上直接安装 Node.js 后使用 `npm install -g openclaw`

### 云端部署

- **Fly.io**：安装 [flyctl](https://fly.io/docs/hands-on/install-flyctl/)
- **Docker**：需 Docker 或 Podman
- **自有 VPS**：SSH 客户端、可选 tmux/screen 做后台运行

---

## 五、网络与访问

- **国内用户**：Anthropic、OpenAI 需代理；DeepSeek、通义可直连
- **远程访问**：若从外网访问家中/公司部署，可配合 Tailscale、frp 等内网穿透

---

## 六、检查清单

部署前可逐项确认：

- [ ] Node.js 18+ 已安装
- [ ] 至少一个 AI API Key 已获取
- [ ] 若接 Telegram，已从 @BotFather 获取 Bot Token
- [ ] 若云端部署，已准备 Fly.io 账号或 VPS
- [ ] 若本地部署，端口 18789 未被占用

---

## 延伸阅读

- [快速开始](/tutorials/getting-started/getting-started) — 官方安装向导
- [云端部署](/openclaw-knowledge/module-01-basics/02-deploy/02-cloud) — Fly.io 等云部署
- [本地部署](/openclaw-knowledge/module-01-basics/02-deploy/03-local) — Ollama 离线方案
