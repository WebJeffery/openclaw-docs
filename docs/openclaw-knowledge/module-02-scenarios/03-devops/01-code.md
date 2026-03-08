# 代码辅助

> 生成 / 排错 / 日志分析

OpenClaw 的 coding profile 提供代码生成、排错、日志分析等能力，适合开发者日常使用。

---

## 一、工具配置

启用完整开发工具集：

```json5
{
  tools: {
    profile: "coding"  // 包含 read_file, write_file, exec, run_terminal_cmd 等
  }
}
```

或按需启用 exec：

```json5
{
  tools: {
    exec: {
      enabled: true,
      allowedBinaries: ["git", "npm", "python3", "node", "ls", "cat", "grep"]
    }
  }
}
```

---

## 二、代码生成

### 使用方式

在 Telegram/Discord 中直接描述需求，例如：

- 「用 Python 写一个读取 CSV 并计算均值的函数」
- 「给这段代码加单元测试」
- 「用 TypeScript 实现一个简单的 LRU 缓存」

Agent 可生成代码并写入文件，或直接返回代码块供你复制。

### Skill 注入规范

```markdown
# ~/.openclaw/skills/code-gen/SKILL.md

# 代码生成技能

## 指令
生成代码时：
1. 优先使用类型注解和清晰命名
2. 包含必要的错误处理
3. 添加简短注释说明关键逻辑
4. 若用户未指定语言，根据场景选择合适语言
```

---

## 三、排错与调试

### 常见场景

- 「这段代码报错 xxx，帮我排查」
- 「为什么这个函数返回 undefined」
- 「分析这个 stack trace」

将错误信息、相关代码粘贴到对话中，Agent 可分析并给出修复建议。

### 执行与验证

若配置了 exec，Agent 可尝试运行代码验证修复是否有效。建议对生产目录使用审批模式，避免误执行。

---

## 四、日志分析

### 方式一：直接粘贴

将日志内容粘贴到对话，让 Agent 分析：

- 错误原因
- 异常模式
- 排查建议

### 方式二：文件读取

若日志在文件中：

```
分析 /var/log/app/error.log 中的最近 100 行，找出主要错误类型
```

Agent 通过 `read_file` 读取并分析。

### 方式三：exec 调用

```
执行 tail -n 500 /var/log/app.log，分析其中的 ERROR 和 WARN
```

需在 exec 白名单中允许 `tail`、`grep` 等命令。

---

## 五、斜杠命令示例

```json5
{
  slashCommands: {
    "/review": {
      description: "代码审查",
      prompt: "对最近一次 git diff 或用户指定的代码进行审查，关注安全、可维护性、性能"
    },
    "/test": {
      description: "生成测试",
      prompt: "为上述代码生成单元测试，覆盖主要分支和边界情况"
    }
  }
}
```

---

## 六、安全注意

- exec 白名单尽量收紧，避免 `rm -rf` 等危险命令
- 敏感项目建议使用 sandbox 或独立工作区
- 代码中若含密钥、密码，避免让 Agent 处理

---

## 延伸阅读

- [执行工具](/tutorials/tools/exec)
- [创建自定义技能](/tutorials/tools/creating-skills)
- [工具 Profile](/tutorials/tools/)
