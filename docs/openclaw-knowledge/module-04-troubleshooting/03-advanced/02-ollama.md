# 本地模型部署坑

> 离线 / 数据导入 / 显存

Ollama 作为本地模型运行时，常见问题与解决方案。

---

## 一、连接被拒绝

### 现象

```
ECONNREFUSED 127.0.0.1:11434
Connection refused
```

### 处理

```bash
# 确认 Ollama 是否在运行
ps aux | grep ollama

# 启动 Ollama
ollama serve

# 验证 API 可访问
curl http://localhost:11434/api/tags
```

---

## 二、模型未被发现

### 现象

`openclaw models list` 中无 Ollama 模型。

### 原因

- 未设置 `OLLAMA_API_KEY`（任意值即可）
- 显式配置了 `models.providers.ollama` 会禁用自动发现
- 模型不支持工具调用，自动发现会过滤掉

### 处理

```bash
export OLLAMA_API_KEY="ollama-local"
# 或在配置中：openclaw config set models.providers.ollama.apiKey "ollama-local"

ollama list
ollama pull qwen2.5:7b   # 支持工具调用的模型
openclaw models list
```

若需使用不支持工具的模型，在 `models.providers.ollama.models` 中显式定义。

---

## 三、显存不足

### 现象

OOM、CUDA out of memory、推理中断。

### 处理

- 换更小模型：`qwen2.5:3b`、`llama3.2:3b`
- 使用量化版：`qwen2.5:7b-q4_0`
- 关闭其他占显存程序
- 若为 CPU 模式，增加 `NODE_OPTIONS="--max-old-space-size=4096"`

---

## 四、响应慢

### 处理

- 优先 7B 及以下模型
- 确保 Ollama 使用 GPU：`ollama ps` 查看
- 增加 Node 内存：`NODE_OPTIONS="--max-old-space-size=4096"`
- 多用户并发时，Ollama 默认串行，可部署多实例或分流到 API 模型

---

## 五、完全离线部署

1. 在有网络的机器上 `ollama pull` 所需模型
2. 将 `~/.ollama/models` 拷贝到离线环境
3. 离线环境安装 Ollama，模型目录放到对应位置
4. OpenClaw 配置 `OLLAMA_BASE_URL` 指向本地 Ollama

---

## 六、输出乱码或中文差

- 检查模型是否支持中文，推荐 qwen、deepseek 等
- 可配置 `fallbacks` 到云端模型做兜底

---

## 延伸阅读

- [本地模型深度部署](/openclaw-knowledge/module-03-advanced/02-models/03-ollama)
- [Ollama Provider](/tutorials/providers/ollama)
- [本地部署](/openclaw-knowledge/module-01-basics/02-deploy/03-local)
