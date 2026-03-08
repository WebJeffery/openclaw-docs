# 本地部署

> 隐私优先，离线版 Ollama 部署

本地部署适合对隐私要求高、不想依赖外部 API、或网络不稳定的用户。配合 **Ollama** 可完全离线运行，数据不出本机。

---

## 一、为什么选本地部署？

- **隐私**：对话、文件、上下文全部在本地
- **成本**：无 API 调用费用（需承担电费与硬件）
- **离线**：断网也能用
- **合规**：适合金融、医疗等敏感场景

---

## 二、安装 Ollama

### macOS

```bash
# 官网下载安装包
# https://ollama.com/download

# 或 Homebrew
brew install ollama
```

### Linux

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### Windows

从 [ollama.com](https://ollama.com) 下载安装程序。

---

## 三、拉取模型

```bash
# 推荐中文场景（7B 级别，8GB 显存可跑）
ollama pull qwen2.5:7b

# 更小显存（3B 级别）
ollama pull qwen2.5:3b

# 更强推理（需 16GB+ 显存）
ollama pull qwen2.5:14b
ollama pull deepseek-r1:7b
```

### 常用模型对比

| 模型 | 显存需求 | 特点 |
|------|----------|------|
| qwen2.5:3b | 约 4GB | 轻量、响应快 |
| qwen2.5:7b | 约 8GB | 平衡 |
| qwen2.5:14b | 约 16GB | 质量更好 |
| deepseek-r1:7b | 约 8GB | 推理能力强 |

---

## 四、配置 OpenClaw 使用 Ollama

### 向导方式

```bash
openclaw onboard --auth-choice ollama
```

### 手动配置

在 `config.json` 或通过 `openclaw configure` 中设置：

```json5
{
  env: {
    OLLAMA_BASE_URL: "http://127.0.0.1:11434"  // 默认
  },
  agents: {
    defaults: {
      model: {
        primary: "ollama/qwen2.5:7b"
      }
    }
  }
}
```

---

## 五、启动顺序

1. **先启动 Ollama**（通常安装后会自动运行）

```bash
ollama serve   # 若未自动启动
ollama list    # 确认模型已拉取
```

2. **再启动 OpenClaw Gateway**

```bash
openclaw gateway
```

---

## 六、混合模式：本地 + 云端

可配置主模型为 Ollama，备用模型为 API（如 DeepSeek），实现：

- 日常用本地，省成本
- 复杂任务或本地超时时自动切云端

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "ollama/qwen2.5:7b",
        fallback: "deepseek/deepseek-chat"
      }
    }
  }
}
```

---

## 七、性能优化

- **显存不足**：换更小模型（如 3B）或使用 ` Ollama` 的 CPU 模式（较慢）
- **响应慢**：优先用 7B 及以下，或考虑量化版本（如 `qwen2.5:7b-q4_0`）
- **多用户**：Ollama 默认单机，多用户并发会排队

---

## 八、数据与备份

- 配置与会话：`~/.openclaw`
- Ollama 模型：`~/.ollama/models`（Linux/macOS）
- 定期备份上述目录即可

---

## 延伸阅读

- [Ollama Provider 文档](/tutorials/providers/) — 更多模型与配置
- [模型切换技巧](/openclaw-knowledge/module-03-advanced/02-models/02-switch) — 降本增效
- [本地模型部署坑](/openclaw-knowledge/module-04-troubleshooting/03-advanced/02-ollama) — 常见问题
