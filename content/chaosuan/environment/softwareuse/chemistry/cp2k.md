---
title: "CP2K"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 6
---
## CP2k2022

###### CP2K 是一个量子化学和固态物理软件包，可以对固态、液态、分子、周期、材料、晶体和生物系统进行原子模拟。CP2K 为不同的建模方法提供了一个通用框架，例如使用混合高斯和平面波方法GPW和GAPW的DFT。支持的理论级别包括DFTB、LDA、GGA、MP2、RPA、半经验方法（AM1、PM3、PM6、RM1、MNDO，...）和经典力场。CP2K 可以使用NEB或二聚体方法进行分子动力学、元动力学、蒙特卡罗、埃伦费斯特动力学、振动分析、核心能级光谱、能量最小化和过渡态优化的模拟。


#### 一、作业模板
1. Slurm
```bash
#!/bin/bash
#SBATCH --nodes=2                 # 节点数量
#SBATCH --ntasks-per-node=56       # 每个节点核心数量
#SBATCH --partition=g1_user        # 队列分区且必须指定正确分区
#SBATCH --job-name=hello         # 作业名称
#SBATCH --output=%j.out            # 正常日志输出 (%j 参数值为 jobId)
##SBATCH --error=%j.err            # 错误日志输出 (%j 参数值为 jobId)

##############################################
#          Software Envrironment             #
##############################################
module load gcc/9.4.0 intel/2022 intelmpi/2022 
module load cp2k/2022_intel2022
##############################################
#               Run job                      #
##############################################
export OMP_NUM_THREADS=1
mpirun  cp2k.psmp -i  test                   # 根据实际算例名称进行修改
```

#### 二、编译
1. 环境加载
```bash
module load intel/2022
module load intelmpi/2022
module load gcc/9.4.0
module load python/3.9.12
```

2. cp2k安装

```bash
# 下载链接 https://github.com/cp2k/cp2k/releases 注：tar.gz安装包make文件有问题
$ tar -xf cp2k-2022.1.tar.bz2
$ cd cp2k-2022.1/tools/toolchain
$ ./install_cp2k_toolchain.sh -h 查看安装参数
```

```bash
$ ./install_cp2k_toolchain.sh --with-intel --with-intelmpi --with-plumed
# 在脚本执行时，同级目录下会生成build目录，自动编译过程中若缺少所需的库，按照报错的提示去网上将安装包下载并且放于build目录中，再执行编译
# 编译到cmake的bug，需要将下载的cmake-3.22.1-Linux-x86_64.sh更改名称，将报错提示的cmake-3.22.1-Linux-x86_64.sh复制
# mv cmake-3.22.1-Linux-x86_64.sh cmake-3.22.1-Linux-x86_64.sh 即可，虽然名称相同但是脚本不识别，重新更改一下名称即可
# 编译到spla-1.5.4.tar.gz的bug,需要按照报错提示修改安装spla的脚本文件，将文件的第40行tar -xf spla-{version}.tar.gz删掉
# cd build | tar -xf spla-1.5.4.tar.gz 进入buid目录手动解压spla-1.5.4.tar.gz文件
# 脚本会自动编译所需的库，以下是自动编译过程
MPI is detected and it appears to be Intel MPI
Compiling with 56 processes.
Step gcc took 0.00 seconds.
==================== Finding Intel compiler from system paths ====================
path to icc is /es01/software/spack_external/compiler/intel/2022/compiler/2022.1.0/linux/bin/intel64/icc
path to icpc is /es01/software/spack_external/compiler/intel/2022/compiler/2022.1.0/linux/bin/intel64/icpc
path to ifort is /es01/software/spack_external/compiler/intel/2022/compiler/2022.1.0/linux/bin/intel64/ifort
Step intel took 0.00 seconds.
==================== Getting proc arch info using OpenBLAS tools ====================
OpenBLAS detected LIBCORE = skylakex
OpenBLAS detected ARCH    = x86_64
==================== Installing CMake ====================
cmake-3.22.1 is already installed, skipping it.
Step cmake took 0.00 seconds.
==================== Finding Intel MPI from system paths ====================
path to mpirun is /es01/software/spack_external/compiler/intel/2022/mpi/2021.6.0/bin/mpirun
path to mpiicc is /es01/software/spack_external/compiler/intel/2022/mpi/2021.6.0/bin/mpiicc
path to mpiicpc is /es01/software/spack_external/compiler/intel/2022/mpi/2021.6.0/bin/mpiicpc
path to mpiifort is /es01/software/spack_external/compiler/intel/2022/mpi/2021.6.0/bin/mpiifort
Found lib directory /es01/software/spack_external/compiler/intel/2022/mpi/2021.6.0/lib/release
libmpi is found in ld search path
libmpicxx is found in ld search path
Step intelmpi took 0.00 seconds.
==================== Finding MKL from system paths ====================
MKLROOT is found to be /es01/software/spack_external/compiler/intel/2022/mkl/2022.1.0
libm is found in ld search path
libdl is found in ld search path
Step mkl took 0.00 seconds.
Step fftw took 0.00 seconds.
==================== Installing LIBINT ====================
libint-2.6.0 is already installed, skipping it.
Step libint took 0.00 seconds.
==================== Installing LIBXC ====================
libxc-5.2.3 is already installed, skipping it.
Step libxc took 0.00 seconds.
==================== Installing Libxsmm ====================
libxsmm-1.17 is already installed, skipping it.
Step libxsmm took 0.00 seconds.
Step scalapack took 0.00 seconds.
==================== Installing COSMA ====================
COSMA-2.5.1 is already installed, skipping it.
Step cosma took 0.00 seconds.
==================== Installing ELPA ====================
elpa-2021.11.002 is already installed, skipping it.
Step elpa took 1.00 seconds.
Step ptscotch took 0.00 seconds.
Step superlu took 0.00 seconds.
Step pexsi took 0.00 seconds.
Step quip took 0.00 seconds.
==================== Installing gsl ====================
gsl-2.7 is already installed, skipping it.
Step gsl took 0.00 seconds.
==================== Installing PLUMED ====================
plumed-2.8.0 is already installed, skipping it.
Step plumed took 0.00 seconds.
==================== Installing hdf5 ====================
hdf5-1.12.0 is already installed, skipping it.
Step hdf5 took 0.00 seconds.
Step libvdwxc took 0.00 seconds.
==================== Installing spglib ====================
spglib-1.16.2 is already installed, skipping it.
Step spglib took 0.00 seconds.
==================== Installing libvori ====================
libvori-220621 is already installed, skipping it.
Step libvori took 0.00 seconds.
==================== Installing spfft ====================
SpFFT-1.0.6 is already installed, skipping it.
Step spfft took 0.00 seconds.
==================== Installing spla ====================
SpLA-1.5.4 is already installed, skipping it.
Step spla took 0.00 seconds.
==================== Installing SIRIUS ====================
sirius_dist-7.3.1 is already installed, skipping it.
Step sirius took 1.00 seconds
```

```bash
# 库编译成功后，按照提示进行软件编译

==================== generating arch files ====================
arch files can be found in the ~/cp2k-2022.1/tools/toolchain/install/arch subdirectory
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local.ssmp
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local_static.ssmp
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local.sdbg
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local_coverage.sdbg
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local.psmp
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local.pdbg
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local_static.psmp
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local_warn.psmp
Wrote ~/cp2k-2022.1/tools/toolchain/install/arch/local_coverage.pdbg
========================== usage =========================
Done!

# 按照以下步骤编译

Now copy:
  $ cp ~/cp2k-2022.1/tools/toolchain/install/arch/* to the cp2k/arch/ directory
To use the installed tools and libraries and cp2k version
compiled with it you will first need to execute at the prompt:
  $ source ~/cp2k-2022.1/tools/toolchain/install/setup
To build CP2K you should change directory:
  $ cd cp2k/
  $ make -j 20 ARCH=local VERSION="ssmp sdbg psmp pdbg"

arch files for GPU enabled CUDA versions are named "local_cuda.*"
arch files for GPU enabled HIP versions are named "local_hip.*"
arch files for OpenCL (GPU) versions are named "local_opencl.*"
arch files for coverage versions are named "local_coverage.*"

Note that these pre-built arch files are for the GNU compiler, users have to adapt them for other compilers.
It is possible to use the provided CP2K arch files as guidance.
```

```bash
# 安装完成后会在cp2k-2022.1/exe/下生成local的目录，目录中存放二进制文件，local相当于bin目录，添加到环境变量中即可使用 ./cp2k.psmp -v会显示安装成功的库以及其他信息CP2K version 2022.1

# Source code revision git:2caeb6f

# cp2kflags: omp libint fftw3 libxc elpa parallel mpi3 scalapack cosma xsmm plumed2 spglib mkl sirius libvori libbqb

# compiler: Intel(R) Fortran Intel(R) 64 Compiler Classic for applications running on Intel(R) 64, Version 2021.6.0 Build 20220226_000000
```

#### cp2k8.2编译环境及参数

```bash
$ module load intel/2019 intelmpi/2019 gcc/9.4.0   # 或多或少有问题，不如2022
$./install_cp2k_toolchain.sh --with-mkl=system  --with-plumed
```



