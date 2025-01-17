---
title: "CMAQ"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 13
---

## CMAQ

###### 多尺度空气质量模型系统社团(CMAQ)的目标是通过整合多个相关科学规律模拟空气质量问题发生的全过程。模拟范围包括对流层，微小颗粒，有毒物质，酸性沉积物和可观测的降解过程。为此，社团囊括了多个相关领域的科学专家。CMAQ从设计之初就被赋予具有多尺度能力，所以不必将城市模型与各种区域尺度的空气质量模型分离开来。

###### 研究目标的格子精度，CMAQ可模拟的空间尺度以及时间尺度都跨越了数个数量级。对模型选择不同的时间尺度，即可以用于评价长时间（每年至数年）的污染大气学，也可以用于模拟短时间内（数周至数个月）的污染源扩散情况，选择不同的空间尺度，可被用于城市或特定区域的模拟。CMAQ还具有在不同尺度下同时处理多种污染的能力。

###### CMAQ在不同的尺度下会调用的适用的公式组和自然环境。其带有的普适化坐标系统会决定关键区域的网格与坐标之间的转化，该系统适用于各种垂直坐标和地图投影。
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

    ./run_cctm.csh
    ```

***

#### 二、编译

1. 环境加载，intel，netcdf，文件夹等

    ```bash
    module load intel/2022 intelmpi/2022
    export CC=icc
    export CXX=icpc
    export FC=ifort
    export F90=ifort
    export F77=ifort
    export DIR2=/home/pc/LIBRARIESICC
    export INCLUDE=${DIR2}/netcdf/include:${INCLUDE}
    export PATH=$DIR2/netcdf/bin:$PATH
    export LD_LIBRARY_PATH=${DIR2}/netcdf/lib:${LD_LIBRARY_PATH}
    export NETCDF=$DIR2/netcdf
    export JASPERLIB=$DIR2/netcdf/lib
    export JASPERINC=$DIR2/netcdf/include
    export CMAQ_HOME=/home/pc/Build_CMAQ/CMAQ-5.2.1
    export CMAQ_LIB=$CMAQ_HOME/lib
    export CMAQ_DATA=$CMAQ_HOME/data
    ```

2. 获取 CMAQ5.2.1 安装包

    ```bash
    unzip CMAQ-5.2.1.zip
    cd CMAQ-5.2.1

    # 设置 bldit_project.csh 脚本
    # 设置对应的cmaq路径
    vim bldit_project.csh

    # 变更CMAQ_HOME的值，明确工作目录：
    set CMAQ_HOME = /home/pc/Build_CMAQ/CMAQ-5.2.1

    # 初始化  运行该脚本
    ./bldit_project.csh
    ```

3. 编辑配置文件  
    ```bash
    vim config_cmaq.csh

    # 修改对应ioapi路径
    # 改完:

    ~~#>  Intel fortran compiler......................................................
        case intel:

            #> I/O API, netCDF, and MPI library locations
            setenv IOAPI_MOD_DIR   /home/pc/LIBRARIESICC/ioapi-3.2/Linux2_x86_64ifort  #> I/O API precompiled modules
            setenv IOAPI_INCL_DIR  /home/pc/LIBRARIESICC/ioapi-3.2/ioapi  #> I/O API include header files
            setenv IOAPI_LIB_DIR   /home/pc/LIBRARIESICC/ioapi-3.2/Linux2_x86_64ifort  #> I/O API libraries
            setenv NETCDF_LIB_DIR  /home/pc/LIBRARIESICC/netcdf/lib  #> netCDF directory path
            setenv NETCDF_INCL_DIR /home/pc/LIBRARIESICC/netcdf/include  #> netCDF directory path
            setenv MPI_LIB_DIR     /home/pc/LIBRARIESICC/mpich    #> MPI directory path

            #> Compiler Aliases and Flags
            setenv myFC mpiifort
            setenv myCC icc
            setenv myFSTD "-O3 -fno-alias -mp1 -fp-model source"
            setenv myDBG  "-O0 -g -check bounds -check uninit -fpe0 -fno-alias -ftrapuv -traceback"
            setenv myLINK_FLAG #"-openmp"
            setenv myFFLAGS "-fixed -132"
            setenv myFRFLAGS "-free"
            setenv myCFLAGS "-O2"
            setenv extra_lib "-lcurl"
            #setenv extra_lib ""
            setenv mpi_lib "-lmpich"    #> No Library specification needed for mpiifort
                                #> -lmpich for mvapich 
                                #> -lmpi for openmpi

            breaksw

    为“netcdf_lib”变量添加openmp属性，如：~~

    ~~setenv netcdf_lib "-lnetcdf -lnetcdff **-lgomp**"  #> -lnetcdff -lnetcdf for netCDF v4.2.0 and later~~

    ###########################################################
    csh #切换 csh 终端 直接输入csh

    source config_cmaq.csh intel #选择 intel 编译器
    ```
4. ICON 编译

    ```bash
    cd PREP/icon/scripts
    vim bldit_icon.csh

    # 修改CMAQ-5.2/PREP/icon/scripts目录下脚本bldit_icon.csh中的参数：

    # 修改ioapi相关库文件路径

    # 设置完执行以下命令编译：
    ./bldit_icon.csh intel

    # 执行完后生成ICON_v52_profile.exe即为编译成功。运行之后会报错 同时生成BLD_ICON_v52_profile_intel文件夹，进入该文件夹

    cd BLD_ICON_v52_profile_intel

    make clean  #将刚才安装的清除掉

    # 修改makefile：
    LINKER     = $(FC)
    LINK_FLAGS = -qopenmp

    # 保存之后 在该文件夹下
    make

    # 执行完后生成ICON_v52_profile.exe即为编译成功
    ```

5. BCON编译

    ```bash
    cd PREP/bcon/scripts
    vim bldit_bcon.csh

    # 修改CMAQ-5.2/PREP/bcon/scripts目录下脚本bldit_bcon.csh中的参数：

    # 修改ioapi相关库文件路径
    
    # 设置完执行以下命令编译：

    ./bldit_bcon.csh intel

    # 执行完后生成BCON_v52_profile.exe即为编译成功。

    # 运行之后会报错 同时生成BLD_ICON_v52_profile_intel文件夹

    # 进入该文件夹

    cd BLD_BCON_v52_profile_intel
    make clean  #将刚才安装的清除掉
    # 修改makefile：

    LINKER     = $(FC)
    LINK_FLAGS = -qopenmp

    # 保存之后 在该文件夹下

    make

    # 即可
    # 执行完后生成BCON_v52_profile.exe即为编译成功
    ```

6. MCIP 编译

    ```bash
    # 修改CMAQ-5.2/PREP/mcip/src目录下脚本Makefile中的参数

    # 设置完执行以下命令编译：

    source ../../../config_cmaq.csh intel
    
    make
    
    # 执行完后生成mcip.exe即为编译成功。
    ```

7. cctm编译

    ```bash
    # 修改CMAQ-5.2/CCTM/scripts目录下脚本bldit_cctm.csh中的参数
    # 设置完执行以下命令编译：

    ./bldit_cctm.csh intel
    
    # 执行完后生成CCTM*.exe即为编译成功。

    $ ls CCTM*.exe
    CCTM_v521.exe
    ```

8. combine 编译

    ```bash
    cd ~/5.2.1/POST/combine/scripts

    ./bldit_combine.csh intel 
    
    # 执行成功后会在 BLD_combine_v521_intel 目录下生成combine_v521.exe 应用程序
    ```

9. CMAQ5.2.1 安装完毕
