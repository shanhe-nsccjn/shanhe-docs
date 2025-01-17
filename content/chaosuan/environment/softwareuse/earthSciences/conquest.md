---
title: "CONQUEST"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## conquest

###### CONQUEST 是一款基于本地轨道密度泛函理论的、能以出色的缩放比例进行大规模并行计算的第一性原理计算软件，它使用局部轨道来表示 Kohn-Sham 本征态或者密度矩阵。 CONQUEST 可以应用于原子、分子、液体和固体，且对于大型系统特别有效。CONQUEST 可以使用哈密尔顿的精确对角化或通过线性缩放的方法来找到基态。 CONQUEST 可以执行结构弛豫（包括单位晶胞优化）和分子动力学（在具有各种恒温器的 NVE，NVT 和 NPT 集成中）。

#### 一、脚本模板
1. Slurm
    ```bash
    #!/bin/bash
    #SBATCH --nodes=1                   # 节点数量
    #SBATCH --ntasks-per-node=56   	    # 每个节点核心数量
    #SBATCH --ntasks=56                 # 总核心数
    #SBATCH --partition=g1_share   	    # 队列分区且必须指定正确分区
    #SBATCH --job-name=conquest    	    # 作业名称
    #SBATCH --output=hello.%j.out       # 正常日志输出 (%j 参数值为 jobId)
    #SBATCH --error=hello.%j.err        # 错误日志输出 (%j 参数值为 jobId)

    ##############################################
    #          Software Envrironment             #
    ##############################################
    unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
    export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数 
    module load intel/2022 intelmpi/2022           # intel 环境加载

    ##############################################
    #               Run job                      #
    ##############################################

    ```

***

#### 二、编译


1. 环境加载

    ```bash
    module load gcc/12.1.0
    module load intel/2022
    module load intelmpi/2022
    module load openmpi/4.1.3
    ```

2. blas安装

    ```bash
    #下载链接：http://www.netlib.org/blas/
    tar -xf blas-3.8.0.tgz
    make
    cp blas_LINUX.a ../lib/libblas.a
    ```

3. lapack安装

    ```bash
    #下载链接：http://www.netlib.org/lapack/
    tar -xf lapack-3.8.0.tar.gz
    cp INSTALL/make.inc.gfortran  ./make.inc
    make lapacklib
    cp liblapack ../lib
    ```

4. scalapack安装

    ```bash
    #http://www.netlib.org/scalapack/
    tar -zxvf scalapack-2.1.0.tar.gz
    cp SLmake.inc.example SLmake.inc
    #修改 SLmake.inc
    BLASLIB      = -lblas  -L$BLASPATH/lib
    LAPACKLIB    = -llapack -L$LAPACKPATH/lib
    ```

    ```bash
    make
    cp libscalapack.a ../lib/
    ```

5. fftw安装

    ```bash
    tar -zxvf fftw-3.3.8.tar.gz
    mkdir fftw 
    cd fftw-3.3.8 
    ./configure --host=alpha AR=ar RANLIB=ranlib --prefix=~/fftw
    make
    make install
    ```

6. conquest安装

    ```bash
    #https://github.com/OrderN/CONQUEST-release
    git clone https://github.com/OrderN/CONQUEST-release
    cd CONQUEST-release/src
    vim system.make #路径根据实际安装情况修改
    ```

    ```bash
    #

    # Set compilers
    FC=mpif90
    F77=mpif77

    # Linking flags
    #ROOT_PATH=/sh2/software/app/conquest/2022.6.29
    LINKFLAGS= -L/sh2/software/compiler/intel/oneapi_2022.2/mkl/2022.1.0/lib/intel64 /sh2/software/compiler/intel/oneapi_2022.2/mkl/2022.1.0/lib/intel64/libmkl_blacs_openmpi_lp64.a /sh2/software/compiler/intel/oneapi_2022.2/mkl/2022.1.0/lib/intel64/libmkl_lapack95_lp64.a -lmkl_scalapack_lp64 -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lmkl_blacs_openmpi_lp64  -lpthread -lm
    ARFLAGS=

    # Compilation flags
    #COMPFLAGS= -O3 $(XC_COMPFLAGS)
    COMPFLAGS= -I/sh2/software/compiler/intel/oneapi_2022.2/mkl/2022.1.0/include/intel64/lp64 -I/sh2/software/compiler/intel/oneapi_2022.2/mkl/2022.1.0/include
    COMPFLAGS_F77= $(COMPFLAGS)

    # Set BLAS and LAPACK libraries
    #BLAS= -llapack -L$(ROOT_PATH)/testlib -lblas -L$(ROOT_PATH)/testlib

    # Full library call; remove scalapack if using dummy diag module
    LIBS= $(FFT_LIB) $(XC_LIB)  $(BLAS)

    # LibXC compatibility (LibXC below) or Conquest XC library

    # Conquest XC library
    XC_LIBRARY = CQ
    XC_LIB =
    XC_COMPFLAGS =

    # LibXC compatibility
    # Choose old LibXC (v2.x) or modern versions
    #XC_LIBRARY = LibXC_v2
    #XC_LIBRARY = LibXC
    #XC_LIB = -lxcf90 -lxc
    #XC_COMPFLAGS = -I/usr/local/include

    # Set FFT library
    FFT_LIB= -L/sh2/software/app/conquest/2022.6.29/fftw/lib -lfftw3
    # #FFT_LIB=-lfftw3
    FFT_OBJ=fft_fftw3.o

    # Matrix multiplication kernel type
    MULT_KERN = default
    # Use dummy DiagModule or not
    DIAG_DUMMY =
    ```

    ```bash
    make
    #编译成功后会在bin目录中生成conquest二进制文件
    ```

