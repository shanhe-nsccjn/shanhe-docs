---
title: "VASP"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 14
---

## VASP

###### 全称Vienna Ab-initio Simulation Package，是维也纳大学Hafner小组开发的进行电子结构计算和量子力学-分子动力学模拟软件包。

###### 请自行购买VASP license许可，下载和安装。如需协助安装或使用，请联系我们，附上课题组拥有VASP license的证明。
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

    mpirun -hosts `scontrol show hostname $SLURM_JOB_NODELIST |tr '\n' ',' | head -c-1` -np $SLURM_NTASKS vasp_std
    ```

***

#### 二、编译CPU版本

1. 环境加载

    ```bash
    module load intel/2022 intelmpi/2022
    ```

2. vasp5.4.4

    ```bash
    $ tar xf vasp.5.4.4.tar.gz
    $ cd vasp.5.4.4
    $ cp arch/makefile.include.linux_intel ./makefile.include
    $ make all
    # 编译完成后会在vasp.5.4.4的文件夹下的bin文件夹里生成vasp_gam、vasp_ncl、vasp_std三个可自行文件。
    ```

#### 三、编译GPU版本（过时的，vasp官方在最新版本中已删除支持）

1. 环境加载

    ```bash
    module load intel/2022 intelmpi/2022
    ```

2. vasp5.4.4

    ```bash
    tar xf vasp.5.4.4.tar.gz
    cd vasp.5.4.4
    cp arch/makefile.include.linux_intel makefile.include
    ```

    ```bash
    vim makefile.include
    # 修改 -openmp 为 -qopenmp
    #更改前：
    GENCODE_ARCH  := -gencode=arch=compute_30,code=\"sm_30,compute_30\" \
                    -gencode=arch=compute_35,code=\"sm_35,compute_35\" \
                    -gencode=arch=compute_60,code=\"sm_60,compute_60\"
    #更改后：
    GENCODE_ARCH  := -gencode=arch=compute_70,code=\"sm_70,compute_70\"  备注：vasp5.4.4最高能接受的GPU sm配置为70  根据实际情况修改
    make gpu
    # GPU版本使用的时候需要载入cuda，高版本如2018的intel编译器编译时会报错
    ```

3. vasp6.1.2

    ```bash
    tar xf vasp.6.1.2.tar.gz
    cd vasp.6.1.2
    cp arch/makefile.include.linux_intel makefile.include
    vim makefile.include
    #更改前：
    GENCODE_ARCH  := -gencode=arch=compute_30,code=\"sm_30,compute_30\" \
                    -gencode=arch=compute_35,code=\"sm_35,compute_35\" \
                    -gencode=arch=compute_60,code=\"sm_60,compute_60\"
    #更改后：
    GENCODE_ARCH  := -gencode=arch=compute_80,code=\"sm_80,compute_80\"  #A100为sm为80 根据实际情况修改
    make gpu
    # GPU版本使用的时候需要载入cuda，高版本如2018的intel编译器编译时会报错
    ```

