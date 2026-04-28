# 飞书需求管理子智能体 - requirement-manager

管理飞书需求多维表格的全生命周期：刷新看板、归档需求、生成周报。

## 目录结构

```
requirement-manager/
├── README.md                           # 本文件
├── agents/
│   └── requirement-manager/            # 子智能体核心文件
│       ├── AGENTS.md                   # 行为指令（最重要的文件）
│       ├── SOUL.md                     # 人格风格
│       └── IDENTITY.md                 # 身份信息
└── skills/
    ├── feishu-requirement-board/       # 看板刷新 + 周报生成
    │   ├── SKILL.md
    │   ├── refresh.sh                  # 从 Bitable 拉取数据 + 生成看板 HTML
    │   ├── generate-board.js           # 生成看板 HTML 页面
    │   ├── weekly-report.sh            # 周报脚本入口
    │   └── weekly-report.js            # 周报生成逻辑
    ├── feishu-requirement-archive/     # 需求归档（本周新增）
    │   ├── SKILL.md
    │   └── resources/scripts/
    │       └── archive.js              # 扫描已上线/转出需求，写入统计 JSON
    └── feishu-requirement-entry/       # 需求录入评估（纯 AI 技能）
        └── SKILL.md
```

## 环境变量配置

此子智能体已移除所有本地硬编码路径，通过环境变量配置，跨节点部署无需修改脚本。

| 变量 | 必填 | 默认值 | 说明 |
|------|------|--------|------|
| `OUTPUT_DIR` | 否 | 当前工作目录 | 看板 HTML 和归档统计 JSON 的输出目录 |
| `RECIPIENT_ID` | 否 | null（不发送） | 飞书用户的 open_id，周报生成后通过机器人私聊推送 |
| `FEISHU_WEBHOOK_URL` | 否 | null | 替代 RECIPIENT_ID，用 webhook 发送周报 |

### OUTPUT_DIR

生成以下文件：
- `requirement-board.html` — 可视化需求看板
- `requirement-archive-stats.json` — 归档统计数据（供周报引用）

**示例：**
```bash
OUTPUT_DIR=/var/www/reports bash skills/feishu-requirement-board/refresh.sh
OUTPUT_DIR=/var/www/reports node skills/feishu-requirement-archive/resources/scripts/archive.js
```

不设置 OUTPUT_DIR 时，文件生成在当前工作目录。

### RECIPIENT_ID

设置后，`weekly-report.js` 会在终端打印周报的同时，通过飞书机器人将周报私聊推送给指定用户。

**如何获取用户 open_id：**
1. 让飞书机器人和该用户在同一个群聊中
2. 调用飞书 API：`GET https://open.feishu.cn/open-apis/im/v1/messages?receive_id_type=open_id`
3. 或联系飞书管理员获取

不设置时，周报仅在终端打印，不影响数据生成。

## 部署步骤

### 1. 放置文件

```bash
# 目标机器上
cp -r requirement-manager ~/.openclaw/workspace-agent-requirement-manager

# 创建 symlink（让子智能体技能指向实际文件）
ln -s ~/.openclaw/workspace-agent-requirement-manager/skills/* ~/.openclaw/workspace/skills/
```

或者更简单：直接用整个目录作为子智能体的 workspace：

```bash
openclaw agents add requirement-manager \
  --workspace ~/.openclaw/workspace-agent-requirement-manager \
  --model deepseek/deepseek-chat
```

### 2. 配置 Cron 任务

周五自动运行三条任务：

| 时间 | 任务 |
|------|------|
| 周五 11:00 | 刷新看板 |
| 周五 14:00 | 归档扫描 |
| 周五 15:00 | 生成周报 |

cron job 配置示例（写入 `~/.openclaw/cron/jobs.json`）：

```json
{
  "id": "requirement-manager-refresh-board",
  "schedule": { "kind": "cron", "expr": "0 11 * * 5", "tz": "Asia/Shanghai" },
  "payload": {
    "kind": "agentTurn",
    "message": "执行看板刷新任务：OUTPUT_DIR=/path/to/output bash skills/feishu-requirement-board/refresh.sh",
    "timeoutSeconds": 300
  },
  "agentId": "requirement-manager",
  "sessionTarget": "isolated",
  "enabled": true
}
```

也可参照 `skills/feishu-requirement-board/SKILL.md` 中的详细说明。

### 3. 验证

```bash
# 手动测试看板刷新
OUTPUT_DIR=/tmp/test bash skills/feishu-requirement-board/refresh.sh

# 手动测试归档
OUTPUT_DIR=/tmp/test node skills/feishu-requirement-archive/resources/scripts/archive.js

# 手动测试周报
OUTPUT_DIR=/tmp/test node skills/feishu-requirement-board/weekly-report.js
```

## 飞书应用配置

所有脚本共用同一组飞书应用凭据（已内置，通常无需修改）：

- **App ID:** `cli_a90b1312feb91bd8`
- **App Secret:** `3H2bgG03cdCZAQnKeUqrEdzHeCkNEhO4`
- **Bitable App Token:** `U79fbsW2VaNhC5sn3PVcrOfxnQd`
- **需求表 Table ID:** `tbl4TIy8hHDesDbD`

如需更换飞书应用，修改各 `.js`/`.sh` 文件顶部的 `CONFIG` 或开头变量即可。

## 依赖

- **运行时：** Node.js（支持 `require('https')`、`require('fs')`）
- **命令行工具：** `curl`、`jq`（用于 refresh.sh 获取 token 和拉取数据）
