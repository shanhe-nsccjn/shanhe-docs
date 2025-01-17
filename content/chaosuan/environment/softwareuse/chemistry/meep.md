---
title: "MEEP"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 10
---
## MEEP

###### MEEP是开源的时域有限差分法模拟软件包，与MPB一起用于模拟电磁系统，由麻省理工学院开发

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

    mpirun python foo.py > foo.out
    ```

***

#### 二、软件安装

1. 环境加载

    ```bash
    module load conda3
    ```

2. 安装Parallel Meep

```bash
conda create -n pmp -c conda-forge pymeep=\*=mpi\_mpich\_\*
# 即我们将并行版本的Meep安装在了虚拟环境pmp上，我们激活虚拟环境

conda activate pmp
# 测试是否安装成功，可以接着打开python,然后

import meep as mp
# 没有报错就说明成功了
```
