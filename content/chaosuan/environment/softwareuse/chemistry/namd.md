---
title: "NAMD"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 11
---
## NAMD

###### NAMD（NAnoscale Molecular Dynamics）是用于在大规模并行计算机上快速模拟大分子体系的并行分子动力学代码。NAMD用经验力场，如Amber，CHARMM和Dreiding，通过数值求解运动方程计算原子轨迹。NAMD最大模拟规模可超过200,000核.

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
    module load intel/2022 intelmpi/2022           # intel 环境加载

    ##############################################
    #               Run job                      #
    ##############################################
    export OMP_NUM_THREADS=1

    namd2 +ppn 56 $input_file
    ```

***

#### 二、软件安装

1. 编译安装charm++

    ```bash
    cd charm-6.10.2

    ./build charm++ netlrts-linux-x86_64 --with-production

    cd netlrts-linux-x86_64/tests/charm++/megatest

    make pgm
    ```

2. 安装namd

    ```bash
    ./config Linux-x86_64-g++ --charm-arch netlrts-linux-x86_64

    cd Linux-x86_64-g++

    make -j
    ```
