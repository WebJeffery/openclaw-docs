# 素材搜集

> 自动抓取 + 分类

通过 OpenClaw 的 `web_search`、`web_fetch`、Firecrawl 等工具，实现素材的自动抓取与分类。

---

## 一、工具能力

| 工具 | 用途 | 配置 |
|------|------|------|
| **web_search** | 搜索关键词，返回标题、URL、摘要 | Brave、Perplexity 等 |
| **web_fetch** | 抓取指定 URL 的正文内容 | 内置 + Firecrawl 备用 |
| **Firecrawl** | JS 渲染页面、反爬站点 | 需 API Key |

---

## 二、素材搜集流程

1. **确定主题**：用户提供关键词或主题
2. **搜索**：Agent 使用 `web_search` 获取相关链接
3. **抓取**：对重点链接使用 `web_fetch` 或 Firecrawl 提取正文
4. **整理**：按来源、时间、类型分类，输出结构化摘要

---

## 三、配置示例

### Web Search

```json5
{
  tools: {
    web: {
      search: {
        provider: "brave",
        apiKey: "${BRAVE_API_KEY}"
      }
    }
  }
}
```

### Firecrawl（复杂页面）

```json5
{
  tools: {
    web: {
      fetch: {
        firecrawl: {
          apiKey: "${FIRECRAWL_API_KEY}"
        }
      }
    }
  }
}
```

---

## 四、Skill 注入搜集规范

```markdown
# ~/.openclaw/skills/material-collect/SKILL.md

# 素材搜集技能

## 描述
根据主题搜集网络素材，并按要求分类整理。

## 使用方法
当用户请求「搜集某某主题的素材」「找一些关于某某的资料」时启用。

## 指令
搜集流程：
1. 用 web_search 搜索 3–5 个相关关键词
2. 对前 3–5 条结果用 web_fetch 抓取正文
3. 提取：标题、来源、发布时间、核心观点、可引用句
4. 按「来源 | 标题 | 摘要 | 链接」格式输出表格
5. 若页面抓取失败，可尝试 Firecrawl 或标注「需手动访问」
```

---

## 五、斜杠命令

```json5
{
  slashCommands: {
    "/搜集": {
      description: "搜集主题素材",
      prompt: "根据以下主题搜集网络素材，输出结构化摘要表格，包含来源、标题、核心内容、链接。"
    }
  }
}
```

使用：`/搜集 人工智能在医疗领域的应用 2024`

---

## 六、定时搜集（Cron）

```json5
{
  cron: {
    jobs: [
      {
        schedule: "0 8 * * *",
        agent: "material-daily",
        message: "搜集今日「AI 行业动态」相关素材，推送到 session media-materials"
      }
    ]
  }
}
```

---

## 七、注意事项

- 遵守目标网站的 robots.txt 与使用条款
- 注明素材来源，避免侵权
- 部分站点需 Firecrawl 才能正确抓取

---

## 延伸阅读

- [Firecrawl 工具](/tutorials/tools/firecrawl)
- [Web Search 配置](/tutorials/tools/)
- [Cron 定时任务](/tutorials/automation/cron-jobs)
