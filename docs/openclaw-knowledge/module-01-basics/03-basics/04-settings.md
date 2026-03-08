# 基础设置优化

> 个性化 + 权限控制

完成基础部署后，可通过配置优化模型选择、系统提示词、权限等，让 AI 更贴合你的使用场景。

---

## 一、模型选择

### 通过配置向导

```bash
openclaw configure --section model
```

### 手动配置示例

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-3-5-sonnet",
        fallback: "deepseek/deepseek-chat"
      }
    }
  }
}
```

### 常用模型 ID

| 提供商 | 模型 ID 示例 |
|--------|--------------|
| Anthropic | `anthropic/claude-3-5-sonnet` |
| OpenAI | `openai/gpt-4o` |
| DeepSeek | `deepseek/deepseek-chat` |
| 通义 | `qwen/qwen-max` |
| Ollama | `ollama/qwen2.5:7b` |

---

## 二、系统提示词（Personality）

通过 `AGENTS.md` 或配置中的 `systemPrompt` 定制 AI 人设与行为：

```markdown
# 角色
你是一个专业的办公助手，擅长写邮件、做总结、排日程。

# 风格
- 回复简洁，条理清晰
- 中文为主，必要时用英文术语
- 主动询问不明确的需求
```

配置文件方式：

```json5
{
  agents: {
    defaults: {
      systemPrompt: {
        content: "你是专业的办公助手..."
      }
    }
  }
}
```

---

## 三、权限与访问控制

### 配对审批

默认新用户需审批才能使用，可在配置中调整：

```json5
{
  pairing: {
    telegram: {
      autoApprove: true   // 自动批准（慎用，仅限私用）
    }
  }
}
```

### 黑名单 / 白名单

```json5
{
  channels: {
    telegram: {
      allowlist: ["user_id_1", "user_id_2"],
      blocklist: ["user_id_x"]
    }
  }
}
```

### 群组策略

部分通道支持仅响应 @ 提及、仅私聊等，见各通道文档。

---

## 四、技能（Skills）启用

Skills 可为 Agent 注入领域知识、工具、工作流：

```bash
openclaw configure --section skills
```

或在配置中指定：

```json5
{
  skills: {
    entries: [
      { id: "my-skill", path: "/path/to/skill" }
    ]
  }
}
```

---

## 五、工具与 Profile

不同场景可选用不同工具集：

```json5
{
  tools: {
    profile: "messaging"   // messaging | coding | full
  }
}
```

- **messaging**：轻量，适合纯聊天
- **coding**：含文件、终端等，适合开发
- **full**：全部工具，功能最全

---

## 六、常用配置入口

| 需求 | 命令或位置 |
|------|------------|
| 改模型 | `openclaw configure --section model` |
| 改系统提示词 | 编辑 `AGENTS.md` 或 config |
| 加技能 | `openclaw configure --section skills` |
| 改通道权限 | config 中 `channels.<name>.allowlist/blocklist` |
| 改工具集 | config 中 `tools.profile` |

---

## 七、配置生效方式

- 部分配置需 **重启 Gateway** 后生效
- 部分支持热重载，修改后自动生效
- 不确定时执行 `openclaw gateway restart` 即可

---

## 延伸阅读

- [系统提示词](/tutorials/concepts/system-prompt)
- [技能配置](/tutorials/tools/skills-config)
- [自定义斜杠命令](/tutorials/tools/slash-commands)
