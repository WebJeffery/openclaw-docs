# 核心指令入门

> 规范 + 示例

OpenClaw 支持 **斜杠命令**（Slash Commands）和 **自然语言** 两种指令方式。掌握常用命令可显著提升使用效率。

---

## 一、斜杠命令（Slash Commands）

在聊天窗口输入 `/` 会弹出可用命令列表。

### 内置常用命令

| 命令 | 说明 |
|------|------|
| `/think` | 触发深度思考，Agent 更仔细分析问题 |
| `/verbose` | 开启详细模式，显示工具调用、推理步骤 |
| `/reset` | 清除当前会话上下文，重新开始 |
| `/model <模型名>` | 切换当前会话的 AI 模型 |
| `/help` | 显示所有可用命令 |
| `/debug` | 切换调试模式 |
| `/stop` | 停止当前正在执行的操作 |
| `/summary` | 生成当前会话摘要 |
| `/elevated` | 临时提升 Agent 权限（执行高风险操作时） |

### 使用示例

```
/think
请分析一下这个需求文档的可行性

/verbose
帮我写一段 Python 代码

/reset
（清空上下文后重新开始）

/model deepseek/deepseek-chat
（切换到 DeepSeek 模型）
```

---

## 二、自定义斜杠命令

可在配置中定义自己的命令：

```json5
{
  slashCommands: {
    "/日报": {
      description: "生成今日工作日报",
      prompt: "根据今天的对话，生成一份简洁的日报，包括：完成了什么、遇到的问题、下一步计划"
    },
    "/总结": {
      description: "总结对话要点",
      prompt: "请总结到目前为止的对话内容，包括主要讨论的话题、做出的决策和待办事项"
    },
    "/reset": {
      description: "重置会话",
      action: "clearContext"
    }
  }
}
```

### 配置字段说明

| 字段 | 说明 |
|------|------|
| `description` | 命令描述，显示在列表中 |
| `prompt` | 触发时发送给 Agent 的提示词 |
| `action` | 内置动作，如 `clearContext`、`toggleVerbose` |

---

## 三、自然语言指令

除斜杠命令外，直接发自然语言即可，Agent 会理解并执行，例如：

- 「帮我写一封邮件，主题是项目进度汇报」
- 「总结一下我们刚才讨论的要点」
- 「用 Python 写一个读取 CSV 的脚本」

配合 `/think`、`/verbose` 可控制思考深度和输出详细程度。

---

## 四、指令规范建议

1. **任务清晰**：尽量写清背景、约束、期望输出格式
2. **分步执行**：复杂任务可拆成多轮对话
3. **善用 /reset**：话题切换或上下文混乱时重置会话
4. **按需 /verbose**：排查推理问题时开启，日常可关闭以节省 Token

---

## 五、模型切换

```bash
/model anthropic/claude-3-5-sonnet
/model deepseek/deepseek-chat
/model ollama/qwen2.5:7b
```

不同模型适合不同场景：简单问答用便宜模型，复杂推理用强模型。

---

## 延伸阅读

- [斜杠命令文档](/tutorials/tools/slash-commands)
- [基础设置优化](/openclaw-knowledge/module-01-basics/03-basics/04-settings)
- [模型切换技巧](/openclaw-knowledge/module-03-advanced/02-models/02-switch)
