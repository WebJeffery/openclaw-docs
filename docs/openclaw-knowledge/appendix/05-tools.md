# 工具推荐

> 配套工具清单（付费）

部署、监控、开发等场景的配套工具推荐。

---

## 一、部署与运行

| 工具 | 用途 | 说明 |
|------|------|------|
| **Node.js** | 运行环境 | 需 v18+，推荐 v22，[nodejs.org](https://nodejs.org) |
| **npm / pnpm** | 包管理 | OpenClaw 通过 npm 安装 |
| **Docker** | 容器化部署 | 可选，[Docker 安装](/tutorials/installation/docker) |
| **Ollama** | 本地模型 | 离线/隐私场景，[ollama.com](https://ollama.com) |
| **Tailscale** | 内网穿透 | 远程访问 Gateway，[tailscale.com](https://tailscale.com) |

---

## 二、开发与调试

| 工具 | 用途 | 说明 |
|------|------|------|
| **VS Code / Cursor** | 编辑器 | 编辑配置、技能、脚本 |
| **jq** | JSON 处理 | `openclaw logs --json \| jq` 分析日志 |
| **curl** | API 测试 | 测试 Webhook、健康检查等 |
| **Chrome DevTools** | 浏览器调试 | Browser 工具、扩展调试 |

---

## 三、监控与运维

| 工具 | 用途 | 说明 |
|------|------|------|
| **openclaw doctor** | 健康检查 | 内置，首选 |
| **openclaw logs** | 日志查看 | 内置，支持过滤与实时跟踪 |
| **systemd / launchd** | 进程管理 | Linux/macOS 守护进程 |
| **htop / top** | 资源监控 | CPU、内存占用 |
| **Grafana + Prometheus** | 可选 | 企业级监控（需自建导出） |

---

## 四、云与服务器

| 工具 | 用途 | 说明 |
|------|------|------|
| **阿里云轻量** | 云部署 | 低成本，适合个人，见 [云部署](/openclaw-knowledge/module-01-basics/02-deploy/02-cloud) |
| **Hetzner / GCP** | 海外云 | 低延迟访问海外 API |
| **Cloudflare Tunnel** | 内网穿透 | 免公网 IP 暴露服务 |

---

## 五、API 与成本

| 工具 | 用途 | 说明 |
|------|------|------|
| **OpenRouter** | 多模型统一 | 一个 Key 访问多家模型 |
| **Million Engine 等中转** | 成本优化 | 可降低 30–70% 成本，支持人民币 |
| **OpenClaw 内置** | 会话/消息级成本追踪 | `openclaw` 命令与 Dashboard |

---

## 六、通道相关

| 工具 | 用途 | 说明 |
|------|------|------|
| **Chrome / Chromium** | Browser 工具 | Linux 需安装，见 [浏览器坑](/openclaw-knowledge/module-04-troubleshooting/02-ops/02-browser) |
| **OpenClaw Chrome 扩展** | 浏览器中继 | 控制网页、填写表单等 |
| **signal-cli** | Signal 通道 | 若使用 Signal，需配置 |

---

## 七、选型建议

- **个人入门**：Node + npm + Ollama（可选）
- **7×24 运行**：云服务器 + systemd/launchd
- **远程访问**：Tailscale 或 Cloudflare Tunnel
- **成本敏感**：Ollama 本地 + API 中转

---

## 延伸阅读

- [零门槛部署](/openclaw-knowledge/module-01-basics/02-deploy/)
- [本地部署](/openclaw-knowledge/module-01-basics/02-deploy/03-local)
- [云部署](/openclaw-knowledge/module-01-basics/02-deploy/02-cloud)
