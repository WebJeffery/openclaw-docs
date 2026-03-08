# 文件与日程管理

> 自动分类 + 提醒同步

通过 OpenClaw 的 Agent + 文件工具 + Cron，实现文件自动分类、日程提醒与同步。

---

## 一、文件自动分类

### 思路

1. Agent 具备 `read_file`、`list_dir`、`write_file` 等能力
2. 通过 Skill 定义分类规则（按扩展名、关键词、日期等）
3. Cron 定时触发或用户手动触发分类任务

### Skill 示例

```markdown
# ~/.openclaw/skills/file-classify/SKILL.md

# 文件分类技能

## 指令
分类规则：
- 扩展名 .pdf/.docx → 文档
- 扩展名 .xlsx/.csv → 数据
- 扩展名 .jpg/.png → 图片
- 文件名含「会议」「纪要」→ 会议
- 文件名含「报销」「发票」→ 财务

对指定目录执行分类时，按规则创建子目录并移动文件，输出分类结果摘要。
```

### 使用方式

在聊天中发送：「帮我整理 /path/to/downloads 目录下的文件，按类型分类」。

---

## 二、日程提醒

### 方式一：Cron 定时提醒

```json5
{
  cron: {
    jobs: [
      {
        schedule: "0 9 * * 1-5",
        agent: "daily-reminder",
        message: "检查今日待办，发送提醒到 session work-reminder"
      }
    ]
  }
}
```

Agent 可读取本地待办文件（如 `~/todo.md`）或通过 API 获取日程，整理后推送到指定会话。

### 方式二：与日历集成

若使用 Google Calendar、Outlook 等，可通过 Webhook 或外部脚本在事件触发时调用 OpenClaw Agent，由 Agent 生成提醒内容并推送到飞书/Telegram。

---

## 三、待办同步

### 本地待办文件

在 `~/todo.md` 维护待办，Agent 可读取并：

- 按优先级排序
- 标注过期项
- 生成每日摘要

### 斜杠命令

```json5
{
  slashCommands: {
    "/待办": {
      description: "生成今日待办摘要",
      prompt: "读取 ~/todo.md，按优先级和截止日期整理今日待办，输出简洁列表"
    }
  }
}
```

---

## 四、配置要点

- 启用 `tools.profile: "coding"` 以使用文件读写
- 若需访问特定目录，需在 exec 或 sandbox 中配置允许路径
- 日程/待办数据若在云端，需通过 API 或脚本桥接

---

## 延伸阅读

- [Cron 定时任务](/tutorials/automation/cron-jobs)
- [Webhook 外部触发](/tutorials/automation/webhook)
- [执行工具](/tutorials/tools/exec)
