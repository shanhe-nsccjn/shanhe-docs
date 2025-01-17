---
title: "COMSOL"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## Comsol

######  COMSOL Multiphysics是一套跨平台的有限元素分析、求解器和多物理场模拟软件。

###### 请自行购买Comsol license许可，下载和安装。如需协助安装或使用，请联系我们，附上课题组拥有Comsol license的证明。

***
#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56        # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
    #SBATCH --job-name=comsol           # 作业名称
    #SBATCH --output=hello.%j.out       # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=hello.%j.err        # 错误日志输出 (%j 参数值为 jobId)

    ##############################################
    #          Software Envrironment             #
    ##############################################
    unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
    export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数
    # module load intel/2022 intelmpi/2022           # intel 环境加载

    ##############################################
    #               Run job                      #
    ##############################################
    comsol batch -inputfile applications/COMSOL_Multiphysics/Equation_Based/black_scholes_put.mph -outputfile my_test.mph 

    ```

***

#### 二、安装
