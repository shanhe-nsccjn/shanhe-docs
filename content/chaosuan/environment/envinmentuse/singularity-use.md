---
title: "singularityce"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 4
---

###### 容器技术是一种以应用软件为中心的虚拟化技术。以应用软件为单元，将软件及所有的依赖打包成容器镜像，打包后的容器镜像可直接拷贝到不同的Linux主机上运行。通过容器技术，可以很好的解决安装软件时，依赖库的安装问题、软件环境的隔离以及软件环境的移植问题。

###### Singularity为劳伦斯伯克利国家实验室开发专门用于高性能计算场景的容器技术，Singularity完全基于可移植性进行虚拟化，更加轻量级，部署更快，Singularity目前被广泛地各高性能计算中心。

###### Singularity容器技术在功能上类似于Docker，使用上与Docker略有不同。Docker用户可以轻松上手使用。由于Docker 在安全、权限、高速网络支持、MPI并行支持等各方面天生且无法修补的缺陷，导致 Docker 在超算上不适合使用，为了适应超算特有的平台环境，出现了一些专门为超算环境开发的容器软件，在目前适合于超算的容器软件里，Singularity 的兼容性最好，对超算特性支持最完整，运行性能也是最好的。

#### 一、脚本模板

```
 #!/bin/bash
 #SBATCH --nodes=1                   # 节点数量
 #SBATCH --ntasks-per-node=56        # 每个节点核心数量
 #SBATCH --ntasks=56                 # 总核心数
 #SBATCH --partition=g1_share        # 队列分区且必须指定正确分区
 #SBATCH --job-name=singularity             # 作业名称
 #SBATCH --output=sin.%j.out        # 正常日志输出 (%j 参数值为 jobId)
 #SBATCH --error=sin.%j.err         # 错误日志输出 (%j 参数值为 jobId)

 ##############################################
 #          Software Envrironment             #
 ##############################################
 unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
 export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数
 module load singularity/3.10.0        # intel 环境加载

 ##############################################
 #               Run job                      #
 ##############################################
 singularity exec --bind /home/cloudam/singularity/lammps:/home/cloudam/singularity/lammps  lammps.sif mpirun  lmp_mpi -in M-1.in
 ###--bind 参数 同 -B 映射目录 主机目录：容器目录 
```

#### 二、本地安装singulartyce

```
# 安装依赖
yum install -y gcc libuuid-devel squashfs-tools openssl-devel make

# 安装go
export VERSION=1.17.2 OS=linux ARCH=amd64   
wget https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz 
tar -C /usr/local -xzvf go$VERSION.$OS-$ARCH.tar.gz
rm -f go$VERSION.$OS-$ARCH.tar.gz 
echo 'export PATH=/usr/local/go/bin:$PATH' >> /etc/profile
source /etc/profile

# 安装singularity
export VERSION=3.9.2
wget https://github.com/sylabs/singularity/releases/download/v${VERSION}/singularity-ce-${VERSION}.tar.gz 
tar -xzf singularity-ce-${VERSION}.tar.gz
cd singularity-ce-${VERSION}
./mconfig --prefix=/opt/singularity/${VERSION}
cd builddir/
make && make install
echo "export PATH=/opt/singularity/${VERSION}/bin:\$PATH" >> /etc/profile
```

## 二、构建容器

#### 通过已有镜像构建

```bash
singularity build --sandbox ansys2022R1 docker://centos:7.6.1810
 #build 构建命令；--sandbox 可写容器镜像，不加这个后缀 默认创建sif只读模式镜像； molspin 构建容器名 
#docker:// ubuntu:18.04  

##可以在 docker 镜像库查找对应版本镜像   https://hub.docker.com/search?image_filter=official&q=
```

#### Definition 文件-.def 

```bash
#Definition 容器构建文件
Bootstrap: docker #选择构建镜像的代理 docker library shub
From: tensorflow/tensorflow:latest-gpu #选择要构建的镜像
Stage: build

%files #从主机复制文件到容器内部
    /var/py/train.py /opt/train.py
%environment #设置环境变量
    export LISTEN_PORT=12345
    export LC_ALL=C
%runscript #容器运行时执行
    echo "Container was created $NOW"
    echo "Arguments received: $*"
    exec echo "$@"
%labels #设置 镜像上的标签
    Author jason.zhang@xtaotech.com
    Version v0.0.1

%help #设置帮助文档
    This is a demo container used to illustrate a def file that uses all
    supported sections.
    
```

构建build 

```
singularity build jason-tf.sif jason_tf.def
```



### 三、singularity 常用命令

1、运行 singularity

```
singularity shell molspin.sif #交互式执行
singularity exec molspin.sif which mpirun #后台执行singularity环境里的应用命令
```

其他命令

```bash
singularity inspect molspin.sif #查看标签
singularity run-help molspin.sif #查看帮助文档
```



### 四、搭建软件环境

```
singularity shell -w molspin #进入名为 molspin的镜像，-w 可写模式
singularity shell molspin.sif #进入只读模式镜像
```

### 五、集群使用singularity

```
# 创建sandbox；
# 这里将创建的sandbox命名为ubuntu20_lammps,并使用docker hub上现有的容器 ubuntu:20.04 作为基础镜像。
singularity build --sandbox ./ubuntu20_lammps docker://ubuntu:20.04

# 进入创建好的sandbox，并进行修改；
# 其中-w表示可写。进入后singularity会自动挂载的HOME目录，如果是用root用户进入，则会挂载/root目录
singularity shell -w ./ubuntu20_lammps

# Ubuntu下安装LAMMPS并行版需要安装必要的依赖包
apt update && apt upgrade -y
apt install openmpi-bin openmpi-doc libopenmpi-dev -y
apt 域名 

# 安装fftw
wget http://www.fftw.org/fftw-3.3.9.tar.gz
tar zxvf fftw-3.3.9.tar.gz
cd fftw-3.3.9
./configure --prefix=/opt/software/fftw_3.3.9 --enable-shared --enable-static --enable-fma
make -j && make install

# 设置临时fftw环境变量
export PATH=/opt/software/fftw_3.3.9/bin:$PATH
export LD_LIBRARY_PATH=/opt/software/fftw_3.3.9/lib:$LD_LIBRARY_PATH

# 安装lammps
wget https://lammps.sandia.gov/tars/lammps-10Feb21.tar.gz
tar zxvf lammps-10Feb21.tar.gz
cd lammps-10Feb21
cd src
vim MAKE/OPTIONS/Makefile.g++_openmpi # 修改如下行
  FFT_INC = -DFFT_FFTW -I/opt/software/fftw_3.3.9/include
  FFT_PATH = -L/opt/software/fftw_3.3.9/lib
  FFT_LIB = -lfftw3

make yes-std
make no-lib
make -j g++_openmpi

mkdir /opt/software/lammps
cp ./lmp_g++_openmpi /opt/software/lammps/

# 设置临时lammps环境变量
export PATH=/opt/software/lammps:$PATH

# 验证(容器内)
cd /root
cp /opt/lammps-10Feb21/bench/in.lj .
mpirun --allow-run-as-root -np 2 --mca btl ^openib lmp_g++_openmpi -in in.lj


# 退出容器，设置永久环境变量(宿主机)
vim ./ubuntu20_lammps/environment
# 加入下面两行
export PATH=/opt/software/fftw_3.3.9/bin:/opt/software/lammps:$PATH
export LD_LIBRARY_PATH=/opt/software/fftw_3.3.9/lib:$LD_LIBRARY_PATH

# 验证(宿主机)
singularity exec ./ubuntu20_lammps mpirun --allow-run-as-root -np 2 --mca btl ^openib lmp_g++_openmpi -in in.lj

# 把修改好的sandbox打包成sif格式；
# 删除不必要的安装包, 如 fftw-3.3.9.tar.gz lammps-10Feb21.tar.gz
# 使用前面创建的sandbox目录生成singularity image file格式镜像。
singularity build ubuntu20_lammps.sif ./ubuntu20_lammps

上传ubuntu20_lammps.sif 到集群环境中。

使用提交脚本进行提交作业。
```

### 六、删除构建的容器

```shell
# 假设要删除的为文件夹名为molspin的sandbox镜像

# 首先，以可读的模式进入要删除的镜像
singularity shell --fakeroot -w molspin

# 删除掉容器中，基于fakeroot创建的所有文件
rm -rf /* 1>/dev/null 2>&1

# 退出镜像
exit

# 将创建好的软件镜像上传到高性能计算集群，加载singularity软件环境
# 删除掉剩下的
rm -rf molspin
```
