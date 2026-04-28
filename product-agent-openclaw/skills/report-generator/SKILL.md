---
name: report-generator
description: 竞品研究报告生成子技能。将搜索结果进行结构化清洗，按模板生成专业的 Markdown 分析报告。由 competitor-research 主技能调用，也可独立使用。
---

# Report Generator

## Skill 名称
report-generator

## Skill 目标
接收原始搜索结果，进行结构化清洗与产品信息提取，按意图类型选择对应模板，生成完整的 Markdown 竞品分析报告。

## 适用场景
- 主技能 competitor-research 完成搜索后调用
- 用户已有搜索结果数据，需要生成结构化报告
- 用户需要将零散的竞品信息整理为专业分析文档

## 不适用场景
- 用户只需要搜索结果列表（不需要分析报告）
- 用户需要的是 PPT 或 Word 格式的报告（本技能仅输出 Markdown）
- 输入数据不是竞品相关的搜索结果

## 输入要求
- `intent_type`：market_analysis / product_deep_research / product_competition
- `target_product`：目标产品名称
- `search_results`：去重后的搜索结果数组（包含标题、URL、摘要、来源）
- 可选：`secondary_product`（对比场景）

## 处理流程

### Step 1: 数据清洗
读取 `{baseDir}/references/data_cleaning.md`，对搜索结果进行结构化处理：
- 提取产品信息：名称、厂商、官网、描述、功能列表
- 提取市场洞察：趋势、技术方向、用户偏好、竞争动态
- 标注验证状态：verified（官网确认）/ partial（部分信息）/ unverified（仅第三方提及）
- 按产品名合并、按内容去重（>80% 相似度丢弃）

### Step 2: 模板选择
读取 `{baseDir}/references/report_template.md`，根据意图类型选择：
- `market_analysis` → 市场分析报告模板
- `product_deep_research` → 产品深度研究报告模板
- `product_competition` → 竞争对比报告模板

### Step 3: 内容填充
- 按模板结构逐章节填充清洗后的数据
- 所有结论标注来源引用 `[1]`、`[2]`
- 缺失信息写"未在搜索结果中找到"
- 不确定信息标注 `[unverified]`

### Step 4: 质量检查
- 每个章节是否有内容（非空）
- 表格数据是否完整
- References 列表是否与正文引用一致
- 是否存在未标注来源的结论

### Step 5: 输出报告
返回完整的 Markdown 格式报告。

## 输出格式
三种报告模板的核心结构：

**市场分析报告**：执行摘要 → 市场概览 → 主要玩家表格 → 产品对比 → 市场趋势 → 推荐建议 → 引用

**产品深度研究报告**：执行摘要 → 产品概览 → 核心功能 → 技术架构 → 定价 → 优劣势 → 引用

**竞争对比报告**：执行摘要 → 产品概览表格 → 功能对比矩阵 → 定价对比 → 差异化分析 → 推荐建议 → 引用

详细模板参见 `{baseDir}/references/report_template.md`，示例输出参见 `{baseDir}/references/example_output.md`。

## 依赖资源
- `{baseDir}/references/data_cleaning.md` — 数据清洗规则与结构化提取标准
- `{baseDir}/references/report_template.md` — 三种报告模板定义
- `{baseDir}/references/example_output.md` — 完整示例报告

## 注意事项
- 不编造搜索结果中未出现的产品功能或定价
- 表格优先于段落（对比数据使用表格呈现）
- 保持客观中立，避免促销性语言
- 优劣势并重呈现
- 引用编号必须与 References 列表一一对应

## 示例调用

```
Input:
  intent_type: "product_competition"
  target_product: "Superhuman"
  search_results: [30+ 条去重后的搜索结果]

Output:
  # Superhuman Competitive Analysis Report
  ## Executive Summary
  在 AI 邮件助手市场中，Superhuman 与 Front、Missive 形成直接竞争...
  ## Products Overview
  | # | Product | Vendor | Website |
  |---|---------|--------|---------|
  | 1 | Superhuman | Superhuman Inc. | superhuman.com |
  ...
  ## Feature Comparison
  | Feature | Superhuman | Front | Missive |
  ...
  ## References
  [1] ... [2] ...
```
