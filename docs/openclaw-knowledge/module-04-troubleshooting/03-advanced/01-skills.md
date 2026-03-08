# 技能冲突

> 卡顿 / 异常排查

多技能并存时，可能出现指令冲突、行为异常或卡顿。本文汇总常见表现与排查方法。

---

## 一、常见冲突表现

| 表现 | 可能原因 |
|------|----------|
| Agent 行为混乱、指令矛盾 | 多技能指令冲突 |
| 响应变慢、Token 暴增 | 技能过多、内容过长 |
| 某技能不生效 | 被同名工作区技能覆盖、Gate 未触发 |
| 插件与自定义技能冲突 | 同 ID、同 Hook 名冲突 |

---

## 二、指令冲突

### 现象

多个技能对同一问题给出不同指令，Agent 行为不一致。

### 处理

1. **合并相似技能**：将冲突指令合并到一个技能，用「若...则...」区分场景
2. **提高优先级**：通过 `agents` 限定技能只加载到特定 Agent，减少同时生效
3. **Gate 细化**：用不同 `keywords` 让技能在不同场景激活，避免同时触发

---

## 三、插件冲突

### 来源优先级

`config > workspace > global > bundled`

同 ID 插件按此顺序裁决，后者不覆盖前者，而是报 duplicate warning。

### 典型冲突

- **Memory 槽位**：仅允许一个 memory 插件，多个时需在配置中显式指定 `slot`
- **Gateway 方法**：插件注册的 RPC 与 core 冲突会直接失败
- **Hook 名**：同名 Hook 按 priority 排序执行，可能产生意外叠加

### 排查

```bash
openclaw plugins list
openclaw plugins diagnose
```

查看 `registry.diagnostics` 中的冲突与错误。

---

## 四、技能与插件叠加

技能由插件提供时，可能与自定义技能叠加：

1. **同主题技能**：若插件已有「代码审查」技能，自定义同名可能重复注入
2. **处理**：禁用插件的该技能，或将自己的技能放到工作区覆盖

---

## 五、性能与 Token 优化

### 技能过多导致卡顿

- 减少同时启用的技能数量
- 用 Gate 规则按需激活
- 压缩技能内容，删除冗余说明

### Token 暴增

- 每个技能都会增加 system prompt 长度
- 建议单次对话加载技能不超过 5 个
- 长技能拆成多个小技能，按场景分别启用

---

## 六、排查清单

1. `openclaw skills list` 查看已加载技能
2. `openclaw plugins diagnose` 查看插件冲突
3. 检查 `skills.*.gate` 是否过于宽松，导致无关对话也激活
4. 检查 `skills.*.agents` 是否合理分配
5. 临时禁用部分技能，观察行为是否恢复

---

## 延伸阅读

- [技能冲突解决](/openclaw-knowledge/module-03-advanced/01-skills/03-conflict)
- [技能配置](/tutorials/tools/skills-config)
- [多插件冲突治理](/beginner-openclaw-framework-focus/15-多插件冲突治理与优先级规范实战)
