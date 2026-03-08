# 合规数据接入

> 合规数据源、API 配置与数据调取

A股投研场景需使用合规、可追溯的数据源。本文介绍如何将合规数据接入 OpenClaw，供 Agent 调用分析。

---

## 一、合规数据源概览

### 常见合规数据提供商

| 提供商 | 数据类型 | 接入方式 | 适用场景 |
|--------|----------|----------|----------|
| **同花顺 iFinD** | 行情、财务、宏观 | HTTP API、Python SDK | 量化、投研 |
| **东方财富** | 行情、资讯 | 开放 API | 行情监控 |
| **Wind** | 全市场数据 | 专业终端 + API | 机构投研 |
| **Tushare** | 免费基础数据 | Python 库 | 个人学习、回测 |
| **AKShare** | 开源免费 | Python 库 | 轻量级数据获取 |

::: warning 合规提示
使用前请确认数据源的服务协议与使用范围，个人投资者需注意数据仅供个人研究使用，不得用于商业分发。
:::

---

## 二、接入思路：Skill + 外部脚本

OpenClaw 通过 **技能（Skills）** 和 **工具** 扩展能力。合规数据接入典型做法：

1. **编写数据获取脚本**：调用数据 API，输出结构化结果
2. **通过 exec 工具或自定义 Skill 调用**：Agent 触发脚本获取数据
3. **Agent 基于返回结果做分析**：生成报告、给出建议

### 示例：Tushare 数据调取（Python）

```python
# ~/.openclaw/scripts/fetch_stock.py
import tushare as ts
import json
import sys

def main():
    ts.set_token("YOUR_TUSHARE_TOKEN")
    pro = ts.pro_api()
    code = sys.argv[1] if len(sys.argv) > 1 else "000001.SZ"
    df = pro.daily(ts_code=code, start_date="20250101", end_date="20250308")
    print(df.to_json(orient="records", force_ascii=False))

if __name__ == "__main__":
    main()
```

配置 exec 白名单允许 `python3` 后，Agent 可执行：

```bash
python3 ~/.openclaw/scripts/fetch_stock.py 000001.SZ
```

---

## 三、通过 Skill 注入数据使用规范

创建 A股数据使用技能，约束 Agent 的数据调用方式：

```markdown
# ~/.openclaw/skills/a-share-data/SKILL.md

# A股合规数据使用技能

## 描述
在涉及 A股数据时，必须通过合规数据源获取，不得使用未授权来源。

## 使用方法
当用户请求行情、财务、异动等数据时启用。

## 指令
1. 行情数据：优先使用已配置的 fetch_stock 脚本（Tushare/iFinD）
2. 财务数据：通过 pro.fina_indicator 等接口获取
3. 公开资讯：可使用 web_search 搜索公开报道，并注明来源
4. 输出时需标注数据来源与更新时间
```

---

## 四、Web Search 作为补充

对于公开资讯、研报摘要等，可启用 `web_search` 工具：

```json5
{
  tools: {
    web: {
      search: {
        provider: "brave",  // 或 perplexity 等
        apiKey: "${BRAVE_API_KEY}"
      }
    }
  }
}
```

Agent 可搜索「某某股票 最新公告」「某某板块 资金流向」等，用于辅助分析。注意区分「公开资讯」与「需授权行情数据」。

---

## 五、配置检查清单

- [ ] 数据 API Token 已配置（环境变量或 SecretRef）
- [ ] exec 工具已启用，且白名单包含 `python3` 等必要命令
- [ ] 数据获取脚本已编写并测试
- [ ] A股数据使用 Skill 已创建并加载
- [ ] 输出格式与合规要求已确认

---

## 延伸阅读

- [创建自定义技能](/tutorials/tools/creating-skills)
- [执行工具配置](/tutorials/tools/exec)
- [全市场异动监控](/openclaw-knowledge/module-02-scenarios/01-a-share/02-monitor)
