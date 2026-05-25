---
title: ""
date: 2026-05-22T22:31:49+08:00
#draft: true
image: https://s2.loli.net/2021/12/07/1rVtuJzys7Foecp.jpg
description: 记载了这个blog的前世今生
tag: ["Blog日志"]
categories : ["Blog日志"]
top: True
---

<meting-js server = "netease"  type="song" id="1931747" list-folded=true fixed="true"></meting-js>
<meting-js server = "netease" type="song" id="22722861" list-folded=true></meting-js>

<meting-js auto="https://link.jscdn.cn/sharepoint/aHR0cHM6Ly8xZHJpdi1teS5zaGFyZXBvaW50LmNvbS86dTovZy9wZXJzb25hbC9zdG9yXzFkcml2X29ubWljcm9zb2Z0X2NvbS9FUWtFNzZXekFsTk5uLWZGaUpDUkFKWUI0bVJoMm5fVzRoOGdVNkNRU1JKLWRRP2U9cmFBN1Q2.mp3" list-folded=true></meting-js>


## TODO <i class="fa-solid fa-list"></i> 


## Begining


- [x] 下载 Codex window 桌面端，启动报错：

`Sign-in failed: failed to start login server: 以一种访问权限不允许的方式做了一个访问套接字的尝试。 (os error 10013)`

按照[解决codex启动报错套接字](https://blog.voiceclouds.cn:8444/tech/fix-codex-startup-socket-error/)顺利解决。

```sh
net stop winnat
netsh interface ipv4 show excludedportrange protocol=tcp
net start winnat
netsh interface ipv4 show excludedportrange protocol=tcp
```

- [x] 美区电话

用的 [hero-sms](https://hero-sms.com/cn) ，充了 `2$` (已经是最低了，算手续费只剩下 `1.65$`), 花了 `0.5$` 整了个美区电话接验证码（全程开着美区VPN）。

## Settings

- [x] 设置智能体沙盒 : 在本机操作系统层面对 Codex 运行命令、读写文件、访问网络进行强制约束。

Codex 智能体执行任务时，通常会调用本机工具,比如：

```shell
ls
cat file.py
python test.py
npm install
sed -i ...
```

沙盒就是在启动这些子进程时，给它们套上一层操作系统级限制。

```shell
用户指令
  ↓
模型推理
  ↓
Codex 工具调用策略
  ↓
审批策略 approval_policy
  ↓
沙盒策略 sandbox_mode
  ↓
操作系统内核限制：Seatbelt / Landlock / seccomp
  ↓
真实文件系统、网络、进程
```
- [ ] 环境

参考[本地环境用于指示 Codex 如何为项目设置工作树](https://developers.openai.com/codex/app/local-environments)，Codex 将此配置存储在项目根目录下的 `.codex` 文件夹中。

