---
title: "旧物 | 大三实习报告 | PDF嵌入"
description: 大概是一份很棒的游记
date: 2022-03-11T11:52:08+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: false
---

{{< embed-pdf url="post/实习报告/实习报告ya.pdf" >}}

-------------------------------------------------
> PS ：如何实现 ？
> 使用 [hugo-embed-pdf-shortcode](https://github.com/anvithks/hugo-embed-pdf-shortcode) ，按照它写的来，但注意以下内容：
> - url 路径一定搞清楚，比如我放在markdown文件旁边，`url="post/实习报告/实习报告.pdf"`
> - 把shortcode文件 embed-pdf.html 的第一行改成 `<script src= '/js/pdf-js/build/pdf.js'></script>`