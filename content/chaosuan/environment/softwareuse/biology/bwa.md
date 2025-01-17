---
title: "BWA"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 9
---
## BWA

###### BWA是为二代测序的短序列比对参考序列（reference，fasta格式）而开发的比对软件。需要先对参考序列建库。根据测序方法的不同，有单末端序列（single-end，SE）比对和双末端序列（pair-end，PE）比对。
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

    bwa index ref.fa
    ```

***

#### 二、编译

1. 软件安装

```bash
module load conda3
conda create -n bwa python=3
conda activate bwa
conda install bwa -c bioconda
```
