---
title: "Blog更改日志"
date: 2021-12-06T22:31:49+08:00
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

- [x] 从 **HugoTex** theme 更换到 **stack** theme
- [x] 更改 点击home会在新页面弹出 ——> 本页刷新
- [ ] 更改文章的归档类别，精简 categories
- [ ] Google 百度等收录网页
- [x] 更换挂载服务商 
    
    ~~垃圾gitee~~（ gitee page 显示index.json可能含有违规内容，删去，导致blog的搜索功能失效 ）

- [x] 添加字数统计
  - [x] 单文章字数统计
  - [x] footer 总字数和文章数统计 

- [x] 添加音乐

- [ ] 添加更多`Markdown`支持
  
    <details>
        <summary> shortcode </summary>
    {{< align center "文字居中" >}}
    </details>

  - [ ] 支持表情
  - [ ] 支持github格式的 !!! note 和 !!! danger
  - [ ] github 风格的 chackbox ，明显的蓝色 √
  - [ ] 将KaTex支持升级
  - [x] 支持使用 Font awesome  <i class="fa-solid fa-font-awesome"></i> 

- [ ] 页面美化
  - [x] 折叠式侧边栏
  - [ ] 字体
    - [x] 更换字体
    - [x] 调整字体大小

  - [ ] 颜色
    - [ ] 页面配色  
      - [x] 底色
      - [x] 滑块颜色
    - [x] [link 颜色]()
    - [x]  ~~赛博朋克块阴影~~ (不要了)

  - [x] 头像与名称居中显示
  - [ ] 调整页面比例，比较喜欢缩放到80%~90%
  - [x] 增加网站icon
  - [x] 使用物色到的icon
    - [x] 字数统计、显示时间的icon


  - [x] 修改 card 圆角 10 $\Rightarrow$ 7
  - [x] 圆角 tag `--tag-border-radius: 4px;` $\Rightarrow$ 20

- [ ] 社交
  - [ ] 编写自己的 about
  - [x] 添加评论功能 
  - [ ] 完善其他跳转信息，如脸书~~可是我没有~~、知乎等账号链接
  - [ ] 添加友链


## 记录 <i class="far fa-sticky-note"></i>

### **HugoTex** theme 更换到 **stack** theme
### 添加字数统计

**单文章字数统计** : 
借鉴小球飞鱼，改`layouts\partials\article\components\details.html`
```html
        {{ if .Site.Params.article.readingTime }} 
            <div>
                {{ partial "helper/icon" "文本" }} 
                <time class="article-words">
                    {{ .WordCount }}字
                </time>
            </div>
```
并在`config.yaml`设置`hasCJKLanguage: true`

**总字数和文章数统计** : [Hugo 总文章数和总字数](https://immmmm.com/hugo-total-count/)

### 页面美化
#### 字体
##### 更换字体

[看对应部分](https://mantyke.icu/2021/f9f0ec87/#%E4%BF%AE%E6%94%B9%E7%BD%91%E7%AB%99%E5%AD%97%E4%BD%93)，简单的复制粘贴
(不过之后想换成方正新书宋或者冬青黑，英文标题部分用贝连体)

##### 调整字体大小
位置 `assets\scss\variables.scss` 中

`--article-font-size: 1.7rem;`  $\Rightarrow$ 1.9

### 颜色
#### 页面配色 

**底色** :
- light ☞ 改了一圈 仅将背景色弄浅了一点
- dark  ☞ `--body-background: #1e202b;`  颜色是从中国传统色漆黑调浅

**滑块颜色**:
- light 
`--scrollbar-thumb: hsla(170, 94%, 55%, 0.575);`
- dark
`--scrollbar-thumb: #c91f37;`   赫赤色
####  [link 颜色]()


从灰色改为 `--link-background-color: 1, 255, 179;`

####  赛博朋克块阴影
```scss
--shadow-l1: 1.4px -1.4px 0px rgba(67, 255, 230, 0.719), 0px 3px 10px rgba(0, 0, 0, 0.04), 0px 1.4px 1px rgba(253, 25, 113, 0.397);
``` 

### 更多markdown支持

####  <i class="fa-solid fa-icons"></i>

<svg class="icon" aria-hidden="true" width =5em>
    <use xlink:href="#icon-hebaodan"></use>
</svg>
<svg class="icon" aria-hidden="true" width =5em>
    <use xlink:href="#icon-xihongshi"></use>
</svg>
<svg class="icon" aria-hidden="true" width =5em>
    <use xlink:href="#icon-niuyouguo"></use>
</svg>
<svg class="icon" aria-hidden="true" width =5em>
    <use xlink:href="#icon-duorou"></use>
</svg>
<svg class="icon" aria-hidden="true" width =5em>
    <use xlink:href="#icon-github"></use>
</svg>
<svg class="icon" aria-hidden="true" width =6.5em>
    <use xlink:href="#icon-yueqiugongzhuanSVG"></use>
</svg>


在文档末添加如下，就可以使用 Font awesome 5 的非Pro icon , 使用方式也很便捷，直接在Font awesome 里面找，点复制粘贴过来就可以了。
```html
<head> 
    <script defer src="https://use.fontawesome.com/releases/v5.11.0/js/all.js"></script> 
    <script defer src="https://use.fontawesome.com/releases/v5.11.0/js/v4-shims.js"></script> 
</head> 
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.11.0/css/all.css">
```
不花钱 <i class="fa-solid fa-dollar-sign"></i> 没必要使用Font awesome 6 ，不如去阿里弄得[iconfont](https://www.iconfont.cn/collections/index?spm=a313x.7781069.1998910419.d2b281273&type=2&sortType=favorite&page=9) 去淘 icon，下面是后来的配置，两方面都能使用。

```html
<head>
    <script src="https://kit.fontawesome.com/cbd1a514ae.js" crossorigin="anonymous"></script> #Font awesome 的kit，注册后创建一个
    <link rel="stylesheet" href="http://at.alicdn.com/t/font_3178786_wcuwkyqh11.css"> #iconfont 账号的项目，注册后创建一个
</head>
```
但是有一些问题，比如
- 1. 不能很好的显示彩色icon (包括font awesome)，后面的两个icon是下面彩色icon现在的显示情况。 <i class="iconfont icon-pinglun"></i> <i class="iconfont icon-10"></i>

<div>
  <svg class="icon" aria-hidden="true">
      <use xlink:href="#icon-xinxi"></use>
  </svg>
  <svg class="icon" aria-hidden="true">
      <use xlink:href="#icon-10"></use>
  </svg>
</div>

- 2. 每次往iconfont项目库里添加新icon，都要更新项目库，把项目库新地址复制过来，有点麻烦。

```html

# 在head里添加
<script src="https://at.alicdn.com/t/font_3178786_idyehkf29xd.js"></script>

----------------

# 上面两个彩svg的引用代码
<div>
  <svg class="icon" aria-hidden="true">
      <use xlink:href="#icon-xinxi"></use>
  </svg>
  <svg class="icon" aria-hidden="true">
      <use xlink:href="#icon-10"></use>
  </svg>
</div>

----------------

<style type="text/css">
    .icon {
       width: 1em; height: 1em; # 可以通过这种方式控制icon大小
       vertical-align: -0.15em;
       fill: currentColor;
       overflow: hidden;
    }
</style>
```

> <i class="fa-solid fa-angles-right fa-3x fa-pull-left" style="color:black"></i>
> 如果是做前端，还稍微可以接受。对于写markdown笔记，这么做实在是太麻烦了点(强迫症震怒)，看到有使用 vue <i class="fa-brands fa-vuejs"></i> 封装[简化操作的](https://www.jianshu.com/p/f14982b2b737)（有一点探索过头了，活该是有追求的前端干的事）。 但无论怎么说，在markdown里用这么多HTML语句和初衷有些本末倒置。 <i class="fa-solid fa-angles-left fa-3x fa-pull-right" style="color:black"></i> 

#### Shortcode

> ⭕ 能用  |  ❌ 一番尝试后失败

一开始想将[Feelit](https://feelit.khusika.com/zh-cn/theme-documentation-extended-shortcodes/#11-script)上的shortcode抄过来，具体是`admonition`⭕ `music` `typeit`，把`\layouts\shortcodes\`复制进来没法正常用，[自定义 Hugo Shortcodes 简码](https://guanqr.com/tech/website/hugo-shortcodes-customization/)写的很详细，搬过来`align`⭕ `quote`⭕,

{{< quote >}}
水水水
{{< /quote >}}

{{< admonition type=tip title="This is a tip" open=false >}}
一个 **技巧** 横幅
{{< /admonition >}}

[Typeit](https://www.typeitjs.com/docs/vanilla)❌

<p id="simpleUsage"></p>

<script>
new TypeIt("#simpleUsage", {
  strings: "This is a simple string.",
  speed: 50,
  waitUntilVisible: true,
}).go();
</script>

### 评论功能

吆西，直接参照[waline文档](https://waline.js.org/guide/get-started.html#vercel-%E9%83%A8%E7%BD%B2-%E6%9C%8D%E5%8A%A1%E7%AB%AF)，先注册个 LeanCloud , 注意是国际版，然后再GitHub登录下Vercel ，是从 快速上手--Vercel 部署 点击过去（球球网你让我登录！）

waline文档 快速上手中 除了 HTML引入(客户端) 以外的照着做就好，之后在`config.yaml` 里改[前端配置](https://waline.js.org/reference/client.html#lang)

```yaml
    comments:
        enabled: true
        provider: waline

        waline:
            serverURL: //你通过vercel获得的waline服务器地址
            lang: 'zh-CN'
            visitor: true
            avatar: 
            emoji:
                - https://cdn.jsdelivr.net/gh/walinejs/emojis/weibo
                - https://cdn.jsdelivr.net/gh/norevi/waline-blobcatemojis@1.0/blobs
                - https://cdn.jsdelivr.net/gh/norevi/blob-emoji-for-waline@2.0/blobs-gif

            requiredMeta:
                - name
                - email
                - url
            placeholder:
            locale:
                admin: Admin
```
更多的（表情包、修改黑暗模式和表情包大小）移步到参考链接，基本上是复制粘贴

## 参考 

[小球飞鱼 | Hugo | 看中 Stack 主题的归档功能，搬家并做修改](https://mantyke.icu/2021/f9f0ec87/#%E4%BF%AE%E6%94%B9toc%E6%A0%B7%E5%BC%8F)

[CSS（层叠样式表）](https://developer.mozilla.org/zh-CN/docs/Web/CSS)

[中国传统颜色手册](https://colors.ichuantong.cn/)

[小球飞鱼 | Hugo | 为 Blog 增加评论区](https://mantyke.icu/2021/comment/)

[waline文档](https://waline.js.org/guide/get-started.html#vercel-%E9%83%A8%E7%BD%B2-%E6%9C%8D%E5%8A%A1%E7%AB%AF)

[No.revi | Hugo｜引入外部字体](https://www.norevi.icu/2021/hugo%E5%BC%95%E5%85%A5%E5%A4%96%E9%83%A8%E5%AD%97%E4%BD%93/)

[No.revi | Waline｜添加自定义表情](https://www.norevi.icu/2021/waline%E6%B7%BB%E5%8A%A0%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A1%A8%E6%83%85/)

[在 Hugo 主题 Stack 中轻松配置 Waline 评论系统](https://ayx.moefox.tech/waline/)

[花裤衩coder | 手摸手，带你优雅的使用 icon](https://www.jianshu.com/p/f14982b2b737)

[Hugo嵌入音乐播放器](https://mrbruce516.top/2020/011/)

[APlayer & MetingJS 音乐播放器使用指南](https://www.ottoli.cn/wordpress/anm)

[探究几种CSS视差动画实现方案及原理](https://segmentfault.com/a/1190000042671680)

[Zdog - Celeste snowglobe 的样例](https://codepen.io/desandro/pen/RQeYYp)

<head>
    <!-- Place your kit's code here -->
    <script src="https://kit.fontawesome.com/cbd1a514ae.js" crossorigin="anonymous"></script>
    <script src="https://at.alicdn.com/t/font_3178786_onqo4ppg8m.js"></script>
    <script src="https://unpkg.com/typeit@8.2.0/dist/index.umd.js"></script>
    <link rel="stylesheet" href="http://at.alicdn.com/t/font_3178786_onqo4ppg8m.css">
</head>