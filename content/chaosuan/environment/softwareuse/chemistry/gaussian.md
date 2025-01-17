---
title: "GAUSSIAN"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 8
---
## Gaussian 半经验计算和从头计算使用最广泛的量子化学软件

###### Gaussian是一个功能强大的量子化学综合软件包，平台并未购买该软件，用户需要确保版权。

###### 请自行购买Gaussian license许可，下载和安装。如需协助安装或使用，请联系我们，附上课题组拥有Gaussian license的证明。

***
#### 一、脚本模板
1. Slurm

    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56        # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
    #SBATCH --job-name=gs        	    # 作业名称
    #SBATCH --output=gs.%j.out          # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=gs.%j.err           # 错误日志输出 (%j 参数值为 jobId)

    ##############################################
    #          Software Envrironment             #
    ##############################################
    unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
    export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数 
    # module load intel/2022 intelmpi/2022           # intel 环境加载

    ##############################################
    #               Run job                      #
    ##############################################
    g16 < GAO-BAB-T11.GJF
    ```

***

#### 二、安装
```bash
mkdir gaussian
mkdir gaussian/tmp
cp E64-511X.tbz gaussian
tar -xf  E64-511X.tbz
cd /es01/home/gaussian/g16/ #需要在g16目录下安装，否则报错
./bsd/install
```

```bash
vim .bashrc
export g09root=/es01/home/gaussian
export GAUSS_SCRDIR=/es01/home/gaussian/tmp
export GAUSS_EXEDIR=$g09root/g09
source $g09root/g09/bsd/g09.profile
```
