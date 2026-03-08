# 自定义技能开发

> 零代码 / 低代码

技能以 Markdown 文件形式存在，无需编程即可创建。掌握基本结构即可快速开发自定义技能。

---

## 一、最小技能结构

```bash
mkdir -p ~/.openclaw/skills/my-skill
```

创建 `~/.openclaw/skills/my-skill/SKILL.md`：

```markdown
# 我的技能名称

## 描述
一句话说明技能用途。

## 使用方法
当用户说/做...时启用此技能。

## 指令
（注入给 Agent 的具体指令）
1. 第一步...
2. 第二步...
3. 输出格式...
```

重启 Gateway 或使用 `/reload-skills` 后生效。

---

## 二、完整 SKILL.md 模板

```markdown
# 技能名称

## 描述
简洁描述用途（1–3 句）。

## 使用方法
在以下场景启用：
- 用户请求 xxx 时
- 用户提到「关键词1」「关键词2」时

## 指令

（以下是注入给 Agent 的实际内容）

你是 xxx 专家。当处理相关请求时：

1. **步骤一**：...
2. **步骤二**：...
3. **输出格式**：...

注意事项：
- 避免...
- 务必...
```

---

## 三、进阶能力

### Gate 规则（按条件激活）

在配置中为技能设置 Gate，减少无效加载：

```json5
{
  skills: {
    "my-skill": {
      enabled: true,
      gate: {
        keywords: ["关键词1", "关键词2"],
        channels: ["telegram", "feishu"]
      }
    }
  }
}
```

### 按 Agent 分配

```json5
{
  skills: {
    "my-skill": {
      enabled: true,
      agents: ["agent-a", "agent-b"]
    }
  }
}
```

### 配置覆盖

在 SKILL.md 顶部用 YAML frontmatter 覆盖配置：

```yaml
---
config:
  model: "claude-opus-4-6"
  maxTokens: 8192
---

# 技能正文...
```

### 环境变量注入

```json5
{
  skills: {
    "api-helper": {
      env: {
        API_BASE: "${MY_API_BASE}",
        API_KEY: "${MY_API_KEY}"
      }
    }
  }
}
```

---

## 四、工作区技能（项目级）

仅对某项目生效的技能，放在项目目录下：

```bash
mkdir -p /your-project/.openclaw/skills/project-skill
```

工作区技能优先级高于全局技能，可覆盖同名技能。

---

## 五、低代码扩展：附带脚本

技能目录下可放脚本，由 Agent 通过 exec 调用：

```
~/.openclaw/skills/my-skill/
├── SKILL.md
├── scripts/
│   └── fetch_data.sh
```

在 SKILL.md 的指令中说明：「当需要获取数据时，执行 `scripts/fetch_data.sh`」。

---

## 六、调试技巧

1. **查看加载情况**：`openclaw skills list`
2. **验证语法**：确保 SKILL.md 为合法 Markdown
3. **精简测试**：先写最小指令，确认生效后再扩展
4. **Token 影响**：技能越长，每次请求 Token 越多

---

## 延伸阅读

- [创建自定义技能](/tutorials/tools/creating-skills)
- [技能配置参考](/tutorials/tools/skills-config)
- [技能冲突解决](/openclaw-knowledge/module-03-advanced/01-skills/03-conflict)
