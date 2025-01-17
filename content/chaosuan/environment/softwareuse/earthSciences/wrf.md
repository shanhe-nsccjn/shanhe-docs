---
title: "WRF"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## WRF 是一种统一的中尺度天气预报模式

###### WRF(Weather Research and Forecasting Model)模式是有美国环境预报中心(NCEP), 美国国家大气研究中心（NCAR）以及多个大学、研究所和业务部门联合研发。WRF模式适用范围很广，从中小尺度到全球尺度的数值预报和模拟都有广泛的应用。

#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56        # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
    #SBATCH --job-name=wrf              # 作业名称
    #SBATCH --output=hello.%j.out       # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=hello.%j.err        # 错误日志输出 (%j 参数值为 jobId)

    ##############################################
    #          Software Envrironment             #
    ##############################################
    unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
    export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数
    module load intel/2022 intelmpi/2022           # intel 环境加载
    module load wrf-3.7                            # 软件加载

    ##############################################
    #               Run job                      #
    ##############################################

    export WRF_HOME=/es01/software/apps/WRFV3.7.0/WRFV3
    export PBV=CLOSE
    export OMP_PROC_BIND=TRUE
    export OMP_NUM_THREADS=1
    export OMP_STACKSIZE="16M"
    export WRF_NUM_TILES=112

    mpirun -np 56 --bind-to core --map-by ppr:56:node:pe=2 numactl -l $WRF_HOME/main/wrf.exe
    ```

***

#### 二、编译

1. 环境加载

    ```bash
    module load intel/2022 intelmpi/2022
    ```

2. WRF3.6

    ```bash
    解压后进入WRF所在目录

    $./configure选择第20项

    20.  Linux x86_64 i486 i586 i686,Xeon (SNB with AVX mods) ifort compiler with icc (dmpar)

    第二项视乎研究对象选择，但普遍选择默认即可。

 

    修改由configure产生的configure.wrf

        (1) 所有-O3改为-O2

        (2) 删掉 -DUSE_NETCDF4_FEATURES

        (3) 在CFLAGS中加入-DRSL0_ONLY （*非必须但推荐，避免大规模计算时rsl文件刷屏的问题）

 

    修改dyn_em/module_advect_em.F第7578行

    !DEC$ vector always 改为 !DEC$ SIMD

 

    修改完毕后编译WRF

    $./compile em_real >& compile.log & （其他模块可以通过$./compile查看，更详细的说明见wrf使用文档）

 

    编译WPS

    在编译WRF的基础上进一步添加环境变量

    $module load jasper/1.900.1/00-CF-14-libpng

    进入WPS目录

    $./configure选择第19项

    Linux x86_64, Intel compiler    (dmpar)

    修改configure.wps，将WRF_DIR改为你的WRF安装目录

    $./compile >& compile.log &

    成功后会在WPS目录下生成

    geogrid.exe

    metgrid.exe

    ungrib.exe
    ```
