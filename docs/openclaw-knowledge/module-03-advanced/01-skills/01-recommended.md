# 高频实用技能推荐

> 2026 版，避资源浪费

根据常见场景，推荐以下技能组合。按需启用可减少 Token 消耗、提升响应质量。

---

## 一、通用场景

### 代码审查（code-review）

- **用途**：审查代码安全性、可维护性、性能
- **适用**：开发者日常、PR 前自检
- **建议**：用 Gate 规则，仅在用户提到「审查」「review」「检查代码」时激活

### Git 工作流（git-workflow）

- **用途**：生成提交信息、PR 描述、分支命名规范
- **适用**：频繁使用 Git 的团队
- **建议**：配置 `keywords: ["commit", "PR", "git", "提交"]`

### 会议纪要（meeting-minutes）

- **用途**：从会议记录生成结构化纪要
- **适用**：职场、项目管理
- **建议**：可配置 `channels: ["feishu", "slack"]` 限定在办公通道

---

## 二、按场景选型

| 场景 | 推荐技能 | 说明 |
|------|----------|------|
| **开发** | code-review, git-workflow, sql-expert | 代码、版本、数据库 |
| **办公** | meeting-minutes, email-draft, doc-format | 纪要、邮件、文档 |
| **投研** | a-share-data, market-monitor | 数据规范、异动规则 |
| **内容** | content-write, compliance-check | 撰写、合规 |
| **运维** | ops-check, log-analyze | 巡检、日志分析 |

---

## 三、避资源浪费原则

### 1. 用 Gate 规则按需激活

```json5
{
  skills: {
    "sql-expert": {
      enabled: true,
      gate: {
        keywords: ["SELECT", "INSERT", "database", "SQL", "查询"]
      }
    }
  }
}
```

避免无关对话也加载 SQL 技能，节省 Token。

### 2. 按 Agent 分配技能

```json5
{
  skills: {
    "code-review": {
      enabled: true,
      agents: ["dev-assistant"]  // 仅开发 Agent 加载
    }
  }
}
```

不同 Agent 承担不同职责，技能不堆叠。

### 3. 控制技能篇幅

- 指令部分控制在 200–500 字
- 避免冗长示例和重复说明
- 复杂逻辑拆成多个小技能

### 4. 定期清理

```bash
openclaw skills list
```

移除长期未用、已过时的技能。

---

## 四、插件自带技能

部分插件内置技能，安装即用：

- **openclaw-devtools**：git、npm、docker 等开发技能
- **Firecrawl**：网页抓取、搜索相关技能

安装前确认插件来源可信，并检查其技能内容。

---

## 五、ClawHub 社区技能

```bash
openclaw skills search "code review"
openclaw skills install clawhub/code-review
```

::: warning 安全
从第三方安装前务必阅读 SKILL.md 内容，恶意技能可能改变 Agent 行为。
:::

---

## 延伸阅读

- [技能系统](/tutorials/tools/skills)
- [技能配置](/tutorials/tools/skills-config)
- [自定义技能开发](/openclaw-knowledge/module-03-advanced/01-skills/02-custom)
