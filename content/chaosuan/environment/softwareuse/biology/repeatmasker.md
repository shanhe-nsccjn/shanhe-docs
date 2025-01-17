---
title: "RepeatMasker"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 9
---

### RepeatMasker和RepeatModeler安装及使用

###### RepeatMasker是重复序列检测的常用工具，通过与参考数据库的相似性比对来准确识别或屏蔽基因组中的重复序列，属于同源预测注释的方式。RepeatMasker是基因组重复序列检测的常用工具。一般依赖于已有的重复序列参考库Repbase作同源预测。对于绝大部分目标真核物种，都收录在Repbase中。有时候Repbase注释重复区的效果不是很好，这种情况下可考虑执行重复序列的从头预测，即通过当前的全基因组序列，训练重复序列集构建本地repeat library，再通过RepeatMasker注释重复序列。其中，与RepeatMasker配套的RepeatModeler，可以实现。

#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56        # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g2_sysadm       # 队列分区且必须指定正确分区
    #SBATCH --job-name=hello            # 作业名称
    #SBATCH --output=hello.%j.out       # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=hello.%j.err        # 错误日志输出 (%j 参数值为 jobId)

    ##############################################
    #               Run job                      #
    ##############################################
    BuildDatabase -name GCA_012411365 -engine rmblast test.fna #根据实际算例名称修改

    RepeatModeler -pa 5 -database GCA_012411365

    RepeatMasker -pa 16 \
    -e rmblast -lib  consensi.fa.classified \
    -dir Repeat_result -gff test.fna #根据实际算例名称修改
    ```

    ```bash
    #注：
    #-dir 后面文件夹需要提前建好，否则不会生成所需文件
    #一定要 -species 指定物种，否则默认比对的是人类重复序列数据库
    #如果使用本地的参考库，通过 -lib 指定，替代 -species
    #-s、-q、-qq 等参数可控制序列比对的灵敏度，如果你的目标物种和参考物种不是很近，可能需要提升灵敏度
    #作业若有报错，三条命令可逐条提交
    #提交作业时，提前加载好搭建的conda环境
    ```

#### 二、安装

1. 创建目录

    ```bash
    #官网：http://www.repeatmasker.org/
    $ mkdir software   #创建工作目录
    $ mkdir CPANmodule #创建模块存放目录
    ```

2. miniconda安装

    ```bash
    $ cd software;bash Miniconda3-latest-Linux-x86_64.sh
    $ vim .condarc #配置conda源
    channels:
    - defaults
        show_channel_urls: true
        default_channels:
    - https://10.251.102.1/anaconda/pkgs/main
    - https://10.251.102.1/anaconda/pkgs/r
    - https://10.251.102.1/anaconda/pkgs/msys2
        custom_channels:
        bioconda: https://10.251.102.1/anaconda/cloud
        ssl_verify: false
    $ vim .bashrc #编辑环境变量
    source /es01/home/test/software/miniconda3/bin/activate
    $ conda create -n test python==3.9.12
    $ conda activate test
    $ conda install -c bioconda h5py
    $ conda install -c bioconda TRF
    $ conda install -c bioconda RECON
    $ conda install -c bioconda CD-Hit
    ```

3. 安装RMBlast

    ```bash
    #下载链接：http://repeatmasker.org/RMBlast.html
    $ tar -xf rmblast-2.11.0+-x64-linux.tar.gz -C software
    $ vim .bashrc #配置环境变量
    export PATH=/es01/home/test/software/rmblast-2.11.0/bin:$PATH
    ```

4. 安装perl

    ```bash
    #下载链接：https://www.cpan.org/src/README.html
    $ tar -xzf perl-5.16.3.tar.gz
    $ cd perl-5.16.3
    $ ./Configure -des -Dprefix=$HOME/software/localperl
    $ make
    $ make test
    $ make install
    $ vim .bashrc #配置环境变量
    export PATH=/es01/home/test/software/perl-5.16.3:$PATH
    ```

5. 安装Repeatmasker

    ```bash
    #安装Libraries 下载链接：http://repeatmasker.org/libraries/
    $ tar -xf RepeatMasker-4.1.2-p1.tar.gz
    $ tar -xf RepeatMaskerMetaData-20181026.tar.gz
    $ cp -r  Libraries/* RepeatMasker/Libraries/
    $ cd RepeatMasker
    $ ./configrue
    $ vim .bashrc
    export PATH=/es01/home/test/software/repeatmaskertest/RepeatMasker:$PATH
    ```

6. 下载cpan模块

    ```bash
    #下载链接：https://metacpan.org/
    $ cd CPANmodule
    #所需模块：
    JSON
    File::Which
    URI
    Devel::Size
    LWP::UserAgent
    Encode::Locale 
    File::Listing 
    HTML::Entities
    HTML::Tagset 
    Encode
    IO::HTML
    LWP::MediaTypes
    Test::Fatal
    HTTP::Headers
    HTML::HeadParser 
    HTTP::Cookies 
    HTTP::Daemon
    Time::Local
    Time::Zone
    HTTP::Date 
    HTTP::Negotiate 
    HTTP::Request 
    HTTP::Request::Common 
    HTTP::Response 
    HTTP::Status
    LWP::MediaTypes 
    Net::HTTP
    Test::Fatal 
    Test::Needs 
    Test::RequiresInternet 
    Try::Tiny 
    WWW::RobotRules
    #有的模块包含在同一安装包里，加载一个即可，有的包需要先加载其他的包

    #perl加载模块例子
    $ tar -xf JSON-4.06.tar.gz
    $ cd JSON-4.06/
    $ perl Makefile.PL
    $ make
    $ make install
    ```

7. 安装RepeatScout

    ```bash
    #下载链接：http://bix.ucsd.edu/repeatscout/
    $ tar -xf RepeatScout
    $ vim .bashrc
    export PATH=/es01/home/test/software/RepeatScout:$PATH
    ```

8. 安装UCSC

    ```bash
    #在有网的环境中下载好UCSC,打包上传到集群工作目录中解压即可
    $ mkdir UCSC
    $ cd UCSC/
    $ rsync -aP rsync://hgdownload.soe.ucsc.edu/genome/admin/exe/linux.x86_64/ ./
    ```

9. 安装RepeatModeler

    ```bash
    $ tar -xf RepeatModeler-2.0.3.tar.gz
    $ cd RepeatModeler-2.0.3 ; perl ./configure
    $ export PATH=/es01/home/test/software/RepeatModeler-2.0.3:$PATH
    ```

