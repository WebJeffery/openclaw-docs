# Windows 安装坑

> Git / Node / 乱码问题

Windows 上安装 OpenClaw 常见问题与解决方案。

---

## 一、推荐：使用 WSL2

OpenClaw 在 Linux 环境下最稳定，**强烈建议**在 Windows 上通过 WSL2 安装。

### 启用 WSL2

```powershell
# 以管理员身份运行 PowerShell
wsl --install
# 重启后按提示完成 Ubuntu 安装
```

### 在 WSL2 中安装

```bash
# 进入 WSL
wsl

# 安装 Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 安装 OpenClaw
sudo npm install -g openclaw@latest
openclaw onboard
```

---

## 二、原生 Windows 安装

若必须使用原生 Windows：

### Node.js 版本

- 需 **Node.js 18+**，推荐 20.x LTS
- 从 [nodejs.org](https://nodejs.org) 下载 LTS 安装包
- 安装时勾选「Add to PATH」

### Git 问题

- 部分操作依赖 Git，需安装 [Git for Windows](https://git-scm.com/download/win)
- 若 `npm install` 报 Git 相关错误，检查 Git 是否在 PATH 中

### 路径与编码

- 路径避免中文、空格，如 `C:\openclaw` 优于 `C:\用户\xxx`
- 若终端乱码，设置：`chcp 65001`（UTF-8）
- PowerShell 执行策略：若脚本被拦截，`Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`

---

## 三、常见错误

| 错误 | 处理 |
|------|------|
| `node 不是内部或外部命令` | 重装 Node.js，勾选 PATH；或重启终端 |
| `npm ERR! code ENOENT` | 检查路径、权限，避免中文目录 |
| `tsx __name is not a function` | 见 [Node.js 问题排查](/tutorials/help/node-issue) |
| 乱码 | `chcp 65001`，或改用 WSL2 |

---

## 四、验证安装

```bash
node --version   # 应 >= 18
npm --version
openclaw --version
```

---

## 延伸阅读

- [Node.js 问题排查](/tutorials/help/node-issue)
- [部署前准备](/openclaw-knowledge/module-01-basics/02-deploy/01-prepare)
- [启动坑](/openclaw-knowledge/module-04-troubleshooting/01-deploy/02-startup)
