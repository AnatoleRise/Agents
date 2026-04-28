# Competitor Research Skill Kit

A platform-agnostic AI skill for automated competitive analysis. Zero code dependencies — pure prompt-driven strategy that tells your AI agent how to search, analyze, and generate professional competitor research reports.

一个通用的 AI 竞品分析技能包，适用于任何智能体平台。零代码依赖，纯提示词驱动。

[English](#english) | [中文](#中文)

---

## English

### How It Works

```
User Query → Intent Recognition → Search Strategy → web_search → Data Cleaning → Report Generation
```

The skill provides the **strategy** (what to search, how to analyze, output format). Your AI agent provides the **execution** (web search, LLM reasoning).

### Quick Start

```bash
git clone https://github.com/750928465/competitor-research-skill-kit.git
```

Copy the skill directories to your agent's skill directory:
- **OpenClaw**: Copy `skills/*` to `~/.openclaw/workspace/skills/`
- **Claude Code**: Copy `skills/*` to `.claude/skills/`
- **Other agents**: Point your agent to the project root or import the `skills/` directory

### Usage

Simply ask your agent:

```
"AI email assistant market analysis"
"Superhuman email client deep research"
"Superhuman vs Front vs Missive comparison"
```

### Three Analysis Scenarios

| Scenario | Example Input | Output Focus |
|----------|--------------|--------------|
| Market Analysis | "AI email assistant market" | Market size, players, trends |
| Product Deep Dive | "Superhuman email client" | Features, pricing, reviews |
| Competitive Comparison | "Superhuman vs Front" | Feature matrix, pricing, pros/cons |

### Project Structure

```
competitor-research-skill-kit/
├── AGENTS.md                                    # Agent behavior & workflow rules
├── SOUL.md                                      # Personality & work style
├── IDENTITY.md                                  # Identity & role definition
├── README.md
└── skills/
    ├── competitor-research/                     # Main entry skill (orchestrator)
    │   └── SKILL.md
    ├── search-engine/                           # Search strategy sub-skill
    │   ├── SKILL.md
    │   ├── references/
    │   │   ├── intent_parser.md                 # Intent recognition rules
    │   │   └── search_strategy.md               # Search query templates
    │   └── assets/
    │       └── sources.yaml                     # Trusted sources config
    └── report-generator/                        # Report generation sub-skill
        ├── SKILL.md
        └── references/
            ├── data_cleaning.md                 # Data structuring rules
            ├── report_template.md               # Report format templates
            └── example_output.md                # Sample report output
```

### Architecture

```
competitor-research (orchestrator)
    │
    ├─ Intent recognition (built-in)
    ├─ search-engine → generates queries, executes search, filters results
    └─ report-generator → cleans data, selects template, outputs report
```

### Requirements

Your AI agent needs:
- **OpenClaw `web_search` tool** (required) — for retrieving search results
- **OpenClaw `web_fetch` tool** (optional) — for detailed page content
- **File reading** capability — to load prompts and config files

For non-OpenClaw agents, map `web_search` and `web_fetch` to the platform's equivalent web search and URL fetch tools.

### License

MIT License

---

## 中文

### 工作原理

```
用户查询 → 意图识别 → 搜索策略生成 → web_search → 数据清洗 → 报告生成
```

技能包提供**策略**（搜什么、怎么分析、输出什么格式），你的 AI 助手提供**执行能力**（网页搜索、LLM 推理）。

### 快速开始

```bash
git clone https://github.com/750928465/competitor-research-skill-kit.git
```

将技能目录复制到你智能体的技能目录下：
- **OpenClaw**：复制 `skills/*` 到 `~/.openclaw/workspace/skills/`
- **Claude Code**：复制 `skills/*` 到 `.claude/skills/`
- **其他智能体**：指向项目根目录或导入 `skills/` 目录即可

### 使用方式

直接向你的智能体提问：

```
"AI 邮件助手市场分析"
"Superhuman 邮件客户端深度研究"
"Superhuman vs Front vs Missive 功能对比"
```

### 三种分析场景

| 场景 | 输入示例 | 输出重点 |
|------|---------|---------|
| 市场分析 | "AI 邮件助手市场" | 市场规模、主要玩家、发展趋势 |
| 产品深度研究 | "Superhuman 邮件客户端" | 功能特性、定价策略、用户评价 |
| 产品竞争对比 | "Superhuman vs Front" | 功能对比矩阵、定价分析、优劣势 |

### 项目结构

```
competitor-research-skill-kit/
├── AGENTS.md                                    # Agent 行为与工作流规范
├── SOUL.md                                      # 人格与工作风格
├── IDENTITY.md                                  # 身份与角色定义
├── README.md
└── skills/
    ├── competitor-research/                     # 主技能（编排调度）
    │   └── SKILL.md
    ├── search-engine/                           # 搜索策略子技能
    │   ├── SKILL.md
    │   ├── references/ (intent_parser, search_strategy)
    │   └── assets/     (sources.yaml)
    └── report-generator/                        # 报告生成子技能
        ├── SKILL.md
        └── references/ (data_cleaning, report_template, example_output)
```

### 架构说明

```
competitor-research（编排调度）
    │
    ├─ 意图识别（内置）
    ├─ search-engine → 生成查询、执行搜索、过滤结果
    └─ report-generator → 清洗数据、选择模板、输出报告
```

### 许可证

MIT License
