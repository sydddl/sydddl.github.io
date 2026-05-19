---
title: "mcmctree 定年 —— 使用氨基酸序列"
date: 2021-05-25T6:52:23+08:00
#draft: true
tags : [
    "mcmctree",
    "生物信息学",
    "分子定年",
    "踩坑",
]
categories : [
    "生物信息学",
    "Sci-tech",
]
---

常见定年软件
- 最大似然法：
    + r8s
    + Reltime
- 贝叶斯法：
    + PAML-MCMCTree（PAML4软件包内含）
    + 其他（Timetree）

[这里是mcmctree的手册](http://abacus.gene.ucl.ac.uk/software/MCMCtree.Tutorials.pdf)

事先声明，mcmctree使用氨基酸序列进行定年比使用核酸序列麻烦的多，而且在此之前，能找到帖子的都语焉不详，手册里的Tutorial 4是主要参考，可以先跑一遍示例文件，坑主要是体现在文件格式上。

若使用氨基酸序列来进行分析，由于mcmctree不能选择较好的氨基酸替换模型进行分析，需要自己手动运行codeml进行分析后，在生成中间文件用于运行mcmctree。若非是因为数据原因必须用氨基酸序列进行定年，还是推荐使用核酸序列。

### 前置

需求文件：(后面称其为那三个文件)
+ 多序列比对文件
+ 带有校准信息有根树
+ 控制文件 mcmctree.ctl 

前两个文件的格式很重要，参考examples里的abglobin.aa 和 abglobin.trees

![](https://gitee.com/deathsprout/giteeimage/raw/master/20210525143511.png)

![](https://gitee.com/deathsprout/giteeimage/raw/master/20210525143609.png)

树文件一定保证有至少一个时间校正点，'B(1.7,1.9)'的意思是最外面的这个分支点的时间，经化石证据矫正约束在170Mya到190Mya[^1]年之间。

[^1]: Mya:百万年,mcmctree的单位是100个百万年

多序列对比文件第一行是：    序列数 序列长 （大写的）i
下面每行是一个序列名，和后面序列对应，序列名与序列之间的有空行，数字则是表明其下的序列们第一个位置是第几位。

我将fasta文件转变为phylip后，如下图所示，序列名并没有单独成行，它的格式并不能直接用，会报错。我也没找到合适的现有格式能方便的转换过去。
我参考的其他博客都是将其转化为phylip就能正常使用，唯一的区别是他们使用核酸序列而我用的是氨基酸序列，这是我踩的坑。

![](https://gitee.com/deathsprout/giteeimage/raw/master/20210525144314.png)

数字不要紧，我在第一行后面加了个I后，除了第一行以外都能识别出，下面是识别过程的输出。
![](https://gitee.com/deathsprout/giteeimage/raw/master/20210525144715.png)

所以重点是将

G000238395 IVDLIDKVGL KDYQACCPFH NEKSPSFTVS QDKYHCFGCG ANGNAISFVM

G000263075 IVDLIDKVGL KDYQACCPFH NEKSPSFTVS QDKYHCFGCG ANGNAISFVM

这种形式变成下面那种形式

G000238395 

G000263075 

（空行）

IVDLIDKVGL KDYQACCPFH NEKSPSFTVS QDKYHCFGCG ANGNAISFVM

IVDLIDKVGL KDYQACCPFH NEKSPSFTVS QDKYHCFGCG ANGNAISFVM

数量大的话可以写个脚本，这里数量不多，用excel分割剪切出来，替换Tab键为空格再复制进去。

### 流程

首先把那三个文件放在一个目录里，修改mcmctree文件，如下图红色部分所示。

![](https://gitee.com/deathsprout/giteeimage/raw/master/20210525150931.png)
```
seqfile  # 多序列比对文件
treefile  # 带有校准信息有根树
ndata   # 输入的多序列比对的数据个数，是密码子就是3；否则设置为1
seqtype = 2    * 0: nucleotides; 1:codons; 2:AAs #数据类型(2为氨基酸)
usedata = 1    * 0: no data; 1:seq like; 2:normal approximation; 3:out.BV (in.BV) # 设置是否利用多序列比对的数据：\
#0，表示不使用多序列比对数据，则不会进行likelihood计算，虽然能得到mcmc树且计算速度飞快，但是其分歧时间结果是有问题的；\
#1，表示使用多序列比对数据进行likelihood计算，正常进行MCMC，是一般使用的参数; \
#2，进行正常的approximation likelihood分析，此时不需要读取多序列比对数据，直接读取当前目录中的in.BV文件。该文件是使用usedata = 3参数生成的out.BV文件重命名而来的。\
#此外，由于程序BUG，当设置usedata = 2时，一定要在改行参数后加 *，否则程序报错 Error: file name empty.. \
#3，程序利用多序列比对数据调用baseml/codeml命令对数据进行分析，生成out.BV文件。由于mcmctree调用baseml/codeml进行计算的参数设置可能不太好（特别时对蛋白序列进行计算时），\
#推荐自己修改软件自动生成的baseml/codeml配置文件，然后再手动运行baseml/codeml命令，再整合其结果文件为out.BV文件。
```
运行
```
mcmctree mcmctree.ctl
```
之后会生成一系列文件，删除out.BV和rst文件，将wag.dat拷贝进来，
(wag.dat在paml  dat 目录里，我是在..pamlX\paml4.9j\dat\ 里面)

打开文件tmp0001.ctl，全部替换为下列内容。
```
seqfile = tmp0001.txt
treefile = tmp0001.trees
outfile = tmp0001.out
noisy = 3
seqtype = 2
model = 2 * 2: Empirical
aaRatefile = wag.dat
fix_alpha = 0
alpha = .5
ncatG = 4
Small_Diff = 0.1e-6
getSE = 2
method = 1
```
运行
```
codeml tmp0001.ctl
```
这样就使用WAG+Gamma生成了适当的Hessian矩阵，接下来将rst2重命名为in.BV,现在可以更改mcmctree.ctl 的 usedata = 2

回到上一目录，新建一个文件夹，将 那三个文件和新生成的in.BV拷贝进去

接下来运行

```
mcmctree mcmctree.ctl
```

后续与mcmctree无关



### 参考
+ [MCMCTree tutorials](http://abacus.gene.ucl.ac.uk/software/MCMCtree.Tutorials.pdf)
+ [mcmctree估算物种分歧时间](https://www.jianshu.com/p/46b28829b078)
+ [使用PAML进行分歧时间计算](http://www.chenlianfu.com/?p=2974)