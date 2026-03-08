# 文档自动化

> Excel 处理 / 排版 / 纪要生成

通过 OpenClaw 的 Agent + 工具（文件读写、exec）实现文档自动化：Excel 处理、排版、会议纪要生成等。

---

## 一、能力基础

OpenClaw Agent 具备：

- **文件读写**：`read_file`、`write_file` 等工具
- **执行命令**：`exec` 可调用 Python、Node 等处理 CSV/Excel
- **自然语言理解**：根据描述生成、修改文档内容

---

## 二、Excel 处理

### 方式一：Python 脚本 + exec

Agent 通过 exec 调用 Python 处理 Excel：

```python
# 示例：汇总多个 sheet
import pandas as pd
import sys

path = sys.argv[1]
df = pd.read_excel(path, sheet_name=None)
summary = {name: len(df) for name, df in df.items()}
print(summary)
```

配置 exec 白名单包含 `python3` 后，Agent 可执行：

```
帮我分析这个 Excel 文件 /path/to/data.xlsx 的各 sheet 行数，并汇总
```

### 方式二：CSV 简化处理

若数据为 CSV，Agent 可直接读取并分析，无需额外脚本。

---

## 三、会议纪要生成

### 斜杠命令

```json5
{
  slashCommands: {
    "/纪要": {
      description: "生成会议纪要",
      prompt: "根据以下会议记录/录音转写内容，生成结构化会议纪要，包含：时间、参会人、议题、结论、待办事项。"
    }
  }
}
```

使用：在飞书/Telegram 中发送会议记录，然后输入 `/纪要` 或直接说「根据上面的内容生成会议纪要」。

### Skill 注入格式规范

```markdown
# ~/.openclaw/skills/meeting-minutes/SKILL.md

# 会议纪要技能

## 指令
纪要格式：
## 会议纪要
- **时间**：YYYY-MM-DD HH:mm
- **参会人**：...
- **议题**：...
- **结论**：...
- **待办**：| 责任人 | 事项 | 截止时间 |
```

---

## 四、排版与格式化

Agent 可对 Markdown、纯文本进行排版：

- 统一标题层级
- 添加目录
- 调整段落与列表格式
- 中英文混排时的空格规范

示例指令：「把下面这段文字整理成规范的 Markdown，加上标题和目录」。

---

## 五、配置要点

```json5
{
  tools: {
    exec: {
      enabled: true,
      allowedBinaries: ["python3", "pandas"]  // 按需添加
    }
  },
  tools: {
    profile: "coding"  // 启用文件、exec 等工具
  }
}
```

---

## 六、安全注意

- exec 白名单尽量收紧，只放必要的命令
- 避免让 Agent 处理含敏感信息的文件路径
- 重要文档建议人工复核后再对外发布

---

## 延伸阅读

- [执行工具](/tutorials/tools/exec)
- [创建自定义技能](/tutorials/tools/creating-skills)
- [斜杠命令](/tutorials/tools/slash-commands)
