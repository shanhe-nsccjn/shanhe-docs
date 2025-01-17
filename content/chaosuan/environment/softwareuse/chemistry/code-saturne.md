---
title: "CodeSaturne"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 4
---
## Code Saturne

###### Code_Saturne是一款由法国核电集团EDF公司开发的开源通用计算流体力学软件，在能源领域特别是核电领域具有广泛的应用。

###### Code_Saturne通过求解NS方程，可以处理2D/3D、稳态/非稳态、层流/紊流（多种紊流模型）、不可压/弱可压、等温/不等温等多种计算问题。软件具有良好的并行性，可进行通用的热、流、环境分析，并具有多个针对能源行业的模块。

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
    # load intel/2022 intelmpi/2022           # intel 环境加载

    ##############################################
    #               Run job                      #
    ##############################################
    export OMP_NUM_THREADS=1

    code_saturne run --initialize --param case1.xml
    ./run_solver 
    ```

***

#### 二、软件安装

1. 进入软件目录并进行编译安装

```bash
./configure --prefix=/WORK/app/Code_Saturne/4.0.3-med CC=icc FC=ifort CXX=icpc --with-mpi=/usr/local/mpi3-dynamic --with-cgns=/WORK/app/cgnslib/3.2.1 --with-cgns-include=/WORK/app/cgnslib/3.2.1/include --with-cgns-lib=/WORK/app/cgnslib/3.2.1/lib --with-hdf5-include=/WORK/app/hdf5/1.8.13/02/include --with-hdf5-lib=/WORK/app/hdf5/1.8.13/02/lib --with-med=/WORK/app/med/3.0.8 --with-med-include=/WORK/app/med/3.0.8/include --with-med-lib=/WORK/app/med/3.0.8/lib --with-metis=/WORK/app/parmetis/4.0.3-mpi --with-metis-include=/WORK/app/parmetis/4.0.3-mpi/include --with-metis-lib=/WORK/app/parmetis/4.0.3-mpi/lib

make -j 

make install
```
