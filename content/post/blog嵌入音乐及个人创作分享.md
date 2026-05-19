---
title: "Blog嵌入音乐及个人创作分享"
description: 自动播放警告！ 🎻
date: 2022-05-07T18:07:35+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
---

<meting-js server = "netease" type="song" id="578090" preload="auto" loop="all" volume=0.25 autoplay=true list-folded=true></meting-js>

## 怎么实现

- APlayer 是一个简洁漂亮、功能强大的 Html5 音乐播放器
- MetingJS 是为 APlayer 添加网易云、QQ音乐等支持的插件

需要在 `\layouts\partials\head\head.html`里添加
```html
<!-- require APlayer -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.css">
<script src="https://cdn.jsdelivr.net/npm/aplayer/dist/APlayer.min.js"></script>
<!-- require MetingJS -->
<script src="https://cdn.jsdelivr.net/npm/meting@2.0.1/dist/Meting.min.js"></script>
```

## 如何使用

#### 从已有音乐播放器引用

这种方式非常简单，比如下面代码的效果就是未闻花名李让的口琴曲子
```html
<meting-js server = "netease" type="song" id="31209775" list-folded=true></meting-js>
```
<meting-js server = "netease" type="song" id="31209775" list-folded=true></meting-js>

```s
server = "netease" # 表示音乐来自网易云
type = "song" # 表示歌曲
id = "xxxxx"  # 是歌曲分享时复制链接里的id ，这里  https://music.163.com/song?id=31209775&userid=412281089 就是 song?id= “31209775”
```

- server 指定调用的 API ，可选 netease, tencent, kugou, xiami, baidu ，分别对应网易云音乐、QQ音乐、酷狗音乐、虾米音乐、百度音乐
- type 指定调用类型，可选 song, playlist, album, search, artist ，分别对应单曲、歌单、专辑、搜索结果、艺术家
- id 指定调用的 id ，一般可以在地址栏中找到

下面是我2020/2021年网易云生成的歌单，可以看到效果如下：
<meting-js server = "netease"  type="playlist" id= "5402207110" list-folded=false></meting-js>

<meting-js server = "netease"  type="playlist" id= "7155378453" list-folded=false></meting-js>

（这样还能越过网易云对港/台/国外ip的屏蔽，在屏蔽网易云的局域网内挂vpn听）

#### 自己编写的音频怎么分享？

稍微麻烦一些，个人理解有两种方式

- 一是申请各种音乐网站的音乐人，这样就有权在上面发自己的音频，流程就和上面一样了。好处是引用代码短，缺点是成为音乐人要签一些他们的合同，而且是有审核的，至少有不错的东西提交审核，时间也可能比较长。（对于我这种菜鸡加懒汉，写一个正常长度的曲子属于奢望 😤 ）
- 二是将音频托管到类似图床的东西里， 我这里用的是[Gimhoy音乐盘](https://music.gimhoy.com/)，缺点是加载慢，用起来代码比较长（因为大部分复制粘贴所以还凑合）

<div id="aplayer"></div>
<script src="APlayer.min.js"></script>
<script>
    const ap = new APlayer({
        container: document.getElementById('aplayer'),
        fixed: false,                //是否附着页面底部，否为false
        autoplay: true,             //是否自动播放，否为false,移动端不能生效
        volume: 0.6,                //初始音量（0~1）
        lrcType: 3,                 //歌词模式（1、HTML模式 2、js模式 3、lrc文件模式）
        mutex: true,                //互斥模式：阻止多个播放器同时播放，当前播放器播放时暂停其他播放器
        order: 'random',            //音频循环顺序（'list'：顺序, 'random'：随机）
        preload: 'auto',            //预加载（'none'：不预加载, 'metadata'：元数据, 'auto'：自动）
        listFolded: false,          //列表默认折叠，开启为true
        theme: '#ee8243',           //主题颜色
        audio: [{
            name: '低唤醒',           //歌曲名称
            artist: 'DeathSprout',       //歌曲作者
            url: "https://link.jscdn.cn/sharepoint/aHR0cHM6Ly8xZHJpdi1teS5zaGFyZXBvaW50LmNvbS86dTovZy9wZXJzb25hbC9zdG9yXzFkcml2X29ubWljcm9zb2Z0X2NvbS9FUkp3V1AtU2NxcE91dktveG1VNUhDc0JJQmwyT3lOU19pMUl3anBERWNwbE5n.m4a" ,         //歌曲源文件地址
            cover: "https://s2.loli.net/2022/05/13/Y84ArkwgyR3vK9O.png",     //歌曲封面地址
            lrc:  'lrc.lrc',        //歌曲的歌词文件
            theme: '#eeeeee'        //主题颜色（优先）
        }]
    });
</script>

对应的代码，可以自定义音乐标题、封面啥的。（本人渣作 😳 ，属于是猩猩在键盘上反复敲）

```html
<div id="aplayer"></div>
<script src="APlayer.min.js"></script>
<script>
    const ap = new APlayer({
        container: document.getElementById('aplayer'),
        fixed: false,                //是否附着页面底部，否为false
        autoplay: true,             //是否自动播放，否为false,移动端不能生效
        volume: 0.6,                //初始音量（0~1）
        lrcType: 3,                 //歌词模式（1、HTML模式 2、js模式 3、lrc文件模式）
        mutex: true,                //互斥模式：阻止多个播放器同时播放，当前播放器播放时暂停其他播放器
        order: 'random',            //音频循环顺序（'list'：顺序, 'random'：随机）
        preload: 'auto',            //预加载（'none'：不预加载, 'metadata'：元数据, 'auto'：自动）
        listFolded: false,          //列表默认折叠，开启为true
        theme: '#ee8243',           //主题颜色
        audio: [{
            name: '低唤醒',           //歌曲名称
            artist: 'DeathSprout',       //歌曲作者
            url: "https://link.jscdn.cn/sharepoint/aHR0cHM6Ly8xZHJpdi1teS5zaGFyZXBvaW50LmNvbS86dTovZy9wZXJzb25hbC9zdG9yXzFkcml2X29ubWljcm9zb2Z0X2NvbS9FUkp3V1AtU2NxcE91dktveG1VNUhDc0JJQmwyT3lOU19pMUl3anBERWNwbE5n.m4a" ,         //歌曲源文件地址
            cover: 'cover.jpg',     //歌曲封面地址
            lrc:  'lrc.lrc',        //歌曲的歌词文件
            theme: '#eeeeee'        //主题颜色（优先）
        }]
    });
</script>
```


## 参考

- [APlayer & MetingJS 音乐播放器使用指南](https://www.ottoli.cn/wordpress/anm)

- [记录一次折腾Aplayer播放器](https://www.back2me.cn/skills/Aplayer.html)