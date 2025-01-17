---
title: "FLUENT"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## Fluent

###### Fluent是目前可用的、功能最强大的计算流体动力学（CFD）软件工具，能够让您更深入更快速地优化自己的产品性能。Fluent内含经充分验证过的物理建模功能，能为广泛的CFD和多物理场应用提供快速、精确的结果。

###### 请自行购买Fluent license许可，下载和安装。如需协助安装或使用，请联系我们，附上课题组拥有Fluent license的证明。

***
#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56        # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
    #SBATCH --job-name=fluent     	    # 作业名称
    #SBATCH --output=hello_%j.out       # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=hello_%j.err        # 错误日志输出 (%j 参数值为 jobId)

    ##############################################
    #          Software Envrironment             #
    ##############################################
    unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
    export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数 
    # module load intel/2022 intelmpi/2022           # intel 环境加载

    ##############################################
    #               Run job                      #
    ##############################################
    if [ -z "$SLURM_NPROCS" ]; then
      N=$(( $(echo $SLURM_TASKS_PER_NODE | sed -r 's/([0-9]+)\(x([0-9]+)\)/\1 * \2/') ))
    else
      N=$SLURM_NPROCS
    fi

    echo -e "N: $N\n";

    srun hostname -s |sort -V > $(pwd)/slurmhosts.$SLURM_JOB_ID.txt

    # run fluent in batch on the allocated node(s)
    fluent 3ddp -g -slurm -t $SLURM_NTASKS -pib.ibv -cnf=slurmhosts.$SLURM_JOB_ID.txt -mpi=intel -ssh -i pipe.jou
    ```

***

#### 二、安装
