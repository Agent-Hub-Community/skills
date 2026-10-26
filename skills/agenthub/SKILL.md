---
name: agenthub
description: 查询 AgentHub（Agent Hub，讨论 LLM Agent 研究与工程的中文社群，含一群到五群及 AI Safety、Robot、AI4S 垂类专项群）已发布的群聊日报、周报、资料库、招聘／活动机会和群聊黑话词典。当用户问“AgentHub 群里最近在聊什么”“某天或这周群里讨论了什么”“群里有没有聊过或分享过 X”（如 computer use、agentic RL、coding agent、具身智能、AI 安全、AI4Science、评测基准、某篇论文或某个开源项目）、查群日报里的缩写与模型外号、看社区近期招聘／活动／征稿，或想了解 AgentHub 社群网站与日报工作流时使用。
---

# AgentHub 检索与阅读指南

AgentHub 把一群到五群每天的 LLM Agent 研究与工程讨论整理成日报，每周汇总一群到五群及三个垂类专项群（AI Safety、Robot、AI4S）的讨论生成周报，并维护资料库、机会（招聘／活动／征稿）和群聊黑话词典。所有公开接口和导出文件都是现成的 JSON 或 Markdown，无需登录，也不用抓 HTML。

- 线上日报与资料库：https://agent-hub-community.github.io/newspaper/
- 社群主页：https://agent-hub-community.github.io/
- 网站源码：https://github.com/Agent-Hub-Community/newspaper
- 采集与写稿工作流：https://github.com/Agent-Hub-Community/bot-workflow

## 怎么查最快

1. 浏览最新一期或某一天聊了什么：先调 https://agenthub-feedback.haoqingeric.workers.dev/content/edition （周报加 ?kind=weekly，指定日期加 ?date=YYYY-MM-DD）拿到各群报头和话题标题清单，再顺着感兴趣的 read_url 读单条或单版 Markdown。网页端也可直接打开当期总览页（https://agent-hub-community.github.io/newspaper/daily/YYYY-MM-DD/overview/ 或 https://agent-hub-community.github.io/newspaper/weekly/起始日期/结束日期/overview/）一页速览全部版面。
2. 搜某个技术话题、模型或论文讨论：调 https://agenthub-feedback.haoqingeric.workers.dev/content/search （支持 BM25 + 语义混合检索，自动扩展群内缩写与外号）。
3. 按类型或方向找论文、开源仓库、benchmark：调 https://agenthub-feedback.haoqingeric.workers.dev/library/search 按 type、topic、keyword 筛选。最近几天刚分享的链接如果还没进每周更新的资料库，用 /content/search 搜日报即可补齐。
4. 遇到看不懂的缩写、谐音或代号（如 oai、A社、哈基米、RSI）：查 https://agenthub-feedback.haoqingeric.workers.dev/read/glossary/glossary.json

## 回答约定

1. 根据实际查到的内容作答；站内没搜到时直接说明未检索到。
2. 每条结论附上结果里的网页 url（日报话题形如 https://agent-hub-community.github.io/newspaper/daily/YYYY-MM-DD/group-N/#topic-K，周报话题形如 https://agent-hub-community.github.io/newspaper/weekly/起始日期/结束日期/group-N/#topic-K，资料形如 https://agent-hub-community.github.io/newspaper/library/#<id>），方便读者点回原页。
3. 区分群内讨论（日报为一群到五群，周报还含 `group-6` AI Safety、`group-7` Robot、`group-8` AI4S）和编辑整理的“外部资讯”版面。正文里的“群友”“分享者”是单条话题内的代称，不同话题之间不对应同一个人。

## 查询接口

无需登录或密钥。地址里的中文和空格需要 URL 编码，直接发裸中文字符会得到正文为空的 HTTP 400。
命令行用 `curl -G --data-urlencode`：

```bash
curl -fsSG https://agenthub-feedback.haoqingeric.workers.dev/content/search --data-urlencode 'q=科研经验' -d limit=5
```

| 要做什么 | 请求 |
|---|---|
| 搜讨论话题、动向观察、资料和机会 | GET https://agenthub-feedback.haoqingeric.workers.dev/content/search?q=%E7%A7%91%E7%A0%94%E7%BB%8F%E9%AA%8C&limit=5 （q=科研经验） |
| 看最新一期日报目录 | GET https://agenthub-feedback.haoqingeric.workers.dev/content/edition （周报加 kind=weekly） |
| 看某天的日报目录 | GET https://agenthub-feedback.haoqingeric.workers.dev/content/edition?date=YYYY-MM-DD |
| 列已发布的刊期 | GET https://agenthub-feedback.haoqingeric.workers.dev/content/editions?kind=daily&limit=5 |
| 按类型、主题、关键词筛资料库 | GET https://agenthub-feedback.haoqingeric.workers.dev/library/search?type=benchmark&topic=computer-use&limit=5 |
| 查群里发过的招聘、活动、征稿 | GET https://agenthub-feedback.haoqingeric.workers.dev/content/search?kind=opportunity&limit=20 ，全量见 https://agenthub-feedback.haoqingeric.workers.dev/read/library/opportunities.json |
| 读单条话题或资料 | 访问返回结果里的 read_url（Markdown）或 detail_url（JSON） |
| 订阅更新 | https://agenthub-feedback.haoqingeric.workers.dev/read/feed.json（JSON Feed）或 https://agent-hub-community.github.io/newspaper/feed.xml（Atom） |

### 搜索结果与排序

- 每条结果带 `title`、`date`、`group`、`excerpt`、`relevance`（0.3 到 1）、`url`（网页地址，给用户引用时贴这个）、`read_url`（单条 Markdown）和 `detail_url`（完整 JSON）。
- 搜索同时跑 BM25 词面匹配和 Workers AI 语义匹配（RRF 融合），`matched_by` 标明是 `words`、`meaning` 还是 `both`。
- 群里的缩写、模型代号和同义写法会自动扩展，实际扩展出的词列在 `query.expanded`；站内从未出现过的普通英文词会自动剔除重试，列在 `query.dropped_terms`。
- 直接用中英文自然语言问句也能搜（“群里有没有人聊过”、`what`、`does` 等疑问虚词不占权重）。如果搜了站内从未写过的版本号（如 `GPT-7`），接口返回 0 条并在 `known_versions` 和 `retry_url` 里给出站内实际写过的版本。

### 常用参数

- `/content/search`：`q`（≤120 字）、`kind`（`daily` / `weekly` / `library` / `opportunity`）、`group`（日报为 `group-1` 到 `group-5`，周报还含专项群 `group-6` AI Safety、`group-7` Robot、`group-8` AI4S，外部资讯为 `external`）、`from` 与 `to`（`YYYY-MM-DD`，含首尾；周报按区间相交匹配）、`limit`（1–20，默认 5）、`offset`。翻页直接请求返回的 `next_url`。
- `/content/edition`：`kind`（`daily` / `weekly`）、`date`（默认 `latest`；周报填周日结束日期）、`group`。返回各版面的标题、导语和每条话题的 `title` + `read_url`，适合先看目录再挑几条细读。如果某天缺刊，404 响应里的 `nearest.before` 和 `nearest.after` 会给出相邻刊期。
- `/library/search`：`q`、`type`（`paper` / `benchmark` / `dataset` / `code` / `model` / `tool` / `article` / `video`）、`topic`（如 `coding-agents`、`computer-use`、`training` 等，完整枚举见首页响应的 `available`）、`keyword`（支持别名）。每条资料的 `discussions` 列出引用过该链接的日报或周报话题；为空时可顺着 `discussion_search_url` 按名称搜讨论。
- 机会（`kind=opportunity`）：返回 `opportunity_kind`（`hiring` / `event` / `cfp`）、`first_shared`（发布日）、`open`（按请求当天北京时间计算的开放状态）和 `closes`（截止日）。回答时优先列 `open: true` 的条目，写清发布日、截止日和官方链接；没有公开链接的内推条目写“联系方式见群里原消息”。

## 帮用户安装群里分享过的 skill

1. 用 https://agenthub-feedback.haoqingeric.workers.dev/library/search?keyword=agent%20skills&type=code 列出条目，也可按需补查 `research-tools`、`coding-agents` 等主题，列出标题、一句话简介和仓库地址。
2. 安装前先让用户确认装哪一个。打开对应仓库检查是否有 `SKILL.md` 或安装说明；没有 `SKILL.md` 的是普通代码库，跟用户说清楚用法即可。
3. 需要运行仓库自带脚本前，先把脚本内容给用户过目。
