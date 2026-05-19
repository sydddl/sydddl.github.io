---
title : "Cas Protein"
date : 2021-05-15T22:38:53+08:00
tags : [
    "CRISPR-Cas",
    "Cas9蛋白作用机制",
]
categories : [
    "非正式学术文章",
]
image: https://pica.zhimg.com/v2-ffc7d038737eaeb8f680cf26b6b45df1_1440w.jpg?source=172ae18b
#gitment = true
#draft: true
hadden: false
---

[同样发表在知乎上](https://zhuanlan.zhihu.com/p/381529366)

### 为什么写这篇文章？

这是我在为完成《蛋白质工程》课程展示任务过程中写的，选题自然是cas蛋白，我发现在网络和各类博客上反而很难找到cas9蛋白的具体内容，它被介绍CRISPR-Cas的文章淹没了，在读了十几篇文献后，用latex写了笔记。鉴于关于CRISPR-Cas的内容随处可见，完全可以去看产品手册或者其他什么，这篇文章中删去了与Cas蛋白无直接关系的大量内容。

### Cas蛋白简介

Cas是CRISPR相关蛋白的简称，
簇状规则间隔的短回文重复序列（CRISPR）和相关蛋白（Cas）构成CRISPR–Cas系统[^1]，是许多细菌和大多数古细菌中存在的抗噬菌体免疫系统。 近年来，CRISPR–Cas系统已发展成为可靠而强大的基因组编辑工具。
[^1]:本文不介绍与Cas蛋白无关的CRISPR–Cas技术细节，有兴趣的详见 [CRISPR Guide](https://www.addgene.org/guides/crispr/)

![CRISPR系统的三大类和十个子类的关系](https://gitee.com/deathsprout/giteeimage/raw/master/crisper.png)
上图1表示有三大类的CRISPR系统（I、II、III），而又可以细分为十个子类，可以看出在CRISPR系统中含有很多种类的Cas蛋白，Cas1到Cas10以及如Cas8a1、Cas12a、Cas13a等比较稀少的类型。[^2]
[^2]:Makarova K S , Haft D H , Barrangou R , et al. Evolution and classification of the CRISPR–Cas systems[J]. Nature Reviews Microbiology.

第2类CRISPR-Cas系统的特征是由单一、大、多结构域的蛋白质组成复合体发挥作用，一些2类效应蛋白，如Cas9,已经成功地用于基因组工程。[^3] 
[^3]:Makarova K S , Zhang F , Koonin E V . SnapShot: Class 2 CRISPR-Cas Systems[J]. Cell, 2017, 168(1-2):328-328.

![基于不同的效应蛋白家族,CRISPR II类系统又可细分为3类、9亚型](https://gitee.com/deathsprout/giteeimage/raw/master/239.png "基于不同的效应蛋白家族，第2类系统可分为三大类和九个亚型")

![不同种类的Cas蛋白数量分布](https://gitee.com/deathsprout/giteeimage/raw/master/cas_number.png)
上图3则是CasPDB数据库统计的在不同种类的菌体中所含的Cas蛋白数量分布，在统计的所有 Cas 蛋白中，Cas1占20.11\% ，cas2占17.46\% ，Cas3、 Cas5和 Cas6占10\% 以上。其余的较少，最著名的Cas9蛋白则在所有Cas蛋白中占2.59\%，本文也将重点讨论Cas9蛋白。

### Cas9蛋白结构

#### Cas9蛋白立体结构

![](https://gitee.com/deathsprout/giteeimage/raw/master/jie.png)
SpCas9 -ABE8e 结构, 是使用PyMOL将从PDB数据库中获取的Cas9结构简单的可视化后的图像。[^4]
[^4]:DOI: 10.2210/pdb6VPC/pdb

![](https://gitee.com/deathsprout/giteeimage/raw/master/jie2.jpg)
3.2 A分辨率下的SpCas9-ABE8e复合物的冷冻电镜建模结构,
SpCas9-ABE8e 冷冻电镜结构：SpCas9灰色显示； sgRNA紫色； 靶链DNA蓝绿色； 非靶链DNA蓝色； TadA-8e二聚体红色和粉红色显示。 Cas9 N端与TadA-8e C端之间的直线距离显示为橙色虚线。

#### Cas9蛋白结构域

![](https://gitee.com/deathsprout/giteeimage/raw/master/domain.png)
Cas9蛋白结构域分为REC结构域、Ruvc结构域、HNH结构域、PI结构域,图6还特地将富含精氨酸的alpha螺旋用紫色标示出来。其中REC结构域可以分为REC1-A、REC2、REC1-B、REC3，作用是连接其他结构域。Ruvc结构域分为三个结构域，彼此之间并不靠近。

![](https://gitee.com/deathsprout/giteeimage/raw/master/PAM.png)
a是靶链DNA和sgRNA结合示意图。b是sgRNA-靶链DNA连接的正交视图。c是Cas9–sgRNA–DNA复合体的前视图和后视图。
在上图7中，RNA均为橙色，靶链DNA为浅蓝色，非靶链DNA为黑色。 非靶链中的5'-NGG-3'PAM三核苷酸以黄色突出显示。图中蛋白质上不同的颜色显示了不同的结构域。[^5]
[^5]:Anders, Carolin, Niewoehner, et al. Structural basis of PAM-dependent target DNA recognition by the Cas9 endonuclease.[J]. Nature, 2014.

### Cas9蛋白功能及作用机制

#### Cas9蛋白在CRISPR-Cas系统中的功能
<div align=center>
<img src="https://gitee.com/deathsprout/giteeimage/raw/master/type2.png" width = 70% height = 60%>
</div>

上图8显示的是Type II 系统的作用过程。[^3] 
首先是依靠Cas1、2复合体将原间隔序列的片段制作成间隔序列整合进CRISPR序列中，形成对外源DNA的记忆，然后在接下来的免疫中，间隔序列就能快速产生原间隔序列片段，这些序列片段与Cas9一起靶向地切除在细菌内的外源DNA，PAM在这里参与防止自身免疫疾病的机制，保障不会切除自身DNA。

### Cas9蛋白功能的机制


#### 结合DNA并识别目标序列


![](https://gitee.com/deathsprout/giteeimage/raw/master/F3.jpg)
上图是SpCas9寻找目标位点的步骤示意图，apo-Cas9 与DNA相互作用靠的是随机碰撞，结合后很快分开。在apo-Cas9 与sgRNA 结合后形成Cas9 RNP，使SpCas9发生形态和功能改变，允许目标通过沿双链DNA 的进行单向搜索约27个bp（非靶链DNA-3'至5'），除非遇到PAM，否则就快速的与DNA分离。与 PAM 的稳定相互作用则促使互补底物上稳定R环的形成，从而激活 Cas9以进行 DNA 切割。[^6] 
[^6]:Lapinaite A , Knott G J , Palumbo C M , et al. DNA capture by a CRISPR-Cas9–guided adenine base editor[J]. ence, 2020, 369(6503):566-571.

就这样借助sgRNA的部分序列与靶DNA位点进行碱基配对，能够引导Cas9结合到这个靶位点上并进行切割。II型和V-B型系统需要tracrRNA才能正常发挥功能，而V-A型系统则需要单独使用crRNA 。在实际应用时，人们可以将tracrRNA和crRNA作为两种向导RNA（gRNA）或者融合在一起形成单向导RNA（single guide RNA, sgRNA），后一种方式已经广泛用于Cas9引导酶Cas9结合到靶DNA序列上并进行切割，Cas9与sgRNA一起被称作Cas9-sgRNA系统。

![](https://gitee.com/deathsprout/giteeimage/raw/master/PAM2.png)
a.Cas9中PAM结合区的放大视图。b.Cas9与PAM相互作用示意图,红圈表示桥接水分子。c.主要凹槽的详细视图。 与GG-PAM的特异性氢键相互作用用虚线表示。
非靶链中的 dG2 * 和 dG3 * 的鸟嘌呤核酸碱基在主槽中分别被 arg1333和 arg1335的碱基特异性氢键相互作用读出，
这些氢键来自 Cas9 C末端的 $\beta$发夹，而与PAM互补的靶链核苷酸不被主沟槽相互作用识别。与PAM相互作用的精氨酸残基在Cas9序列中是保守的。[^7] 
[^7]:Anders, Carolin, Niewoehner, et al. Structural basis of PAM-dependent target DNA recognition by the Cas9 endonuclease.[J]. Nature, 2014.

![](https://gitee.com/deathsprout/giteeimage/raw/master/+1P.png)
<div style="float:left;border:solid 1px 000;margin:2px;">
<img align="left"
src="https://gitee.com/deathsprout/giteeimage/raw/master/+1P2.png" width = 50% height = 50% />

残基Glu 1108和ser 1109与靶DNA链中的+1磷酸二酯基团相互作用，直接导致PAM上游的局部链分离，将靶链转为与sgRNA结合（1-2bp），靶链DNA与sgRNA之间的碱基配对促进了引导-靶标异质双链的进一步逐步置换和传播。[^7] 
</div>

<br/><br/>

##### 切割

Cas9的基因组编辑能力只有在被称作前间隔序列邻近基序(protospacer adjacent motif, PAM)的短片段DNA序列的存在下才成为可能。只有DNA靶位点附近存在PAM时，Cas9才能进行准确切割。PAM的存在也是激活酶Cas9所必需的，PAM 序列的特征为 NGG（其中 N 为任意核苷酸）。[^8] 
[^8]:Nierzwicki U , Arantes P R , Saha A , et al. Establishing the allosteric mechanism in CRISPR‐Cas9[J]. Wiley Interdiplinary Reviews: Computational Molecular ence.

核酸酶Cas9含有两个具有切割活性的结构域：HNH结构域和RuvC结构域，其中
HNH结构域切割与crRNA互补的DNA链，而RuvC结构域切割非互补链(即HNH和
RuvC结构域协同切割TS和NTS)。RuvC结构域可分为三个亚结构域：RuvC I、RuvC II和RuvC III，RuvC I接近于Cas9的氨基端，RuvC II和RuvC III位于HNH结构域的两侧（见图5）。[^9]
[^9]:Huai C , Li G , Yao R , et al. Structural insights into DNA cleavage activation of CRISPR-Cas9 system[J]. Nature Communications, 2017, 8(1):1375.

当Cas9与靶基因位点结合时发生了构象变化，核酸酶功能区对靶标DNA的反向链进行定位切割。Cas9介导的DNA切割最后结果是目标DNA（PAM序列上游约
3～4个核苷酸）的双链断裂（double strand break，DSB）。
![](https://gitee.com/deathsprout/giteeimage/raw/master/weidian.png)
上图是切割位置示意图，实验中观察到的切割位点由黑色（HNH）和蓝色（RuvC）三角形标记（蓝色填充:首选位点，无填充:典型位点(较不利)，青色填充:不利位点）。[^10]
[^10]:Stephenson A , Raper A T , Suo Z . Bidirectional Degradation of DNA Cleavage Products Catalyzed by CRISPR/Cas9[J]. Journal of the American Chemical Society, 2018, 140(10):3743.

传统的Cas9蛋白包含RuvC和HNH两个催化结构， RuvC和HNH可分别剪切DNA的两条链形成双链断裂，如gRNA与非目标区域结合就有造成不必要插入突变的可能。

### 其他

#### 存在的问题

基因组极为复杂，gRNA可能与非靶向序列局部匹配，这种局部匹配也会激活Cas
9内切酶活性，从而产生脱靶效应。[^11]
[^11]:Kuscu, C., Arslan, S., Singh, R., Thorpe, J. \& Adli, M. Genome-wide analysis reveals characteristics of off-target sites bound by the Cas9 endonuclease. Nat Biotechnol 32, 677-683, doi:10.1038/nbt.2916 (2014)

此外，Cas9不仅识别标准PAM，也可识别非标准PAM，这也可能会引起一定程度的脱靶。脱靶可能影响正常基因的功能表达，甚至激活致癌因子、抑制抑癌基因，造成安全隐患，这极大的阻碍了该技术在临床的进一步应用。目前主要通过优化gRNA
设计，改造Cas9蛋白，使用RNP递送方式等策略来提高CRISPR/Cas9系统特异性以降低脱靶现象。

#### Cas9蛋白突变体

博德研究所的研究人员对Cas9进行突变使RuvC和HNH两个催化结构域中的一个缺失核酸酶活性形成Cas9n，Cas9n与DNA双链作用时仅产生单链切口。应用CRISPR/Cas9n系统进行基因编辑需要使用两个相邻且相反链上的gRNA序列。虽然所用的每条gRNA的脱靶结合位点可能出现在全基因组范围内，但是Cas9n仅催化每个位置的单链断裂 （Single-strand break，SSB）。SSB优先通过HDR进行修复，而不是NHEJ，这可降低不必要的插入缺失突变的发生。应用CRISPR/Cas9n系统进行基因编辑能将脱靶活性降低50-1000倍。[^12]
[^12]:Slaymaker, l. M. et al. Rationally engineered Cas9 nucleases with improved specificity. Science 351, 84-88, doi:10.1126/science.aad5227 (2016)