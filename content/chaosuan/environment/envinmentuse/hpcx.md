---
title: "hpcx"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 4
---

# AI 平台，一个虚拟化平台，基于docker搭建，适用于ai 训练、gpu 加速计算的平台。

## 一、使用说明 

```bash
##1、共享目录为 “/用户名目录” ，注意在当前用户下，创建的所有开发环境共享目录为同一个
##2、网络支持ib,ib驱动存在于物理机上，当前镜像内没有，调用时可用通过指定网卡的方式调用
##3、因网络驱动存在于物理机，不建议使用源码编译mpi,可以使用hpc-x环境安装mpi的方式来替代

```

## 二、平台基本操作

参考浪潮普通用户操作手册

### AI平台mpi使用—hpc-x 

```
https://developer.nvidia.com/networking/hpc-x ### 下载地址
```

#### 安装步骤

```
cd hpcx
export HPCX_HOME=$PWD
```

#### 根据当前环境编译openmpi

```
$ tar xfp ${HPCX_HOME}/sources/openmpi-gitclone.tar.gz
$ cd ${HPCX_HOME}/sources/openmpi-gitclone
$ ./configure CC=icc CXX=icpc F77=ifort FC=ifort --prefix=${HPCX_HOME}/ompi-icc \
--with-hcoll=${HPCX_HOME}/hcoll \
--with-ucx=${HPCX_HOME}/ucx \
--with-platform=contrib/platform/mellanox/optimized \
2>&1 | tee config-icc-output.log
$ make -j32 all 2>&1 | tee build_icc.log && make -j24 install 2>&1 | tee install_icc.log
```

### 激活hpc-x

```bash
export HPCX_HOME=$PWD
cd hpcx-v2.12-gcc-MLNX_OFED_LINUX-5-ubuntu18.04-cuda11-gdrcopy2-nccl2.12-x86_64/
source hpcx-init.sh
hpcx_load

##module 方式加载
module use $HPCX_HOME/modulefiles
module load hpcx
```

### mpirun命令参数

```bash
QUDA_ENABLE_P2P=3 /yangybai11/hpcx-v2.12-gcc-MLNX_OFED_LINUX-5-ubuntu18.04-cuda11-gdrcopy2-nccl2.12-x86_64/ompi/bin/mpirun --allow-run-as-root  -np 16 --host 192.208.79.37:8,192.224.8.14:8  -bind-to none -map-by slot -x LD_LIBRARY_PATH -x HOROVOD_MPI_THREADS_DISABLE=1 -x PATH -mca pml ucx -x NCCL_DEBUG=INFO -x NCCL_TREE_THRESHOLD=0 -x UCX_LOG_LEVEL=info ./hmc -i s1.0_restart_37540.xml -geom 1 2 2 4
##--allow-run-as-root root 执行mpirun 
##  -mca pml ucx UCX 与 OpenSHMEM 显式使用 	
## 
```

```bash
/etc/hosts ##跨节点作业时，可能需要ssh 各节点之间的连接
~/.ssh/known_hosts
```

注意：这个平台网络存在于物理机上，在实例环境中访问存在问题，使用  https://developer.nvidia.com/networking/hpc-x

```
https://content.mellanox.com/hpc/hpc-x/v2.12/hpcx-v2.12-gcc-MLNX_OFED_LINUX-5-ubuntu18.04-cuda11-gdrcopy2-nccl2.12-x86_64.tbz
```





