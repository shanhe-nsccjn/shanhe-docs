---
title: "STARCCM"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## StarCCM+

###### Simcenter STAR-CCM +是由Siemens Digital Industries Software开发的基于计算流体动力学的商业仿真软件。Simcenter STAR-CCM +允许对一系列工程问题进行建模和分析，这些问题涉及流体流动，热传递，应力，颗粒流动，电磁学和相关现象。

###### 请自行购买StarCCM+ license许可，下载和安装。如需协助安装或使用，请联系我们，附上课题组拥有StarCCM+ license的证明。

***
#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56        # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
    #SBATCH --job-name=starccm    	    # 作业名称
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
    ulimit -s unlimited
    ulimit -l unlimited

    cat /dev/null > machinefile
    scontrol show hostname $SLURM_JOB_NODELIST > machinefile
    jobname=`ls | grep sim | sed 's/.\{3\}.$//'`   # 自动识别目录下的单sim文件进行提交，如果有多个文件需指定，请修改下行jobname参数

    starccm+ -power -mpi intel -machinefile './machinefile' -np $SLURM_NTASKS -rsh ssh -cpubind -batch run -batch-report $jobname
    ```

***

#### 二、安装
