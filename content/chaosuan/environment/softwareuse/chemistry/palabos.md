---
title: "Palabos"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 12
---
## Palabos

###### Palobos是一个基于LBM（格子Boltzmann方法）开源软件，也是为数不多的基于ＬＢＭ方法的计算流体软件（同类商业软件仅有Powerflow一款，且售价极为昂贵），由来自不同国家的多位学者合作开发完成，主要用于流体模拟。Palobos集成了多个LBM模型。它采用c++编写，可实现MPI并行，并采用了多种优化措施。它能将计算结果输出为VTK形式，并采用诸如paraview等开源的后出了软件进行数据分析处理。

###### PalaBos的主要特点在于，其在并行结构上采取并行机制与模型分离的方式，使得应用建模与并行机制不相关。这也使得PalaBos的易于扩展。 

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

    mpirun ./cavity3d 400 
    ```

***

#### 二、软件安装

1. 进入软件目录并进行编译安装

palabos 带有众多算例，想要对某个算例进行计算时，进入指定目录进行编译即可
```bash
cd $Palabos/examples/benchmarks/cavity3d

make
```
