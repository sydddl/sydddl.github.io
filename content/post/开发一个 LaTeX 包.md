---
draft: true
---

# 开发一个 LaTeX 包

写一个宏包的基本工作就是将你原本很长的文档导言拷贝到一个分离的文件中去，这个文件需要以 `.sty`结尾，文件中需要使用一个专用的命令，声明宏包的名称：

```Latex
\ProvidesPackage{package name}
```
### 需求整理

- [ ] 自动识别结构 ？ 
- [ ] 结合 `PyTikZ` 和 自定义 `.sty` 开发 ？ 
- [ ] 

### 我目前做了啥

- [x] Fork HarisIqbal88 的 PlotNeuralNet 库，clone 这个分支到本地，这样就可以做修改了。

### 背景知识
- TikZ和PGF是一种用在TeX上的CLI绘图工具, CLI和GUI是两种常见的绘图方式，前者是所想即所得的，通过类编程的思想实现绘图，后者所见即所得，如PS、Illustrator。
- PGF ，全名 `portable graphics format`

### 具体命令/实现

- `\tikzset` :  TikZ 是 PGF 引擎的前端，每个 `tikzpicture` 都要选择 `tikz key family` ，`\tikzset` is a shortcut to set keys that belongs to the tikz family.
```latex
\def\tikzset{\pgfqkeys{/tikz}}
```
所以`\tikzset` 是接下来给定内容的封装器，

```latex
\tikzset{help lines/.style={blue!50,very thin}} %相当于重定义了 help lines ， /.append style 表示追加， 都是覆盖定义
```

- `\pgfmathsetmacro` {\x}{computation} 

创建一个变量 `\x` ，是后面运算的结果，例如 `{\cubey*\scale}`

- `\pgfmathparse`{computation}

暂存结果到变量 `\pgfmathresult`

- `\foreach` \var in { item1, item2, ..., itemN} { }

循环操作

> **Styling the Nodes**
> 



### 参考

- [自己动手写latex宏包](https://www.cnblogs.com/yishuiliunian/archive/2011/04/05/2005632.html)
- [如何利用 Git 与 GitHub 进行多人协作开发](https://www.jianshu.com/p/8c69d1021d98)
- [Latex--TikZ和PGF--高级文本绘图，思维绘图，想到--得到!](https://www.cnblogs.com/tsingke/p/6649800.html) 
- [Graphics in LATEX](https://tug.org/pracjourn/2007-1/beccari/beccari.pdf) 
对LaTeX常见画图工具的简单综述
- [LATEX 2ε for class and package writers](https://mirrors.cloud.tencent.com/CTAN/macros/latex/base/clsguide.pdf)
- [How to TikZ (Slides)](https://www.math.tu-berlin.de/fileadmin/i26/download/AG_ModNumDiff/FG_NumMath/seminars/toolseminar/How_to_TikZ.pdf)
- [PyTikZ – A Python interface to TikZ | GitHub](https://github.com/allefeld/pytikz)
- [PGFmanual.pdf](https://mirrors.dgut.edu.cn/CTAN/graphics/pgf/base/doc/pgfmanual.pdf)