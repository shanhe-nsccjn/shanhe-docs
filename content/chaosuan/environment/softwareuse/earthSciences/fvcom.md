---
title: "FVCOM"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

##FVCOM

###### FVCOM用于多尺度，多物理的海洋动力学模型模拟计算，对研究水体采用非结构网格划分。由非结构网格划分法带来了能自由的在关注点上加密网格、提高精度，同时不影响非关注区域计算量的优点。

###### 软件适用于研究多尺度水体问题，从全球洋流，区域海洋至江河入海口都能满足研究者的需要。FVCOM包含的物理知识可以充分满足在海水，海冰，海浪，洋流，渔业等方向上的研究。目前FVCOM已经成功运用于09年法航搜索，2014年亚航搜救，日本311海啸还原模拟，30年来北极海冰面积变化，全球海水实时动态，珠江口附近海域模拟等项目。

***
#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56        # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
    #SBATCH --job-name=vasp             # 作业名称
    #SBATCH --output=vasp.%j.out        # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=vasp.%j.err         # 错误日志输出 (%j 参数值为 jobId)

    ##############################################
    #          Software Envrironment             #
    ##############################################
    unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
    export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数
    # module load intel/2022 intelmpi/2022           # intel 环境加载

    ##############################################
    #               Run job                      #
    ##############################################
    export OMP_NUM_THREADS=1

    fvcom --casename=tst
    ```

***

#### 二、编译

1. 环境加载

    ```bash
    module load intel/2022 intelmpi/2022
    ```

2. 解压后进入FVCOM目录下的METIS_source,Configure文件夹编译其中所有文件。

    ```bash
    $cd FVCOM根目录/METIS_source

    $make

    $cd FVCOM根目录/Configure

    $./configure.sh parallel

    # 新生成的make.inc文件位于FVCOM根目录下FVCOM_source文件内。
    # 第19行中 CFLAGS=-03 改写为：CFLAGS="-03 -DNAGf90Fortran"
    ```

3. 解压编译同目录中netcdf.tgz

    ```bash
    $tar xf netcdf.tgz

    # 修改netcdf中/cxx/ncvalues.cpp的头文件
    # 将#include <string>改为#include <string.h>

    $make 

    $cp ../../METIS_source/libmetis.a install/lib

    # 清理上一次fvcom的编译痕迹  
    $FVCOM/3.2.1/FVCOM_source/make clean
    ```
4. 再次编辑FVCOM3.2.1/FVCOM_source/make.inc

    ```bash 
    # 注意make.inc下面的功能开关，不同开关决定了编译出来后fvcom的功能，请自行选择。

    # 编译FVCOM_source
    $FVCOM/3.2.1/FVCOM_source/make

    # 注意1. FVCOM中make.inc文件决定了编译后的FVCOM能调用什么功能包。当使用者想改变当前FVCOM的功能时，必须重新编译FVCOM，重新执行上述的6-8步。具体开关功能请看FVCOM根目录/doc中的说明文档。

    # 注意2. FVCOM中使用波浪模块时，将swanmain.F文件中所有USE VARS_WAVE后的 "ONLY: ******" 删掉。
    ```
5. 完成。
