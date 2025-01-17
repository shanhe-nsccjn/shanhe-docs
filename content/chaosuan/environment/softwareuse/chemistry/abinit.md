---
title: "ABINIT"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 2
---
## ABINIT

###### ABINIT的主程序使用赝势和平面波。用密度泛函理论计算总能量、电荷密度、分子和周期性固体的电子结构；根据密度泛函计算的力与压强进行几何优化、分子动力学模；或根据密度泛函微扰理论生成动力学矩阵、波恩有效电荷、介电张量，以及其他的性质，以及许多其他的性质。激发态可以用含时密度泛函理论（对分子）或GW近似（多体微扰理论）计算。此外还提供了大量的工具程序。程序的基组库包括了元素周期表1-109号所有元素。 ABINIT适于固体物理，材料科学，化学和材料工程的研究，包括固体，分子，材料的表面，以及界面，如导体、半导体、绝缘体和金属。

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

    abinit < tstring.files >log 
    ```

***

#### 二、编译

1. 环境加载

    ```bash
    module load intel/2022 intelmpi/2022
    ```

2. 解压并进入软件目录

    ```bash
    tar zxvf abinit-8.10.3.tar.gz && cd abinit-8.10.3 && mkdir build tarballs

    # 将LibXC 3.0.0，NetCDF 4.1.1和LAPACK for Abinit ≥ 6.10拷贝到tarballs中。

    # 进入build文件夹中
    # 在build中建立hostname.ac文件，内容如下：
    ```

    ```bash
    # ================================================================
    # Configuration file for ABINIT 8 compilation on COBALT
    # tested for Intel2015 + impi
    #
    # ================================================================
    #
    FC="mpiifort"
    CC="mpiicc"
    CXX="mpicxx"
    #
    enable_mpi="yes"
    enable_openmp="yes"
    #
    with_linalg_flavor="mkl+scalapack"
    with_linalg_libs=${SCALAPACK_LDFLAGS}
    #
    with_fft_flavor="fftw3"
    with_fft_incs="-I${MKL_INCDIR}"
    with_fft_libs=${MKL_LDFLAGS}
    #
    with_trio_flavor="netcdf"
    with_dft_flavor="libxc"
    ```

    ```bash
    #在build中执行configure如下：
    ../configure --with-tardir=~/software/abinit-8.10.3/tarball

    make mj56
    ```
