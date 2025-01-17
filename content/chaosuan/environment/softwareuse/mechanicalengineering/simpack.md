---
title: "SIMPACK"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## Simpack

###### Simpack 是一种通用多几何体系统仿真 (MBS) 软件，允许分析师和工程师对任何机械或机电系统的非线性运动进行仿真。它允许工程师生成并解算虚拟 3D 模型，以便预测和可视化动态运动、耦合力和应力。

###### 请自行购买Simpack license许可，下载和安装。如需协助安装或使用，请联系我们，附上课题组拥有Simpack license的证明。

***
#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=28        # 每个节点核心数量
    #SBATCH --ntasks=28                 # 总核心数
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
    #SBATCH --job-name=simpack    	    # 作业名称
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
    input=(`ls | grep -i spck`)

    echo "This is job #${SLURM_ARRAY_JOB_ID}, with parameter ${input[$SLURM_ARRAY_TASK_ID]}"
    echo "There are ${SLURM_ARRAY_TASK_COUNT} task(s) in the array."
    echo "  Max index is ${SLURM_ARRAY_TASK_MAX}"
    echo "  Min index is ${SLURM_ARRAY_TASK_MIN}"

    simpack-slv -j 28 --integration ${input[$SLURM_ARRAY_TASK_ID]}

    sleep 5
    ```

***

#### 二、安装
