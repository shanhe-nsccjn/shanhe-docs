---
title: "软件编译"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 4
---

## 软件编译流程

###### （软件安装流程尚未统计完成，持续更新中...）

***

###### 目前超算平台有两种编译环境，涵盖Intel和GNU编译器，可通过module的使用方法加载编译环境。
**如每次都需要使用，则可在用户目录下的 .bashrc 文件中进行添加，使其登陆后自动加载编译环境。**

<br>

```
软件编译大致流程如下：

（1）准备
配置编译之前，查询官方资料并确定需求的编译环境、依赖库、软件需要安装的组件以及软件安装的位置等。

（2）配置
软件的配置信息保存在一个configure文件中，该文件是由autoconf工具生成的，通过运行该脚本加入--help或-h参数获取具体软件的编译参数。

a.常规使用方法，软件配置执行 ./configure --prefix=folder，使用--prefix参数指定安装位置即可。
b.如若使用Intel编译器进行程序编译，则可使用F77=ifort FC=ifort CC=icc CXX=icpc ./configure --prefix=folder 命令指定编译器。

（3）编译
使用make命令进行编译，通常使用-j参数指定编译的线程数(不加参数默认使用机器核心数为线程数)，默认执行make即可，也可指定make -j 线程数 命令，进行多线程加速。

（4）安装
make命令编译完成且无报错后，执行make install命令，即可将软件安装在configure中prefix参数指定的安装目录中。
```

###### 备注：对于简单的源程序，可以通过编译器先生成目标文件再链接多个目标文件生成可执行文件。
