---
title: "从RNA-seq到GO富集分析"
date: 2021-06-17T14:57:22+08:00
draft: true
tags : [
    "RNA-seq",
    "GO",
    "笔记",
    "富集分析",
    "软件操作",
]
categories : [
    "RNA-seq",
    "GO",
    "笔记",
    "富集分析",
    "软件操作",
]
---

本文意在综合自己的实践和其他阅读的材料，给出自己对RNA-seq的理解，重点在于梳理我自己的知识，并试图做一个干货满满的文章(想写全实在是太多了，估计做不到)。

## 什么是 RNA-seq
---
在概念上，RNA-seq是个简单的过程，RNA的产生涉及到转录过程，不同条件不同状态的样本转录产生的所有RNA的量是不完全相同的，能通过第二代测序技术得到转录组的测序数据。

>RNA-seq 的生信处理大体上可以分成下面的部分:

>+ 获取对照组和实验组的转录组测序数据。
>+ 将测序结果与基因组或转录组匹配。
>+ 计算有多少reads对齐到一个区域(如某基因)，归一化计数。
>>假设其中实验组（条件B）一个样本基因b0023对齐到10000个reads，有几次生物学重复，平均下来7774.6，而它的对照组（条件A）基因b0023仅对齐1400.18个reads，经过一些标准化处理和计算后，就可以比较条件A和条件B下该基因的表达是上调、下调或是无显著变化。那些转录水平显著变化的基因，可以称之为差异表达基因（differential expression gene）。
>+ RNA-seq 已经完成，但我们大部分时候并不对这具体的差异表达基因感兴趣，而是对那些更宏观的如代谢通路、生理功能等感兴趣，这往往关联到一大批基因，这就需要进行GO富集分析或者KEGG富集分析。

基于RNA-seq 发展了很多其他组学分析技术，如scRNA-seq（单细胞）等。

## 稍微来点背景知识
---
了解的人可以直接跳过

#### *1.术语*

**read**
>如果对第二代测序技术比较了解，这时候测序数据是短读长的片段，待测序列的一端加上已知接头，单端读取序列，即单末端测序（Single-end sequencing  SE）。
>在待测序列两端的加上接头，进行双端测序得到成对的序列，称为成对末端测序（Paired-end sequencing PE）。如PE100,是指把一段序列两端都测100bp，内部序列不测序但能知道之间的长度。这里的短读长片段叫read。
>>**read depth**: Read深度,一个样本测序得到的reads数。

>>**测序深度**:单个核苷酸被测到的次数或所有核苷酸被测到的平均深度

**index**
>测序的标签，用于测定混合样本，通过每个样本添加的不同标签进行数据区分，鉴别测序样品。

**唯一分子标识符（UMIs）**
>在扩增前，构建RNA-seq文库的时候加入的短序列或barcodes，理想情况下每条转录本结合一个唯一的标识符，含有此标识符的reads都来源于此转录本，定量时只计算一次。可以用来降低RNA-seq的定量偏好性，在RNA起始量低的单细胞实验中尤为适用。

**Contig**
>高通量测序中利用软件将具有一定长度overlap的reads连成更长的片段，这些通过reads overlap关系得到的不含N的组装片段称之为Contig。

**Mapping（映射）**
- A mapping is a region where a read sequence is placed.
- A mapping is regarded to be correct if it overlaps the true region.

**Alignment（对齐）**
+ An alignment is the detailed placement of each base in a read.
+ An alignment is regarded to be correct if each base is placed correctly.

**normalization（归一化）**
>由于 技术误差，测序深度不同，基因长度不同，为了能够比较不同的样本，比较不同的基因的表达量，以及使表达水品分布符合统计方法的基本假设，就需要对原始数据进行标准化，这对于准确比较样品之间的基因表达是必需的。
>常见到的归一化方法有RPKM/FPKM、TPM、TMM。

>**影响因素**
- **library size (文库大小会对reads counts造成影响)**
>>消除因建库样本大小造成的影响，100个细胞和10000个细胞建的库里面RNA含量肯定有差别吧。
- **library composition（文库组成）**
>>RNA-seq或者是其它的高通量测序技术通常会比较不同组织类型之间的测序数据差异,例如我们可能会比较肝脏与脾脏的差异。这个时候就可能会出现问题，因为在肝脏中，存在着某些特异性基因，这些基因只在肝脏中大量转录，而在脾脏中转录不活跃。

>**DESeq normalization**
>>DESeq归一化方法(在DESeq R包中实现)基于所有基因的中位数计数比的基因计数来估计测序深度。
**effective length（有效长度）**

**差异表达基因（Differentially Expressed Gene，DEG）**
>指在两个不同条件（如对照与处理、野生型和突变型、不同时间点、不同组织等）下，表达水平存在显著差异的基因，称之为差异表达基因。

**差异表达转录本（DifferentiallyExpressed Transcript，DET）**
>指表达水平存在显著差异的转录本。

>****


#### *2.文件格式*
**FASTQ**
>下机器的原始数据以FASTQ格式，后缀会缩写成.fq(fasta缩写为.fa)，文件一般以四行记录。
>+ @开头，介绍序列信息
>+ 序列本身的信息
>+ +开头，一般与第一行内容相同
>+ 序列质量信息   （RNA seq中会使用序列质量信息筛选掉不合格的测序）

**sam格式**
>SAM 是sequence alignment format [http://samtools.github.io/hts-specs/SAMv1.pdf] 的缩写，BAM文件是SAM的二进制文件。当测序生成的fastq文件比对到参考基因组后就会生成SAM文件或者BAM文件。大部分的数据分析都是始于SAM文件。
>SAM格式文件包括头部注释部分和比对结果部分，头部分为''可选部分''。头部分位于比对部分之前，以“@”开头。比对部分有11列是固定的，其他多列可选。

**GFF 文件**
>全称为general feature format，这种格式主要是用来注释基因组。以tab键分割的9列组成，以下为每一列的对应信息：
```
seq_id：序列的编号，一般为chr或者scanfold编号；

source: 注释的来源，一般为数据库或者注释的机构，如果未知，则用点“.”代替

type: 注释信息的类型，比如Gene、cDNA、mRNA、CDS等;

start: 该基因或转录本在参考序列上的起始位置；(从1开始，包含);

end: 该基因或转录本在参考序列上的终止位置；(从1开始，包含);

score: 得分，数字，是注释信息可能性的说明，可以是序列相似性比对时的E-values值或者基因预测是的P-values值，.表示为空(和注释来源、注释类型都有关);

strand: 该基因或转录本位于参考序列的正链(+)或负链(-)上;

phase: 仅对注释类型为“CDS”有效，表示起始编码的位置，有效值为0、12. (对于编码蛋白质的CDS来说，本列指定下一个密码子开始的位置。每3个核苷酸翻译一个氨基酸，从0开始，CDS的起始位置，除以3，余数就是这个值，，表示到达下一个密码子需要跳过的碱基个数。该编码区第一个密码子的位置，取值0,1,2。0表示该编码框的第一个密码子第一个碱基位于其5’末端；1表示该编码框的第一个密码子的第一个碱基位于该编码区外；2表示该编码框的第一个密码子的第一、二个碱基位于该编码区外；如果Feature为CDS时，必须指明具体值。)；

attributes: 一个包含众多属性的列表，格式为“标签＝值”(tag=value)，以多个键值对组成的注释信息描述，键与值之间用“=”，不同的键值用“；”隔开，一个键可以有多个值，不同值用“,”分割。注意如果描述中包括tab键以及“，= ；”，要用URL转义规则进行转义，如tab键用 代替。键是区分大小写的，以大写字母开头的键是预先定义好的，在后面可能被其他注释信息所调用。
```
>****
#### *3.指标*
**统计学指标**
> 

>**FDR**
>>即错误发现率，定义为在多重假设检验过程中，错误拒绝(拒绝真的原(零)假设)的个数占所有被拒绝的原假设个数的比例的期望值。通过控制FDR来决定P值的阈值。

>
**测序质量**（我使用的是FastQC）
>**Q 值（phred quality score）**  
>>Q=-10log_10 （P_error）。Q30表示测序碱基的精确度在99.9%

>**测序覆盖度（sequencing coverage）** 
>>Fold-80：80%的目标碱基达到平均深度所需的额外测序的倍数（平均深度/(80%以上区域被覆盖的深度)）。Fold80 越大，均一性越差，一般2以内均一性较好。

>**Duplication（冗余度指标）**
>> 比对到转录组相同位置的的测序reads的比例。大部分重复可能是高表达转录本带来的真实信号，而另一些则是由于扩增和测序偏好性造成的,一般与建库过程中过度PCR，样本投入量过低有关。

**RIN(RNA integrity number)**
>用来衡量从样品中抽提的RNA的质量，评估RNA完整性是获得有意义基因表达数据的第一个关键步骤。RIN数值范围为1-10，将真核生物总RNA质量分类，其中1代表降解最严重的情况，10代表最完整。测序公司一般推荐RIN要大于7；RIN＜6，会建议重新送样；RIN在6-7之间，存在测序风险，对于转录组测序和全转录组测序，存在文库产量低或建库失败；测序数据随机性差，可能会有偏好性；有效数据留存率低、mapping率低、基因覆盖不均匀、rRNA残留率高等风险。

**降解曲线**
>
>****

**FPKM**: expected number of Fragments Per Kilobase of transcript sequence per Millions base pairs sequenced,是每百万fragments中来自某一基因每千碱基长度的fragments数目，同时考虑了测序深度和基因长度对fragments计数的影响，是目前最为常用的基因表达水平估算方法

**RPKM**：expected number of reads Per Kilobase of transcript sequence per Millions base pairs sequenced，是每百万reads中来自某一基因每千碱基长度的reads数目。

**TPM**

**RSEM**

**FC（Fold Change）**
>即差异表达倍数。



#  实验课程实操
---

##### bowtie2
##### samtools

##### HTSeq


```R
# 命令参数
-f | --format default: sam 设置输入文件的格式，该参数的值可以是sam或bam。
-r | --order default: name 设置sam或bam文件的排序方式，该参数的值可以是name或pos。前者表示按read名进行排序，后者表示按比对的参考基因组位置进行排序。若测序数据是双末端测序，当输入sam/bam文件是按pos方式排序的时候，两端reads的比对结果在sam/bam文件中一般不是紧邻的两行，程序会将reads对的第一个比对结果放入内存，直到读取到另一端read的比对结果。因此，选择pos可能会导致程序使用较多的内存，它也适合于未排序的sam/bam文件。而pos排序则表示程序认为双末端测序的reads比对结果在紧邻的两行上，也适合于单端测序的比对结果。很多其它表达量分析软件要求输入的sam/bam文件是按pos排序的，但HTSeq推荐使用name排序，且一般比对软件的默认输出结果也是按name进行排序的。
-s | --stranded default: yes 设置是否是链特异性测序。该参数的值可以是yes,no或reverse。no表示非链特异性测序；若是单端测序，yes表示read比对到了基因的正义链上；若是双末端测序，yes表示read1比对到了基因正义链上，read2比对到基因负义链上；reverse表示双末端测序情况下与yes值相反的结果。根据说明文件的理解，一般情况下双末端链特异性测序，该参数的值应该选择reverse（本人暂时没有测试该参数）。
-a | --a default: 10 忽略比对质量低于此值的比对结果。在0.5.4版本以前该参数默认值是0。
-t | --type default: exon 程序会对该指定的feature（gtf/gff文件第三列）进行表达量计算，而gtf/gff文件中其它的feature都会被忽略。
-i | --idattr default: gene_id 设置feature ID是由gtf/gff文件第9列那个标签决定的；若gtf/gff文件多行具有相同的feature ID，则它们来自同一个feature，程序会计算这些features的表达量之和赋给相应的feature ID。
-m | --mode default: union 设置表达量计算模式。该参数的值可以有union, intersection-strict and intersection-nonempty。这三种模式的选择请见上面对这3种模式的示意图。从图中可知，对于原核生物，推荐使用intersection-strict模式；对于真核生物，推荐使用union模式。
-o | --samout 输出一个sam文件，该sam文件的比对结果中多了一个XF标签，表示该read比对到了某个feature上。
-q | --quiet 不输出程序运行的状态信息和警告信息。
-h | --help 输出帮助信息。
```

### 其他
---
##### cDNA文库
cDNA文库指的是某生物某一发育时期所转录的全部mRNA反转录形成的cDNA克隆集合。cDNA文库与基因组文库不同，基因组文库包含了cDNA文库，还包含了非转录的基因组序列，比如重复序列等。cDNA文库包含了细胞的全部mRNA信息，利于研究基因时空特异性表达、基因调控、基因功能等。cDNA文库可用于microarray或者高通量测序应用。



### 参考
---
##### 文献
- Stark R, Grzelak M, Hadfield J. RNA sequencing: the teenage years. Nat Rev Genet. 2019 Jul 24. doi: 10.1038
##### 手册
- [Introduction to DGE - ARCHIVED](https://hbctraining.github.io/DGE_workshop/lessons/02_DGE_count_normalization.html)
##### 书籍
- [RNA-Seq by Example (Dr. István Albert)](https://www.biostarhandbook.com/books/rnaseq/index.html)
- 
##### 博客
- [RNA-seq分析htseq-count的使用](https://www.cnblogs.com/triple-y/p/9338890.html)
- [基因芯片数据分析（六）：DESeq2包的基本原理](https://cloud.tencent.com/developer/article/1553526)
- [cDNA文库构建流程解读](http://www.bioon.com.cn/sub/showarticle.asp?newsid=76939)
- [RNA-Seq名词解释](https://blog.csdn.net/qq_29300341/article/details/53159200?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.base)
- [Nature重磅综述 |关于RNA-seq，你想知道的都在这](https://mp.weixin.qq.com/s?__biz=MzI5MTcwNjA4NQ==&mid=2247489699&idx=1&sn=6d2c74e720f67744c3b039c86606f64a&scene=21#wechat_redirect)
- [RNA-seq最强综述名词解释&思维导图|关于RNA-seq，你想知道的都在这（续）](https://mp.weixin.qq.com/s?__biz=MzI5MTcwNjA4NQ==&mid=2247492185&idx=2&sn=f4071e96476d9ef3feeeff06d745e2cd&chksm=ec0e27d3db79aec5f7aaa47142a46e7bb42b1d591bb98a1633e785c5e9517e17f06469034e85&cur_album_id=1335351932285976579&scene=189#rd)
- [NGS基础 - GTF/GFF文件格式解读和转换](https://mp.weixin.qq.com/s?__biz=MzI5MTcwNjA4NQ==&mid=2247484166&idx=1&sn=417e155672bd718def86003b16bf0078&scene=21#wechat_redirect)
- [SAM文件格式介绍](https://www.jianshu.com/p/ab133ee9712c)