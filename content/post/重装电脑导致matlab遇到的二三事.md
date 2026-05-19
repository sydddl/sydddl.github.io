---
title: "重装电脑导致matlab遇到的二三事"
date: 2021-09-26T19:16:13+08:00
#draft: true
tags : [
    "麻烦",
    "踩坑",
    "解决方法记录",
]
categories : [
  "麻烦解决记录",
]
---

## 问题描述

在一次夜间放着跑程序时被母上拔掉了电源，应当是电脑启动了系统自动更新导致系统损坏，蓝屏折腾好久最终还是重装了系统。最近需要使用matlab上的工具包matconvnet，过程中先后遇到了如下两个问题。

- ### 问题一
>> mex -setup

>>错误使用 mex
未找到支持的编译器。您可以安装免费提供的 MinGW-w64 C/C++ 编译器；请参阅安装 MinGW-w64 编译器。有关更多选项，请访问
https://www.mathworks.com/support/compilers。

重装系统之前，c的编译器电脑中是有MinGW以及vscode的，观察环境变量所指的位置仍然有这些文件，（但注册表那边肯定是无了）尝试重新安装了一个MinGW并配置好环境变量，仍然无用。一开始是怀疑配置文件中编辑器的版本和地址与实际情况不符，也因为我的matlab是2018b，顺手删了更新下2020a，发现问题仍然存在。

详细报错
```matlab
>> mex -setup -V
详细模式已开。

略

... 正在查找编译器 'MinGW64 Compiler (C)'...
... 正在查找环境变量 'MW_MINGW64_LOC'...否。
找不到已安装的编译器 'MinGW64 Compiler (C)'。

略
```
可以看出matlab找MinGW编辑器是以MW_MINGW64_LOC为环境变量名的,之前我都是将它的环境变量放到path里面去,在重新创建一个名为MW_MINGW64_LOC的环境变量,值设为新路径之后(D:\for_sci\new_minGW\mingw64)。

```matlab
>> setenv('MW_MINGW64_LOC','D:\for_sci\new_minGW\mingw64')
>> mex -setup
MEX 配置为使用 'MinGW64 Compiler (C)' 以进行 C 语言编译。

要选择不同的语言，请从以下选项中选择一种命令:
 mex -setup C++ 
 mex -setup FORTRAN
MEX 配置为使用 'MinGW64 Compiler (C++)' 以进行 C++ 语言编译。
MEX 配置为使用 'MinGW64 Compiler (C++)' 以进行 C++ 语言编译。
```

- ### 问题二
```matlab
>> vl_compilenn('verbose', 1)
警告: 以后的版本中将会取消对 toolboxdir('distcomp') 的支持。请改用 toolboxdir('parallel')。 
> In toolboxdir (line 49)
  In vl_compilenn (line 367) 
警告: CL.EXE not found in PATH. Trying to guess out of mex setup. 
> In vl_compilenn>check_clpath (line 650)
  In vl_compilenn (line 426) 
'cl.exe' 不是内部或外部命令，也不是可运行的程序 
或批处理文件。 
错误使用 vl_compilenn>check_clpath (line 656)
Unable to find cl.exe

出错 vl_compilenn (line 426)
    cl_path = fileparts(check_clpath()); % check whether cl.exe in path
```

这里是缺少'cl.exe'，观察我所使用的vscode，发现其并没有'cl.exe'，从而安装了vs2019，将该文件的路径加到配置文件中去。详见
[Matconvnet error cl.exe not found](https://stackoverflow.com/questions/40226354/matconvnet-error-cl-exe-not-found)

- ### 问题三
旧版本的matlab，如2015b，如果后安装的vs，配置文件里是没有相关的文件的，在max -setup -V 的时候能发现不会去寻找该编译器。经尝试，将高版本已有的配置文件选择好vs的版本，复制过去是可行的。

