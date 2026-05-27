---
title: "自用 | Codex 三层架构完全指南"
date: 2026-05-27T12:31:49+08:00
#draft: true
image: "/image/title_image_svg/pattern_horizon.svg"
description: To 提示词架构师 “@ 博客作者来根据从源头+那两人观点到我们初版、克里斯蒂娜的回复（包括评价）以及最后的修改一起，写成一个完整的详细教程”
tag: ["日志","AI TOOLS","CodeX","效率"]
categories : ["日志"]
top: True
---

！ [具体参照](https://app.lobehub.com/share/t/L6ekmpJg)，里面有详细过程和每个文件配置。

## 一个熟悉的场景
你打开终端，让 Codex 帮你修一个边界条件的 bug。30 秒后，你发现它把整个模块重构了——顺便统一了命名风格、提取了三个新抽象、删掉了一段你不确定是否有用的旧代码。

你翻了翻你的 AGENTS.md，里面有"修改时遵循最小改动原则""不要过度抽象"——写得清清楚楚。

问题出在哪？

**散文约束的致命缺陷：模型看到了，但没遵守。** 因为"最小改动"不是布尔值——它是需要判断力的原则，而在大量的 context window 的噪音中，这句话太容易被淹没了。

这篇文章将展示一个不同的思路：放弃用长篇散文约束 agent，转而用**三层架构**——机器强制执行层、行为指导层和渐进式知识发现层——构建一套真正可靠的规则系统。

---

## 一、两个极端，一个平衡点
在设计 AGENTS.md 的社群里，一直存在两派观点。
### 观点一：渐进式披露派
这派人主张用 AGENTS.md 作为"摄取管道"（ingestion pipeline）。入口文件极简，只做路由；每个目录维护 `.INDEX.md`（结构化链接）和 `.SUMMARY.md`（语义摘要）；agent 按需加载文档，而非在会话启动时全量 dump 所有项目知识。
这一思路的核心理念直接来自 Anthropic 的 Context Engineering 指南：
> 让 agent 自主导航和检索数据，实现渐进式披露——agent 通过探索逐步发现相关上下文。每次交互为下一次决策提供信息：文件大小暗示复杂度，命名约定暗示目的，时间戳可作为相关性代理。Agent 逐层构建理解，只在工作记忆中保持必要内容，利用笔记策略实现额外持久化。这种自我管理的上下文窗口让 agent 专注于相关子集，而非淹没在详尽但可能无关的信息中。
简单说：**不要在一开始就把所有东西塞给 agent。让它在需要的时候自己去发现。**

### 观点二：代码强制执行派

另一派人的态度直截了当：
> "Everyone who is writing pure prose in agents.md doesn't know how to use it. It's brittle and ineffective. Y'all should be writing policies enforced by actual code not prose."
这句话翻译过来就是：能用机器判定的规则，不该放在自然语言文件里。权限边界、工具约束、安全策略——这些应该用配置文件（比如 Codex 的 `config.toml`）强制执行，而不是依赖模型"理解并遵守"一段散文。

### 融合：两派并不矛盾

仔细看，这两派争论的其实是**不同层级的问题**。

"散文无用"派针对的是权限和安全——你说 "never print secrets"，模型可能遵守，也可能不遵守。这种规则放在散文里，可靠性约等于零。
但"渐进式披露"派处理的是**信息组织**——500 页架构文档、50 个设计决策、20 个模板，你怎么在不让 agent 上下文爆满的前提下让它获取这些知识？这根本不是 toml 能解决的问题。

真正的道路是：**散文不该做的事交给代码，散文该做的事做得更精炼。**
这就是三层架构的起源。

---

## 二、三层架构总览
```
请求进来
    │
    ▼
┌──────────────────────────────────────┐
│  L1: config.toml                      │
│  运行时约束引擎解析键值对              │
│  模型根本看不到，不消耗任何 token      │
│  → 权限、安全、工具边界               │
└──────────────┬───────────────────────┘
               │ 通过约束后
               ▼
┌──────────────────────────────────────┐
│  L2: .agents/ 行为文件                 │
│  注入模型上下文                        │
│  → BEHAVIOR.md: 判断力训练 (~600 tokens)│
│  → POLICIES.md: 领域约束 (~50 tokens)  │
│  → FILES.md: 约定 (~100 tokens)        │
│  → CONTINUITY.md: 状态 (~200 tokens)   │
│  固定开销: ~1,000 tokens               │
└──────────────┬───────────────────────┘
               │ 需要项目知识时
               ▼
┌──────────────────────────────────────┐
│  L3: .INDEX.md 链                      │
│  按需渐进加载                          │
│  → 仅相关文档进入上下文                │
│  → 无关文档永不触碰                    │
│  边际开销: ~100-500 tokens/次          │
└──────────────────────────────────────┘
```
**对比传统做法**：把所有规则、模板、设计文档塞进一个 AGENTS.md → 每次会话初始消耗 5000+ tokens。三层架构固定开销约 1000 tokens，知识按需加载——对大多数任务节省 60-80%。

---

## 三、L1：机器强制执行层
这一层解决一个核心问题：**什么规则不应该依赖模型的理解能力？**
答案是所有能被机械判定的规则。
看看下面这个对比：
```
散文版（AGENTS.md 中）：                     toml 版（config.toml 中）：
─────────────────────────                  ──────────────────────
"Never print secrets to terminal           strip_secrets_from_output = true
 output. Do not request users paste        secret_patterns = [
 secrets. Avoid commands that might            "(?i)(token|key|secret)\\S*",
 expose secrets like dumping env vars          "-----BEGIN [A-Z]+ PRIVATE KEY-----"
 broadly. Prefer existing                   ]
 authenticated CLIs; redact sensitive
 strings in any displayed output."
4 条建议，~200 tokens，零保证               2 行配置，0 tokens 注入，运行时强制
```
关键洞察：**config.toml 的注释不会被注入到模型上下文。** 配置文件由 Codex 的运行时约束引擎解析，不是拼到 system prompt 里发给模型。模型根本看不到这一层——它只是行为被约束了。
下面是完整的 `~/.codex/config.toml` 设计：
```toml
[security]
write_scope = "repo"
deny_write_globs = ["*.env", "*.key", "*.pem", "*.secret"]
deny_command_patterns = ["sudo *", "rm -rf /*", "curl * | sh"]
strip_secrets_from_output = true
[network]
default_api_mode = "readonly"
require_confirmation_for = ["DELETE", "POST", "PUT", "PATCH"]
[container]
require_container = true
deny_host_package_install = true
[recency]
recency_triggers = ["latest", "current", "today", "as of now"]
auto_timestamp_command = "date -Is"
[tools]
default_enabled = ["read", "write", "execute", "search", "web"]
require_user_approval_for = ["delete_file", "force_push"]
```
每一项都是 `key = value` 或 `key = [patterns]`——零歧义，零散文，程序直接判定。

---

## 四、L2：行为指导层
L1 管的是"能不能做"——这是硬边界。L2 管的是"怎么做得好"——这是判断力训练。
这一层的文件全部放在 `.agents/` 目录（加入 `.gitignore`），既不影响项目本身，也允许不同开发者有自己的配置。
### 文件结构与职责
```
<project>/.agents/
├── AGENTS.md          # 项目入口——极简路由，指向其他文件
├── ONBOARDING.md      # 会话初始化流程——确保 agent 不跳过关键步骤
├── BEHAVIOR.md        # 编码哲学——先想再写、极简优先、外科手术式修改
├── POLICIES.md        # 具体约束——"Python 3.11，别用 3.12 语法"
├── FILES.md           # 特殊文件约定——.INDEX.md、CONTINUITY.md 等
├── DOCUMENTATION.md   # 文档审阅流程——先读摘要，再判断是否深入
└── CONTINUITY.md      # 跨会话状态——PLANS/DECISIONS/PROGRESS/DISCOVERIES/OUTCOMES
```
### 入口文件：极简路由
入口 AGENTS.md 的核心使命不是包含内容，而是告诉 agent **去哪找内容**。整个项目入口只需这几行：
```markdown
# AGENTS.md
## Onboarding
Execute `.agents/ONBOARDING.md`.
## Rules
All constraints in `~/.codex/config.toml` apply.
Behavioral norms in `.agents/BEHAVIOR.md`.
## Knowledge Discovery
Technical background → start at `docs/.INDEX.md`.
Templates → `templates/.INDEX.md`.
Historical decisions → `decisions/.INDEX.md`.
**Principle**: never load all docs at once. Read `.SUMMARY.md` first.
## Continuity
`.agents/CONTINUITY.md`
```
### 为什么需要 ONBOARDING.md？
没有显式编排的初始化流程，agent 可能跳过关键文件。"你应该读"和"你必须读"的区别在于有没有一份 checklist。ONBOARDING.md 首先确保 agent 一次不漏地完成初始化：
```markdown
# ONBOARDING.md
1. Read `.agents/POLICIES.md` — CRITICAL: project constraints override defaults
2. Read `.agents/FILES.md` — special file conventions
3. Read `.agents/BEHAVIOR.md` — coding philosophy
4. Read `.agents/CONTINUITY.md` — restore context
5. Read `docs/.INDEX.md` — discover documentation
6. If the task requires: drill into `docs/**/.INDEX.md` as needed
```
**POLICIES.md 被放在第一位**——这是一次关键的架构修正（同时也是来自克里斯蒂娜的批评）。BEHAVIOR.md 定的是通用原则（"极简优先""先想再写"），在任何项目都成立；但 agent 真正犯错的场景不是不知道这些原则，而是在具体的技术决策点回到默认行为。

比如 agent 看到一段 Python 代码想用 `match-case` 简化，BEHAVIOR.md 说"简单优先"——但项目用 Python 3.9，`match-case` 不可用。POLICIES.md 具体约束才是这类 bug 的防线。

### BEHAVIOR.md：行为指南

```markdown
# BEHAVIOR.md

## 1. Think Before Coding

Don't assume. Don't hide confusion. Surface tradeoffs.

- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

Minimum code that solves the problem. Nothing speculative.

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Self-check: "Would a senior engineer call this overcomplicated?" If yes, simplify.

## 3. Surgical Changes

Touch only what you must. Clean up only your own mess.

- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.
- Remove only imports/variables/functions that YOUR changes made unused.

The test: every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

Transform vague requests into verifiable goals:

- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:

1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]


## 5. Accuracy and Recency

When a request depends on recency:

- Run `date -Is` and state the current timestamp explicitly.
- Prefer official/vendor docs for any dependency.
- Prefer the newest versioned docs, release notes, or changelogs.
- Cross-check at least two reputable sources for safety/compatibility-sensitive details.

## 6. Anti-Hallucination

- Read the full source document before drafting output.
- Re-read the original source before finalizing to verify:
  - factual accuracy
  - no invented details
  - wording/style preserved (unless user explicitly asked to rewrite)
- If paraphrasing is required, label it explicitly as a paraphrase.

## 7. Definition of Done

A task is done when:

- [ ] The requested change is implemented or the question answered
- [ ] Verification provided: build attempted (if code changed), lint run (if code changed)
- [ ] Errors/warnings addressed or explicitly listed as out-of-scope
- [ ] Tests/typecheck passed where applicable
- [ ] Relevant documentation updated
- [ ] Impact explained: what changed, where, why
- [ ] Follow-ups listed if anything was intentionally left out
- [ ] `.agents/CONTINUITY.md` updated if the change materially affects state

## 8. Self-Critique

Before finalizing any non-trivial output, perform one round of self-review:

**Step 1: Edge cases**
- What inputs could break this?
- What happens on empty/null/boundary values?

**Step 2: Reviewer perspective**
- If I were code-reviewing this, what would I flag?
- Is there a hidden assumption I'm not stating?
- Would this still work if the environment/version/scale changed?

**Step 3: Simplicity check**
- Is there a strictly simpler way to achieve the same result?
- Did I introduce any abstraction the problem doesn't demand?

**Step 4: State findings**
If you find issues, fix them before output. If you find tradeoffs worth noting, surface them explicitly rather than hiding them.

## 9. Escalation Protocol
When the agent encounters a situation not covered by existing documentation,
constraints, or behavioral guides:
**Required format for escalation:**

⚠️ ESCALATION: [one-line summary]

Context:

What I'm trying to do: [task description]
What I've tried: [approaches attempted, why they didn't work]
What I considered: [alternatives discarded and why]
Where I'm stuck: [specific decision point or knowledge gap]
Options (if applicable):
A. [Option A] — risk: [risk], requires: [requirement]
B. [Option B] — risk: [risk], requires: [requirement]

Question: [single, specific, actionable question]

**Rules:**
- Never escalate with a vague "how should I do this?"
- Always demonstrate that you've attempted to solve it first
- For tool/library questions, include what docs you've already consulted
- If the decision has tradeoffs, present them — don't force the user to do the analysis
```


**"外科手术式修改"的实际效果**：在克里斯蒂娜的评估中，这是改变幅度最大的行为维度（从"经常越界重构"到"只做必要修改"）。在 AGENTS.md 里写"保持最小改动"不work，但在一个精心组织的多层规则系统中，L1 的 `write_scope = "repo"` 从物理层限制范围，L2 的"Surgical Changes"条款从认知层定义了什么是正确的改动边界。
**自我质疑机制（Self-Critique）**：最初的 BEHAVIOR.md 有"反幻觉"条款（被动验证——拿输出和源文档对比），但缺少一个主动的攻击自己方案的步骤。修正后的 #8 Self-Critique 在每次非平凡输出前强制四步检查：
```
1. 边界情况：什么输入会破坏这个方案？
2. 审查者视角：如果我是 reviewer，我会质疑什么？
3. 简洁性检查：有没有更简单的等价方案？
4. 陈述发现：修好能修的，亮出值得讨论的权衡。
```
区别在于：反幻觉问的是"我有没有捏造？"自我质疑问的是"这个方案有什么我没看到的问题？"前者查输出，后者攻方案。
### POLICIES.md：从空壳到关键防线
最初的设计只是一个含四个占位标题的文件（Tech Stack / Code Review / Deployment / Testing）。经过修正后，改为填空式具体约束模板：
```markdown
# POLICIES.md
## Language & Runtime
- Python version: 3.11
- Banned syntax: No Python 3.12 features (type params, PEP 695)
## Build & Test
- Test framework: pytest
- Required flags: pytest --strict-markers
## Code Conventions
- Type checking: mypy --strict
- Forbidden patterns: No `Any` type, no bare except
## Dependencies
- Adding a dependency: team approval required
- Version pinning: exact versions in requirements.txt
```
每一条都是**可判定的**——agent 要么遵守要么违反，没有灰色地带。
### FILES.md

```markdown
# FILES.md

## Special File Conventions

| File | Purpose |
|:--|:--|
| `AGENTS.md` | Agent instruction entrypoint |
| `.INDEX.md` | Directory index for fast discovery |
| `.SUMMARY.md` | Directory content summary |
| `CONTINUITY.md` | Cross-session state persistence |
| `ONBOARDING.md` | Session initialization flow |
| `BEHAVIOR.md` | Coding philosophy and judgment norms |
| `POLICIES.md` | Domain-specific constraints |
| `README.md` | Human-maintained project log. **NEVER modify.** |

## Discovery Rules

- Entering any directory → read `.INDEX.md` first
- Need an overview → read `.SUMMARY.md`
- Related files are traced through links in `.INDEX.md`

## Orphan Detection

When navigating directories following the progressive disclosure convention,
if you encounter a `.md` file that is NOT listed in the directory's `.INDEX.md`:

1. Note it in `.agents/CONTINUITY.md` [DISCOVERIES]: "Found orphan file X not in .INDEX.md"
2. If the file appears relevant to the current task, read it anyway
3. Do NOT silently add it to `.INDEX.md` — surface it for human decision
```

---

## 五、L3：渐进式知识发现层
这是三层架构中最具结构性创新的部分，也是 token 效率最大的来源。
### 双层索引：INDEX + SUMMARY
每个包含项目知识的目录都维护两个文件：
- **`.INDEX.md`**：结构化链接列表。告诉 agent"这里有什么"。
- **`.SUMMARY.md`**：语义摘要（3-5 句）。帮 agent 判断"我需要读吗"。
两者的分工很明确：INDEX 做导航，SUMMARY 做筛选。agent 先读 SUMMARY 判断相关性，相关才通过 INDEX 深入，不相关就跳过。
### 加载流程示例
```
Agent 收到任务："给 pipeline 模块加点日志"
    ↓
读 .agents/AGENTS.md → 看到 docs/.INDEX.md
    ↓
读 docs/.INDEX.md → 看到多种文档分类
    ↓
读 docs/architecture/.SUMMARY.md → 看到 pipeline 相关
    ↓
读 docs/architecture/pipeline.md → 加载具体设计
    ↓
数据流文档、API 设计文档 → 从未加载
```
整个过程不需要 agent 做任何超出当前任务范围的上下文消费。数据流、API 设计、部署架构全部保持在文件系统中，从未进入 context window——因为它们与日志任务无关。
### 索引的结构
根索引（`.agents/.INDEX.md`）：
```markdown
# .agents/ Index
1. [Onboarding](ONBOARDING.md)
2. [Behavior Guide](BEHAVIOR.md)
3. [Project Policies](POLICIES.md)
4. [Documentation](DOCUMENTATION.md)
5. [Continuity](CONTINUITY.md)
6. [Project Docs](../docs/.INDEX.md)
7. [Templates](templates/.INDEX.md)
8. [Decisions](decisions/.INDEX.md)
```
项目文档索引（`docs/.INDEX.md`）再指向子目录的索引，递归深入。
### 为什么不用 ls/glob 自动生成？
自动目录列表会列出草稿、会议记录、个人便签——对 agent 完成任务毫无价值的内容。`.INDEX.md` 是一份**经过人工判断**的导航文件：只列出对完成任务有价值的信息。文件加了，索引跟着更新；文件过时了，索引跟着删减。这个筛选判断只能人来（或 agent 按规则）做。

---

## 六、跨会话记忆：CONTINUITY.md 协议

```markdown
# CONTINUITY.md

## [PLANS]
<!-- Current plan with status markers: [ ] pending, [=] in progress, [x] done -->

## [DECISIONS]
<!-- Format: ISO_TIMESTAMP [PROVENANCE] decision -->
<!-- Provenance tags: [USER], [CODE], [TOOL], [ASSUMPTION] -->
<!-- If unconfirmed, write UNCONFIRMED. Never guess. -->

## [PROGRESS]
<!-- Mid-implementation course changes, reflections, implications -->

## [DISCOVERIES]
<!-- Unexpected findings with short evidence snippets -->

## [OUTCOMES]
<!-- Task completion summary: achieved, remaining, lessons learned -->

## Anti-Bloat Rules

- Facts only. No transcripts. No raw logs.
- Every entry must include:
  - ISO timestamp (e.g., `2026-05-27T10:30Z`)
  - provenance tag: `[USER]`, `[CODE]`, `[TOOL]`, `[ASSUMPTION]`
  - if unknown → write `UNCONFIRMED`
- Information changes → supersede explicitly. Don't silently rewrite history.
- Sections bloating → compress older items into `[MILESTONE]` bullets.
```

一个经常被忽略的事实：agent 的最大弱点不是推理能力，而是**金鱼记忆**。每次会话结束，所有上下文消失。下次会话 start from scratch。
CONTINUITY.md 是对这一问题的直接回应。它用五个区段记录 agent 的工作状态：
```markdown
## [PLANS]          当前计划 + 状态标记 [ ] [=] [x]
## [DECISIONS]       ISO时间戳 + 来源标签 + 决策内容
## [PROGRESS]        中途变更 + 反思
## [DISCOVERIES]     意外发现 + 简短证据
## [OUTCOMES]        任务完成总结
```
**反膨胀规则是 CONTINUITY.md 的生命线**：
- 只记事实，不记对话。不记原始日志。
- 每条必须含：ISO 时间戳 + 来源标签（`[USER]`/`[CODE]`/`[TOOL]`/`[ASSUMPTION]`）
- 不确定的写 `UNCONFIRMED`，绝不猜测
- 信息变化时显式取代，不静默覆写历史
前面说的孤儿检测，也是在 CONTINUITY.md 的 DISCOVERIES 区段中记录的。

---

## 七、升级路径：当 agent 不知道该怎么办
最初的设计只说"asking questions when uncertain"。但"ask"太宽泛了——agent 可能给出无用提问（"你想让我怎么做？"），也可能更糟：不提问，默默猜一个方案。
修正后的结构化升级协议要求 agent 在困惑时提供(是behavior里的9.Escalation Protocol)：
> ⚠️ ESCALATION: [一句话摘要]
>
> **Context**: 我要做什么
> **What I've tried**: 试过什么，为什么不行
> **What I considered**: 考虑过什么替代方案，为什么放弃
> **Where I'm stuck**: 具体卡在哪——是知识缺口还是决策权衡
>
> **Question**: 一个具体、可执行的问题
规则：永远不要用模糊的"我该怎么办"升级。每次升级必须证明 agent 已经尝试过自己解决。

---

## 八、实际效果评估
克里斯蒂娜从六个维度评估了这套系统的行为改变幅度：
| 行为维度 | 裸 Codex | 加规则后 | 改变幅度 |
|:--|:--|:--|:--|
| 修改范围控制 | 经常越界重构 | 外科手术式 | **很大** |
| 会话连续性 | 每次从头来 | CONTINUITY 恢复 | **很大** |
| 上下文加载 | 全量 dump | 渐进式按需 | **很大**（token） |
| 幻觉频率 | 偶尔编造 API | 强制交叉验证 | 中等 |
| 代码简洁度 | 倾向过度抽象 | 简单优先 | 中等 |
| 任务完成率 | 写到 80% 就停 | DoD checklist | 中等 |
三个"很大"的改变背后是两条不同的因果链：
1. **Scope creep 急剧减少**：不是靠散文说服，而是 L1 物理约束（`write_scope = "repo"`）+ L2 认知训练（Surgical Changes）的双重防线。
2. **跨会话记忆可用**：CONTINUITY.md 让下一次会话的 agent 不用从头探索。
3. **Token 效率大幅提升**：不是渐进式发现"更优雅"，而是它把决策循环从"先吞所有信息再行动"变成了"行动中按需获取信息"——这改变了 agent 的认知结构，而不仅仅是优化资源消耗。
克里娜的总结：填实 POLICIES.md、加自我质疑规则后，这套东西是见过的 Codex 配置里前 5% 的水平。当然，前提是 agent 真的老老实实读了 ONBOARDING.md。

---

## 九、你需要知道的所有文件
### 全局配置（跨项目复用）
| 文件 | 位置 | 职责 |
|:--|:--|:--|
| `config.toml` | `~/.codex/` | L1 机器强制执行——安全/权限/工具/容器 |
| `AGENTS.md` | `~/.codex/` | 全局入口——指向 .agents/ 目录 |
### 项目级规则（每个仓库独立）
| 文件 | 位置 | 负载 |
|:--|:--|:--|
| `AGENTS.md` | `.agents/` | 项目入口路由 |
| `ONBOARDING.md` | `.agents/` | 会话初始化 checklist |
| `BEHAVIOR.md` | `.agents/` | 编码哲学 + 自我质疑 |
| `POLICIES.md` | `.agents/` | 具体约束（语言版本、测试要求等） |
| `FILES.md` | `.agents/` | 特殊文件约定 + 孤儿检测 |
| `DOCUMENTATION.md` | `.agents/` | 文档审阅流程 |
| `CONTINUITY.md` | `.agents/` | 跨会话状态五段式 |
### 渐进式索引链
| 文件 | 位置 | 职责 |
|:--|:--|:--|
| `.INDEX.md` | 每个知识目录 | 结构化链接列表 |
| `.SUMMARY.md` | 每个知识目录 | 语义摘要（判断相关性） |

---

## 十、一条现实的注解
这套架构有一个诚实的脆弱点：**索引维护靠人（或 agent 遵守规则）**。`.INDEX.md` 不会自动更新；文件加了但没进索引，对后续 agent 不可见。
三层缓解策略——POLICIES.md 的维护规则（预防）、CONTINUITY.md 的孤儿检测（发现）、code review 时的索引一致性审计——能覆盖大部分风险。但完美自动化不存在。
这不是设计缺陷，而是手动索引系统的固有代价。权衡是明确的：付出一些索引维护成本，换取每次会话 60-80% 的 token 节省。对于大多数项目，这个 tradeoff 是值得的。

---

**关于语言选择**：本文展示的所有 agent 指令文件使用英文。理由很实际——技术术语的英文 token 密度远高于中文