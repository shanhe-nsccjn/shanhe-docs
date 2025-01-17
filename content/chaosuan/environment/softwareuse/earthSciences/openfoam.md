---
title: "OPENFOAM"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## OPENFOAM

**是对连续介质力学问题进行数值计算的C++自由软件工具包，其代码遵守GNU通用公共许可证。它可进行数据预处理、后处理和自定义求解器，常用于计算流体力学(CFD)领域。该软件由OpenFOAM基金会维护。**

### 一、提交脚本

```bash
 #!/bin/bash
 #SBATCH --nodes=1                   # 节点数量
 #SBATCH --ntasks-per-node=56        # 每个节点核心数量
 #SBATCH --ntasks=56                 # 总核心数
 #SBATCH --partition=g1_share           # 队列分区且必须指定正确分区
 #SBATCH --job-name=of                # 作业名称
 #SBATCH --output=of.%j.out          # 正常日志输出 (%j 参数值为 jobId)
 #SBATCH --error=of.%j.err           # 错误日志输出 (%j 参数值为 jobId)

 ##############################################
 #          Software Envrironment             #
 ##############################################
 unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
 export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数 
 module load intel/2022 intelmpi/2022           # intel 环境加载
 moudle load openfoam                           # 软件加载(参考module使用方法，选择具体版本)

 ##############################################
 #               Run job                      #
 ##############################################

 # Mesh the geometry
 blockMesh
 # Decompose the mesh for parallel run
 decomposePar
 # Run the solver
 mpiexec simpleFoam -parallel 
 # Reconstruct the parallel results
 reconstructPar
```

### 二、 编译 

```bash
# openfoam 源码安装
环境依赖 gcc+intel+intelmpi
        gcc+openmpi

1、改文件名格式，将解压包改为以下格式

OpenFOAM-6   ThirdParty-6

2、cd Openfoam 

(可以将70行左右的GCC改为Icc  下面一个OPENMPI 改为intelmpi  进行intel 编译  intel编译时指定 $MPI_ROOT值为mpi的位置   /sh3/software/compiler/intel/2022/mpi/2021.6.0/bin/)

3、source etc/bashrc

4、先执行 ThirdParty里面的 ALLwmake

5、后执行 Openfoam里面的 ALLwmake
。
注意 编译失败后执行 allclean  ，ALLWmake 可以加 -J 参数
```

