---
title: "Perl实验课程笔记"
date: 2021-05-22T19:35:28+08:00
description: "生物信息学实验课程中解彬彬老师教授的perl语言内容，内容也不多倒是非常实用"
#draft: true
tags : [
    "课程笔记",
    "编程语言",
]
categories : [
	"笔记",
	"编程",
]
image: https://i.loli.net/2021/12/03/b5iCUtcZKnwF1BJ.jpg

---

关于笔记难免会选择性记忆，自己已然掌握的便不会记录，比如perl的正则表达式和python共通，便不记录在里面。（当然在这里更推荐去学python）

```perl
sort #对数组排序（默认按照字符串排序）
reverse #列表反向排列

sort{$a cmp $b}; #（由小到大）
sort{$b cmp $a}; #（由大到小）
sort{$a <=> $b}; #(按照数值进行排序)
sort {$h{$a}<=>$h{$b}} key %h; #对哈希 比value排key

# 用 . 可以将字符串连接
substr $总串,开始位置,长度,该内容替换被取出的内容;#用来取子串
index $字符串,"寻找的内容",起始位置;#搜寻字符串内容，返回位置
my $str = "xal" x 5; #输出xalxalxalxalxal
join "自定分隔符",@arr; #连接数组中所有的字符串
split /自定分割符/,$str; #分割字符串
```
2021/04/28

```perl
#! /usr/bin/perl
use strict;
use warnings;

my @lines; #数组的每一项对应文件的每一行
my $tmp; #依次把每一行赋值给临时变量
open IN,"testout.txt" or die "fail to open testout.txt to read:$!\n";
while ($tmp = <IN>) {
	chomp $tmp; #把字符串尾巴的换行符切掉
	print"line:|",$tmp,"|\n";
}
while (<IN>) { #因为上一个while以及把指针指到文件末尾了，此while将没有输出
	print"line:|",$_,"|\n";#临时变量
}
close IN;

open IN,"testout.txt" or die "fail to open testout.txt to read:$!\n";
while (<IN>) {
	chomp $_;
	print"line:|",$_,"|\n";#临时变量
}
close IN;

open OUT,">testout.txt" or die "fail to open testout.txt to write:$!";# $!是存放最近的出错信息的变量
print OUT "this is the 1 line.\n";
print OUT "this is the 2 line.\n";
close OUT;

my @arr = ("list");
$arr[$#arr+1] = "new";#尾添

push @arr,"new_push";#尾添
print "@arr\n";

pop @arr;#尾删
print "@arr\n";

shift @arr;#头删
print "@arr\n";

unshift @arr,"interposition";#头插入
print "@arr\n";

my $return;
$return = shift @arr;# 将@arr的头删除并将其作为返回值赋值给$return
print"$return\n @arr\n";

# $return = shift; #在运行perl文件时输入数据作为参数
# such as  perl input_output.pm 1 2 3
$return = shift @ARGV;# 通过添加@ 使其能添加多个参数
if (not $return){
	die "usage:perl arg.plx<paramter>\n";
}#没给参数，杀死并报错
print"$return\n @arr\n";


# 使得脚本可以接受文件名作为参数，打开该文件并逐行输出
my $filename;
$filename = shift;
if (not $filename){die "usage:perl arg.plx<paramter>\n";}
open IN,$filename or die "fail to open $filename to write:$!";
while (<IN>) {
	chomp $_;
	print $_,"\n";
}
close IN;
```
读取fasta文件，读取并输出序列信息和序列本身。
```perl
#! /usr/bin/perl
	use strict;
	use warnings;
	
	my $filename;
	my @seq = ();my @name = ();
	my $cnt = 0;
	my $lnbuf ;my $ch ;
	my $tmpname;my$tmpseq;
	
	$filename = shift;
	if (not $filename){die "usage:perl arg.plx<paramter>\n";}
	open IN,$filename or die "fail to open $filename to write:$!";
	while (<IN>) {
		chomp;
		$ch = substr $_,0,1;
		if ($ch eq ">"){
			if ($cnt == 0){
				$tmpname = $_; # push @name,$_;
				$cnt ++;
			}
			else{
				push @name,$tmpname;
				push @seq,$tmpseq;
				$tmpseq = "";
				$cnt ++;
				$tmpname=$_;}
		}
		else {
			$tmpseq .= $_; # 连接缩写 .=
			# @seq[$cnt-1] .= $_;
		}
	}
	close IN;
	
	if($tmpname ne ""){
		push @name,$tmpname;
		push @seq,$tmpseq;
	}
	
	foreach my $i(0 .. $#name){
		print $name[$i],"\n",$seq[$i],"\n";
	}
```
Perl的正则表达式的三种形式，分别是匹配，替换和转化:

- 匹配：m//（还可以简写为//，略去m）

- 替换：s///

- 转化：tr///

这三种形式一般都和 =~ 或 !~ 搭配使用， =~ 表示相匹配，!~ 表示不匹配。

perl处理完后会给匹配到的值存在三个特殊变量名:

+ $`: 匹配部分的前一部分字符串

+ $&: 匹配的字符串

+ $': 还没有匹配的剩余字符串

```perl
	my $str = "Hello the perl world";
	$str =~ /Hello/; #搜索  搜到返回为真
	$str =~ /Hello/ ? print "found Hello\n" : print "can't found\n" ;
	
	my $str2 = ">seq1 >seq2 >seq3";
	$str2 =~ /^>/ ? print "found > at begin\n" : last ; #  ^ 锚定到句首
	#  $ 锚定到句末， bp$ 搜索句末的bp
	
	my $str3 = "total length 10000000 bp, N50 20000 bp";
	$str3 =~ /total length (\d{1,}) bp/ ? my $tot_len = $1 : last ;  print $tot_len,"\n";
	# () 里面的值会被抓取出来，存在临时变量里 第一个放在 $1,第二个放在 $2  ...
	# \d 匹配任何Unicode十进制数（就是在Unicode字符目录[Nd]里的字符）
	## 这包括了 [0-9] ，和很多其他的数字字符。如果设置了 ASCII 标志，就只匹配 [0-9] 
	# {1,} 表示匹配次数，1次以上
	
	my $str4 = "joadfffff               
	the";
	$str4 =~ /(f*)\s*(the)/ ? my $jiaf = $1.$2 : last ;  print $jiaf,"\n";
	# \s  匹配任何Unicode空白字符（包括 [\t\n\r\f\v])
	
	# *?    '*', '+'，和 '?' 修饰符都是贪婪的,在后面加？，表示非贪婪匹配
	
	my $str5 = "01234569acespvnmgogjbtfgg";
	$str5 =~ /([0-9a-h]+)/ ? print $1,"\n" : last ;
	$str5 =~ /([^0-9a-h]+)/ ? print $1,"\n" : last ; # [^ ]表示反选字符集合
	
	my $string = '4runoooob2,57';
	$string =~ tr/a-z/A-Z/s ; print "$string\n"; # s把多个相同的输出字符缩成一个
	$string =~ tr/N/ /c ;  print "$string\n";   # 把所有非N 替换为空格
	$string =~ tr/\t //d ;  print "$string\n";   # 删除tab和空格
	$string =~ tr/0-9/ /cs ; print "$string\n"; # 把数字间的其它字符替换为一个空格。
	
```
引用：用来弄高维数据结构（实际是指针）
```perl
my $addr = \$str ; print $addr,"\t",$$addr,"\n";
	# \$str 取地址，|   ${ $addr } 解引用，也可缩写为 $$addr
	#输出：  SCALAR(0x7fffc0a84060)  Hello the perl world
	
	my @arr = (1 .. 7);
	my $addr2 = \@arr ; print $addr2,"\t","@{ $addr2 }","\t",${ $addr2 }[0],"\n";
	#  ARRAY(0x7ffff5b228c8)   1 2 3 4 5 6 7   1
	
	my %h = (a=>1,b=>2,c=>3);
	my $addr3 = \%h;  print $addr3,"\t",keys %{$addr3},"\n";
	print ref($addr),"\t",ref($addr2),"\t",ref($addr3); # ref()  返回引用的类型
	# HASH(0x7fffdae689e8)    cab
	
	my @arrac1 = split / +/,"4 -1 -4 0 1";
	my @arrac2 = split / +/,"2 -3 -4 0 1";
	my @arrac3 = split / +/,"1 -5 -4 0 1";
	my @arr_of_arr ;
	$arr_of_arr[0]= \@arrac1;$arr_of_arr[1]= \@arrac2;$arr_of_arr[2]= \@arrac3;
	foreach my $i (0 .. $#arr_of_arr){
		print $i,"\t",$arr_of_arr[$i],"\t","@{$arr_of_arr[$i]}","\n";
	}# 演示，非实际用法，麻烦的写法

	@{ $arr_of_arr[0] } = split / +/,"1 2 3 4 5"; # 省事操作
	foreach my $i (0 .. $#arr_of_arr){
		print $i,":";
		foreach my $j (0 .. $#{$arr_of_arr[$i]}){
			print " ",$j,"(",$arr_of_arr[$i][$j],")"; 
		}
		print "\n";
	}   # can  $arr_of_arr[$i] -> [$j]
	
	my $arrlist = [
	[9,8,7,6,5],
	[1,2,3,4,5],
	[5,5,5,5,5],
	]; #匿名数组
	print @$arrlist,"\t","@{@$arrlist[0]}";
```
![](https://gitee.com/deathsprout/giteeimage/raw/master/arr_of_arr.png)
上图是部分输出结果

```perl
# 计算拼接成contig的文件 N50的perl程序
use strict;
use warnings;

my @name;my @seq;my $seq; my @long ;  
my $alllong = ""; my $contig = 0 ; my $longest; my $shortest;
my $GC=0 ; my $N=0; my $N50;

open IN,"data/a9_k63.contig.long1500" or die "$!"; # a9_k63.contig.long1500
while (<IN>) {
    chomp;
    my $head = substr $_,0,1;
    if ($head eq ">"){
        if ($contig == 0){
            $contig ++;
            push @name,$_;
            }
        else{
        $contig ++;
        push @name,$_;
        $alllong .= $seq ;
        push @seq,$seq; push @long,length($seq);
        $seq = "";
        }
    }
    else{
        $seq .= $_;
    }
}
push @seq,$seq; 
$alllong .= $seq ;
push @long,length($seq);
close IN;

my @arrlong = sort{$a<=>$b} @long;
$longest = $arrlong[0];$shortest = $arrlong[$#long];

my $G = $alllong =~ tr/G/G/; 
my $C = $alllong =~ tr/C/C/;
$N = $alllong =~ tr/N/N/;
$GC = ($G+$C)/length($alllong);

my $sum =0;my $lo2 = length($alllong)/2;
foreach my $i (0 .. $#arrlong){
    $sum = $sum+$arrlong[$i];
    if ($sum > $lo2){
        $N50 = $arrlong[$i];
        last;
    }
}
print "@long\n";

print "大小：",length($alllong),"\tcontig数:",$contig,"\n";
print "最短contig：",$longest,"\t最长contig:",$shortest,"\n";
print "GC含量:",$GC,"\t\tN数量:",$N,"\n";
print "N50:",$N50,"\n";
```