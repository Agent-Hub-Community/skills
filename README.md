# AgentHub Skills

[Agent Hub](https://agent-hub-community.github.io/) 社群公开给 Claude Code、Codex、OpenCode、Cursor 等 Agent 使用的技能包（Skills）。

| Skill | 适合谁用 | 能做什么 |
|---|---|---|
| [`agenthub`](skills/agenthub/SKILL.md) | 所有读者与研究者（零配置，直接联网查） | 查 [AgentHub 日报](https://agent-hub-community.github.io/newspaper/)已发布的每日群聊总结（一群到五群）、自然周周报、外部资讯、资料库（论文／代码／数据集／Benchmark）、招聘与活动机会，以及群聊黑话词典；支持 BM25 + 语义混合搜索，也能帮你在资料库里挑群友分享过的其他 Agent Skill 安装 |
| [`agenthub-daily`](skills/agenthub-daily/SKILL.md) | 日报维护者与自建社群总结的开发者 | 配合 [`bot-workflow`](https://github.com/Agent-Hub-Community/bot-workflow) 和 [`newspaper`](https://github.com/Agent-Hub-Community/newspaper) 两个仓库，从本机微信群聊和外部渠道采集素材、调模型写稿与 AI 锐评、审稿定稿、生成网页预览并发布 |

---

## 1. 安装读者检索 Skill（`agenthub`）

装好后，你可以直接问你的 Agent：

- “AgentHub 群里这周在聊什么？”
- “群里有没有聊过或分享过 computer use / agentic RL 的评测基准？”
- “日报里常说的 A社、哈基米、RSI 是什么意思？”
- “最近群里有哪些 Agent 方向的实习或全职招聘？”
- “帮我在 AgentHub 资料库里找几个好用的科研或作图 skill。”

### 方式 A：发一句话让 Agent 自己装

把下面这段话直接发给 Claude Code、Codex 或其他能读写本机文件的 Agent：

```text
把 AgentHub 装成你的 skill：下载 https://raw.githubusercontent.com/Agent-Hub-Community/skills/main/skills/agenthub/SKILL.md ，原样保存为你的用户级 skills 目录下的 agenthub/SKILL.md（Claude Code 是 ~/.claude/skills/agenthub/SKILL.md，Codex 是 ~/.agents/skills/agenthub/SKILL.md）。目录不存在就新建，已有旧版就覆盖。
```

### 方式 B：命令行安装

```bash
# Claude Code
mkdir -p ~/.claude/skills/agenthub
curl -fsSL https://raw.githubusercontent.com/Agent-Hub-Community/skills/main/skills/agenthub/SKILL.md \
  -o ~/.claude/skills/agenthub/SKILL.md

# Codex / 通用 ~/.agents/skills
mkdir -p ~/.agents/skills/agenthub
curl -fsSL https://raw.githubusercontent.com/Agent-Hub-Community/skills/main/skills/agenthub/SKILL.md \
  -o ~/.agents/skills/agenthub/SKILL.md
```

或者直接克隆本仓库后软链过去：

```bash
git clone https://github.com/Agent-Hub-Community/skills.git
ln -s "$PWD/skills/skills/agenthub" ~/.claude/skills/agenthub
```

---

## 2. `agenthub` 背后的公开检索接口

`agenthub` 不需要 API Key 或登录，直接访问线上公开的检索接口与静态 Markdown / JSON 导出：

| 入口 | 地址 | 用途 |
|---|---|---|
| 全站混合搜索 | `https://agenthub-feedback.haoqingeric.workers.dev/content/search?q=...` | 按 BM25 + 语义向量搜日报、周报、动向观察、资料库和机会（自动扩展群内缩写与外号） |
| 刊期大纲 | `https://agenthub-feedback.haoqingeric.workers.dev/content/edition` | 查看最新一期或指定日期的各群报头与话题目录（周报加 `?kind=weekly`） |
| 资料库筛选 | `https://agenthub-feedback.haoqingeric.workers.dev/library/search` | 按 `type`（`paper` / `code` / `benchmark` / `dataset` 等）、`topic`、`keyword` 筛资料 |
| 群聊黑话词典 | `https://agent-hub-community.github.io/newspaper/glossary/glossary.json` | 群里常用缩写、模型外号、梗与正式名称对照 |
| 站点总索引 | `https://agent-hub-community.github.io/newspaper/llms.txt` | 给 LLM / Agent 的全站说明与机器可读文件入口 |

注意：命令行用 `curl` 调搜索接口传中文时，请用 `curl -G --data-urlencode 'q=...'` 编码。

---

## 3. 安装日报生产与编审 Skill（`agenthub-daily`）

如果你要跑一整套群聊采集、调模型写稿、定稿回执和静态站预览，需要先克隆 [`newspaper`](https://github.com/Agent-Hub-Community/newspaper) 与 [`bot-workflow`](https://github.com/Agent-Hub-Community/bot-workflow)，再挂载 `agenthub-daily`：

```bash
ln -s "$PWD/skills/skills/agenthub-daily" ~/.claude/skills/agenthub-daily
```

环境配置与离线合成示例见 [`bot-workflow` 的 README](https://github.com/Agent-Hub-Community/bot-workflow#readme)。

---

## 相关仓库

- 线上日报与资料库：<https://agent-hub-community.github.io/newspaper/>
- 社群主页：<https://agent-hub-community.github.io/>
- 网站与公开数据仓库：[Agent-Hub-Community/newspaper](https://github.com/Agent-Hub-Community/newspaper)
- 采集、写稿与审核流程仓库：[Agent-Hub-Community/bot-workflow](https://github.com/Agent-Hub-Community/bot-workflow)

## 许可证

本仓库采用 [MIT 许可证](LICENSE)。
