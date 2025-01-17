---
title: "R"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 4
---

###### R 语言是为数学研究工作者设计的一种数学编程语言，主要用于统计分析、绘图、数据挖掘。R 语言是解释运行的语言（与 C 语言的编译运行不同），它的执行速度比 C 语言慢得多，不利于优化。但它在语法层面提供了更加丰富的数据结构操作并且能够十分方便地输出文字和图形信息，所以它广泛应用于数学尤其是统计学领域。 

#### 一、脚本模板
1. Slurm

    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 机器节点数量
    #SBATCH --ntasks-per-node=56        # 每个机器上的核心数量
    #SBATCH --ntasks=56                 # 总核心数（总运行核心）
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区（会被告知）
    #SBATCH --job-name=hello            # 作业名称（推荐修改一下，以便区分）
    #SBATCH --output=hello.%j.out       # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=hello.%j.err        # 错误日志输出 (%j 参数值为 jobId)   

    cd /es01/home/jcuser05/test #进入工作目录
    R --slave --no-restore --file=test.R #根据实际名称更改算例文件
    ```

#### 二、集群安装R语言

1. 通过conda新安装R及环境

    ```bash
    conda info --envs # 查看目前的conda环境
    conda create -n R3.5  # 创建名为R3.5的环境
    conda activate R3.5  #激活R3.5环境
    conda install r-base=3.5.1  #安装R 指定为R版本为3.5.1
    conda deactivate # 退出当前环境
    conda remove --name R3.5  --all #移除R3.5环境
    ```

2. 本地环境上传

    ```bash
    #需要本地搭建与集群环境相同路径，否则R无法使用
    #R各版本之间存在不兼容情况，尽量选择与之前环境一致的R版本
    ```

#### 三、R包安装

1. 通过conda安装R包

    ```BASH
    conda install r-ps #安装r包通过r-r包形式指定
    ```

2. 离线安装R包

    ```bash
    #CRAN 网址 ： https://cran.r-project.org/web/packages/available_packages_by_name.html
    #进入
    R 
    install.packages('/es01/home/software/pack/ncdf4_1.19.tar.gz')  #括号内指定R包，根据实际路径修改
    #不进入
    R CMD INSTALL foo_1.0.tar.gz
    ```

3. git上的R包，离线安装

    ```bash
    #githup 上的 r包需要进行解压，unzip uataq-master.zip
    R 
    library(devtool)#加载devtool
    devtools::install_local("/es01/home/software/pack/uataq-master")
    ```

4. 常见问题解决方法

    ```bash
    #当安装本地r包需要依赖conda环境安装依赖库文件或者头文件的情况下可以将所需文件路径加到环境变量里面，重新加载变量即可或者重新登陆。
    source .bashrc
    #在家目录中编辑.Rprofille文件，配置R语言现在或者以前包的路径，防止重新下载R语言需要重新安装R包，.Rprofile一般不会自动生成需要创建新文件。
    vim .Rprofile
    .libPaths(c("/es01/home/test/anaconda3/envs/r412/lib/R/library","/es01/home/test/R/lib64/R/library"))
    # 第一行为conda安装R包路径，第二行为源码安装R包路径
    ```

#### 四、R使用命令
1. 简单使用
    ```bash
    library() #查看加载的包
    library(devtool) #加载已安装的包
    .libPaths() #查看已经安装的R包的路径
    colnames(installed.packages()) #查看已经安装的R包
    ```

#### 附：
1. 有网环境conda安装

    ```bash
    #有网环境下，conda安装R语言，推荐使用的conda软件以及conda源
    #conda软件
    Anaconda3-5.2.0-Linux-x86_64.sh
    #清华conda源
    show_channel_urls: true
    remote_read_timeout_secs: 20000.0
    channels:
    - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda
    - http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
    - http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/linux-64
    - defaults
    conda install r-base==4.1.2
    ```

2. R源码安装
    ```bash
    #R语言下载链接: https://cran.r-project.org/
    #curl下载链接： https://curl.se/download.html
    #安装curl
    ./configure --prefix=/es01/home/test/curl --with-amissl --with-bearssl --with-gnutls --with-mbedtls --with-nss --with-openssl --with-schannel --with-secure-transport --with-wolfssl --with-nss-deprecated
    make
    make install
    #将curl加入环境变量，并重新加载环境变量
    export PATH=/es01/home/test/curl/bin:$PATH
    export LD_LIBRARY_PATH=/es01/home/test/curl/lib:$LD_LIBRARY_PATH
    export INCLUDE_PATH=/es01/home/test/curl/include/curl:$INCLUDE_PATH
    source .bashrc
    #安装R3.6.3
    ./configure --prefix=/es01/home/test/R
    make
    make install
    ```



