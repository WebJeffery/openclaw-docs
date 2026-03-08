# 云端部署

> 职业交易者首选，一键脚本实操

云端部署适合需要 7×24 运行、多端访问、或本地网络不稳定的场景。OpenClaw 官方支持 **Fly.io**，也可在自有 VPS 上通过 Docker 或直接安装运行。

---

## 一、Fly.io 部署（推荐）

### 前置条件

- [flyctl CLI](https://fly.io/docs/hands-on/install-flyctl/) 已安装
- Fly.io 账号（免费套餐可用）
- AI API 密钥、通道凭证（如 Discord/Telegram Token）

### 快速步骤

```bash
# 1. 克隆仓库
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# 2. 创建应用与卷
fly apps create my-openclaw
fly volumes create openclaw_data --size 1 --region iad

# 3. 配置 fly.toml（编辑 app 名、区域等）
# 4. 设置密钥
fly secrets set ANTHROPIC_API_KEY="sk-ant-xxx"
fly secrets set OPENCLAW_STATE_DIR="/data"

# 5. 部署
fly deploy
```

### 关键配置示例

```toml
[env]
  NODE_ENV = "production"
  OPENCLAW_STATE_DIR = "/data"
  NODE_OPTIONS = "--max-old-space-size=1536"

[processes]
  app = "node dist/index.js gateway --allow-unconfigured --port 3000 --bind lan"

[[vm]]
  size = "shared-cpu-2x"
```

### 私有加固部署

若不想暴露公网 IP，可参考 Fly.io 文档配置 `fly.private.toml`，或使用 Tailscale 等实现私有访问。

---

## 二、自有 VPS 部署

### 方式 A：直接安装

```bash
# 安装 Node.js 20（以 Ubuntu 为例）
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 安装 OpenClaw
sudo npm install -g openclaw@latest

# 向导配置
openclaw onboard

# 安装为系统服务，开机自启
openclaw gateway install
systemctl --user enable --now openclaw-gateway.service
```

### 方式 B：Docker 部署

```bash
# 拉取镜像
docker pull ghcr.io/openclaw/openclaw:latest

# 运行（需挂载配置目录）
docker run -d \
  --name openclaw \
  -v openclaw_data:/root/.openclaw \
  -p 18789:18789 \
  -e ANTHROPIC_API_KEY="sk-ant-xxx" \
  ghcr.io/openclaw/openclaw:latest \
  gateway --port 18789
```

### 多阶段构建（2026 新特性）

Dockerfile 支持 `OPENCLAW_VARIANT=slim` 构建更小镜像：

```dockerfile
ARG OPENCLAW_VARIANT=slim
# 构建时传入
```

---

## 三、密钥与安全

### 环境变量

```bash
export ANTHROPIC_API_KEY="sk-ant-xxx"
# 或
export DEEPSEEK_API_KEY="sk-xxx"
```

### Fly.io Secrets

```bash
fly secrets set ANTHROPIC_API_KEY="sk-ant-xxx"
fly secrets set TELEGRAM_BOT_TOKEN="xxx"
```

### 远程访问鉴权

若从外网访问 Gateway，务必配置 `gateway.auth`：

```json5
{
  gateway: {
    auth: {
      mode: "token",
      token: "your-secure-token"
    }
  }
}
```

---

## 四、持久化与备份

- **Fly.io**：使用 `fly volumes` 挂载到 `/data`，`OPENCLAW_STATE_DIR=/data`
- **VPS**：数据默认在 `~/.openclaw`，建议定期备份 `config`、`sessions` 目录
- **Docker**：通过 `-v` 挂载宿主机目录，避免容器删除导致数据丢失

---

## 五、常用运维命令

```bash
# 查看应用状态（Fly.io）
fly status

# SSH 进入容器（Fly.io）
fly ssh console

# 查看日志
openclaw logs --follow

# 诊断
openclaw doctor
```

---

## 延伸阅读

- [Fly.io 部署教程](/tutorials/installation/fly) — 完整步骤
- [部署后验证](/openclaw-knowledge/module-01-basics/02-deploy/04-verify) — 上线前检查
- [认证与安全](/tutorials/gateway/authentication) — 远程访问配置
