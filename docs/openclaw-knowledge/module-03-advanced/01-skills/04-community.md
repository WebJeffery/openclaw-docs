# 社区优质技能获取与优化

> 选型与适配

从 ClawHub 等社区获取技能可快速扩展能力，但需注意选型、安全与适配。

---

## 一、ClawHub 技能库

```bash
# 搜索技能
openclaw skills search "code review"

# 安装技能
openclaw skills install clawhub/code-review

# 查看已安装
openclaw skills list

# 卸载
openclaw skills uninstall clawhub/code-review
```

---

## 二、选型标准

| 维度 | 建议 |
|------|------|
| **来源** | 优先官方、知名作者、星标高的技能 |
| **更新** | 查看最后更新时间，过久未更新慎用 |
| **依赖** | 确认是否需要额外 API Key、插件 |
| **评价** | 若有评论或 Issue，可参考他人反馈 |

---

## 三、安装前必查

### 1. 阅读 SKILL.md

技能会注入到 Agent 提示词，恶意内容可能：

- 泄露对话
- 改变 Agent 行为
- 注入不当指令

安装前务必查看 `SKILL.md` 全文。

### 2. 检查依赖

- 是否需要 Firecrawl、Brave 等 API Key
- 是否需要特定插件
- 是否需要 exec 白名单

### 3. 测试环境

建议先在测试 Agent 或独立会话中试用，确认无异常后再推广。

---

## 四、适配与优化

### 本地化修改

安装后技能在 `~/.openclaw/skills/<id>/`，可直接编辑 `SKILL.md`：

- 调整指令以符合你的流程
- 补充中文说明
- 收紧或放宽 Gate 规则

### 工作区覆盖

若只想在某项目使用，可复制到项目目录：

```bash
cp -r ~/.openclaw/skills/clawhub/code-review /your-project/.openclaw/skills/
```

然后按需修改项目内的 `SKILL.md`。

### 版本管理

对修改过的技能建议自建备份或 fork，避免下次 `skills install` 时被覆盖。

---

## 五、自建分享

若你开发了实用技能，可考虑：

- 提交到 ClawHub（若支持）
- 在 GitHub 建仓库分享
- 在社区文档中提供 SKILL.md 与使用说明

---

## 延伸阅读

- [技能系统](/tutorials/tools/skills)
- [自定义技能开发](/openclaw-knowledge/module-03-advanced/01-skills/02-custom)
- [技能冲突解决](/openclaw-knowledge/module-03-advanced/01-skills/03-conflict)
