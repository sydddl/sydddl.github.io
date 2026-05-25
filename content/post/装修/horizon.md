---
title: "Horizon 新闻推送"
date: 2026-05-08T22:31:49+08:00
#draft: true
image: "/image/title_image_svg/pattern_horizon.svg"
description: 添加
tag: ["Blog日志","工具","Git"]
categories : ["工具"]
top: True
---

## 配置 horizon

> 这里我是先本地安装整了一下，想要定时更新自动推送还得是 Github Action，直接就是action的操作了。

fork [原GitHub项目](https://github.com/Thysrael/Horizon)，注意修改`data/config.json`文件，这里推荐参考原项目教程（[完整配置指南](https://github.com/Thysrael/Horizon/blob/main/docs/configuration.md)），我是本地安装先通过交互式向导生成这个文件，然后再进行修改的。

其中重点是两项：

- AI API
- 新闻 Sources
- 其他：
  - "filtering": {
    "ai_score_threshold": 7.0, # 过滤阈值，低于这个评分的就不展示了
    "time_window_hours": 120  # 新闻时间窗，一般和你要自动更新的频率挂钩，这里是5天一更（太频繁了我也懒得看）
    },

### deepseek api

参考 [API 文档](https://api-docs.deepseek.com/zh-cn/)，用的 `deepseek-v4-flash` 模型，很便宜，一次Horizon任务几毛钱。

这里的api如果也用deepseek的这个模型，可以直接复制，具体的KEY需要在仓库 `settings/secrets/actions` 中 `Repository secrets` 里添加名为 `OPENAI_API_KEY` 的密钥。


```json
"ai": {
    "provider": "openai",
    "model": "deepseek-v4-flash",
    "base_url": "https://api.deepseek.com",
    "api_key_env": "OPENAI_API_KEY",
    "temperature": 0.3,
    "max_tokens": 8192,
    "throttle_sec": 0.0,
    "languages": [
      "zh"
    ],
    "azure_endpoint_env": null,
    "api_version": null
  },
```

### 新闻 Sources

> to be continue

## GitHub Actions 自动执行

_Horizon works great as a GitHub Actions cron job. See [.github/workflows/daily-summary.yml](https://github.com/Thysrael/Horizon/blob/main/.github/workflows/daily-summary.yml) for a ready-to-use workflow that generates and deploys your daily briefing to GitHub Pages automatically._

可以直接用，注意:

```yml
on:
  schedule:
    - cron: '0 0 */5 * *'   # 这里是自动启动的时间，Runs every 5 days at 00:00 UTC
```

去仓库actions启用，先run一下看看能不能跑通。


## 同步到现有hugo博客

如果没有别的需求，可以直接在当前仓库打开 `settings/Pages` ，源（Source）选择 `Deploy from a branch`，分支选择 `gh-pages`
+ `root` （GitHub Actions跑通自动创建的分支）。

然后就可以在特定页面直接网页访问了，比如[我的是: https://sydddl.github.io/Horizon/](https://sydddl.github.io/Horizon/)。

但如果我想要定时的同步到我已有博客的特定栏目里要怎么做？

> Horizon → Hugo 自动同步方案

- [ ] 🎯 目标

    实现：
    - Horizon 自动生成 AI 新闻 md
    - 自动同步到 Hugo 博客
    - Hugo 自动展示，无需人工操作

- [ ] 🧠 架构设计

```
Horizon repo（生成 md）
        ↓
GitHub Action
        ↓
Push 到 Hugo repo
        ↓
content/horizon/
        ↓
Hugo 自动构建
        ↓
网页更新
```


- [ ] ⚙️ Markdown 标准化

Horizon 输出加入 Hugo frontmatter：

```md
---
title: ""
date: 
tags: []
categories: ["Horizon"]
---