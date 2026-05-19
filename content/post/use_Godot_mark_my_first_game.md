---
title: "Use_Godot_mark_my_first_game"
description: 按着Godot手册做了第一个2D游戏之后，想要试水独自整一个2D rogue。
date: 2022-03-05T21:54:13+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: true
---

### TODO
- [ ] 开始界面
- [ ] Player
  - [x] 默认旋转 （）
  - [ ] 

### 实现

#### 基本机制
- [ ] 操作
  - [x] 位移
- [ ] 血条
- [ ] 等级
- [ ] 武器
  - [ ] 发射子弹

#### rogue

### 写的bug
> Player一移动就消失 
`_process(delta: float)` 中 `position += velocity * speed * delta` 之前忘记乘 `delta`

> 子弹跟随Player移动和旋转
