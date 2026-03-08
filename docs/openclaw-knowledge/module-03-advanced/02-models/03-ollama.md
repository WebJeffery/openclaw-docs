# 本地模型深度部署

> Ollama 优化

Ollama 是运行本地大模型的主流方式。合理配置可提升响应速度、降低资源占用。

---

## 一、Ollama 安装与模型拉取

### 安装

- **macOS**：`brew install ollama` 或从 [ollama.com](https://ollama.com) 下载
- **Linux**：`curl -fsSL https://ollama.com/install.sh | sh`

### 拉取模型

```bash
ollama pull qwen2.5:7b      # 平衡之选
ollama pull qwen2.5:3b      # 轻量
ollama pull deepseek-r1:7b  # 推理强
ollama pull llama3.2:3b     # 英文场景
```

---

## 二、OpenClaw 配置

```json5
{
  env: {
    OLLAMA_BASE_URL: "http://127.0.0.1:11434"
  },
  agents: {
    defaults: {
      model: {
        primary: "ollama/qwen2.5:7b",
        fallbacks: ["ollama/qwen2.5:3b"]
      }
    }
  }
}
```

---

## 三、性能优化

### 显存不足

- 换更小模型（3B、1.5B）
- 使用量化版：`qwen2.5:7b-q4_0`
- 关闭其他占显存程序

### 响应慢

- 优先 7B 及以下
- 确保 Ollama 使用 GPU（`ollama ps` 查看）
- 增加 `NODE_OPTIONS="--max-old-space-size=4096"` 等

### 多用户并发

Ollama 默认单机串行，多请求会排队。可：

- 部署多个 Ollama 实例（不同端口）
- 或用 API 模型做分流

---

## 四、完全离线部署

1. 提前在有网络的机器上 `ollama pull` 所需模型
2. 将 `~/.ollama/models` 拷贝到离线环境
3. 离线环境安装 Ollama，模型目录放到对应位置
4. OpenClaw 配置 `OLLAMA_BASE_URL` 指向本地 Ollama

---

## 五、混合模式：本地 + 云端

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "ollama/qwen2.5:7b",
        fallbacks: ["deepseek/deepseek-chat"]
      }
    }
  }
}
```

日常用本地，复杂任务或超时时自动切云端。

---

## 六、常见问题

| 问题 | 处理 |
|------|------|
| 连接 refused | 确认 `ollama serve` 已启动 |
| 模型不存在 | `ollama list` 检查，缺则 `ollama pull` |
| 超时 | 增大 `agents.defaults.timeoutSeconds` |
| 输出乱码 | 检查模型是否支持中文，或换 qwen 等 |

---

## 延伸阅读

- [本地部署](/openclaw-knowledge/module-01-basics/02-deploy/03-local)
- [Ollama Provider 文档](/tutorials/providers/)
- [本地模型部署坑](/openclaw-knowledge/module-04-troubleshooting/03-advanced/02-ollama)
