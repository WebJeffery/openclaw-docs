# 记忆断片

> Memory 配置优化

Agent 记不住之前对话、上下文丢失等问题的排查与优化。

---

## 一、会话与上下文

### 基本概念

- **Session（会话）**：按通道 + 用户/群组区分，同一会话内共享历史
- **上下文窗口**：模型有 Token 上限，超出会触发压缩（Compaction）或裁剪
- **压缩**：自动裁剪旧消息，保留最近对话与关键信息

---

## 二、常见原因

### 1. 会话被重置

- 用户发送 `/reset` 或 `/new` 会清空当前会话
- 检查是否误触或配置了自动重置

### 2. 上下文过长触发压缩

- 长对话会触发 Compaction，旧内容被摘要或丢弃
- 可配置 `agents.defaults.compaction` 调整策略

### 3. 不同会话隔离

- 私聊与群聊是不同会话，历史不共享
- 切换通道或群组会进入新会话

### 4. Memory 插件未正确配置

- 若使用 QMD、Memory 等插件，需正确配置并启用
- 检查 `plugins` 与 `memory` 相关配置

---

## 三、配置优化

### 压缩与保留

```json5
{
  agents: {
    defaults: {
      compaction: {
        recentTurnsPreserve: 10,  // 保留最近 N 轮
        postCompactionSections: ["capabilities", "constraints"]
      }
    }
  }
}
```

### 上下文长度

```json5
{
  agents: {
    defaults: {
      contextTokens: 100000  // 上限，与模型匹配
    }
  }
}
```

### 会话修剪（Session Pruning）

针对 Anthropic 的 cache-ttl 优化，可减少 TTL 过期后的成本，详见 [会话修剪](/tutorials/concepts/session-pruning)。

---

## 四、Memory 插件

若使用外部 Memory 插件（如 QMD）：

- 确认插件已启用且无冲突（memory slot 仅能有一个）
- 检查插件所需权限与存储路径
- 查看 `openclaw plugins diagnose` 输出

---

## 五、排查命令

```bash
openclaw config show | grep -A 10 compaction
openclaw plugins list
openclaw doctor
```

---

## 延伸阅读

- [会话修剪](/tutorials/concepts/session-pruning)
- [压缩配置](/tutorials/concepts/compaction)
- [Memory 概念](/tutorials/concepts/memory)
