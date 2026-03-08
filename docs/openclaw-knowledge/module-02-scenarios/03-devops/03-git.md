# Git 仓库集成

> 自动化指令

通过 OpenClaw Agent + exec 工具，在聊天中执行 Git 操作：状态查看、提交、PR 摘要等。

---

## 一、配置 exec 白名单

```json5
{
  tools: {
    exec: {
      enabled: true,
      allowedBinaries: ["git", "ls", "cat", "head"]
    }
  }
}
```

---

## 二、常用场景

### 状态查看

```
当前目录的 git 状态是什么？有哪些未提交的改动？
```

Agent 执行 `git status`、`git diff --stat` 等并解读。

### 提交信息生成

```
根据最近的改动，生成一条符合 Conventional Commits 的提交信息
```

Agent 读取 `git diff`，生成类似 `feat(api): add user login endpoint` 的提交信息。

### PR 摘要

```
根据最近 5 个 commit 生成 PR 描述
```

Agent 执行 `git log -5 --oneline` 或 `git log -5 -p`，整理为 PR 描述模板。

---

## 三、斜杠命令

```json5
{
  slashCommands: {
    "/git status": {
      description: "查看 Git 状态",
      prompt: "执行 git status 和 git diff --stat，用简洁中文总结当前改动"
    },
    "/commit": {
      description: "生成提交信息",
      prompt: "根据 git diff 生成一条 Conventional Commits 格式的提交信息，并给出 git add 和 git commit 的完整命令"
    },
    "/pr": {
      description: "生成 PR 描述",
      prompt: "根据最近 5 个 commit 生成 PR 标题和描述，包含改动概要、测试建议"
    }
  }
}
```

---

## 四、Skill 注入 Git 规范

```markdown
# ~/.openclaw/skills/git-workflow/SKILL.md

# Git 工作流技能

## 指令
- 提交信息遵循 Conventional Commits：feat/fix/docs/style/refactor/test/chore(scope): description
- PR 描述包含：背景、改动、测试、相关 Issue
- 不自动执行 git push、git force push 等危险操作，仅输出建议命令
```

---

## 五、与 GitHub/GitLab 集成

OpenClaw 无官方 GitHub/GitLab 插件，但可通过：

1. **Webhook**：GitHub PR 创建时触发 Webhook，调用 OpenClaw 生成 PR 摘要并回写到 PR 描述
2. **Cron**：定时检查仓库，有新 PR 时生成摘要推送到 Telegram/飞书
3. **CLI**：在本地执行 `openclaw run "根据当前分支的改动生成 PR 描述"`，手动粘贴到 PR

---

## 六、安全注意

- 不要将 `git push --force`、`git reset --hard` 等加入自动执行白名单
- 涉及敏感仓库时，注意对话内容不要泄露到公开通道
- 建议在独立工作区或 sandbox 中操作 Git

---

## 延伸阅读

- [执行工具](/tutorials/tools/exec)
- [Webhook 外部触发](/tutorials/automation/webhook)
- [创建自定义技能](/tutorials/tools/creating-skills)
