---
title: "提交运行作业"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 4
---

## 作业提交运行(推荐使用这种方式后台运行作业)

***

##### sbatch 是将整个计算过程，写到脚本中，通过sbatch指令提交到计算节点上执行。

#### 注：
- 登录到集群后请不要在登录节点直接执行计算程序。

- 编写作业提交脚本，必须通过提交或者交互运行作业。

### 0 准备工作，您需要将算例文件上传到您的集群账号中，假设您上传一个binary程序到了您的家目录中的test目录中
###### 上传操作请参考2.4小节

### 1 第一步，首先准备一个脚本模板，下面是个例子，它将在一个节点上执行共56核心的binary程序。
###### 如果您需要指定的节点或者核心数，需要修改下面例子中对应的 节点数量，节点核心，总核心数
###### 假设您需要用总核心数80，每节点40核心，对应修改数量为  2，40 ，80 ，即可

```bash
#!/bin/bash
#SBATCH --nodes=1                   # 机器节点数量
#SBATCH --ntasks-per-node=56   	 # 每个机器上的核心数量
#SBATCH --ntasks=56                 # 总核心数（总运行核心）
#SBATCH --partition=g1_share   	   # 队列分区且必须指定正确分区（会被告知）
#SBATCH --job-name=hello    	    # 作业名称（推荐修改一下，以便区分）
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
mpirun -np $SLURM_NPROCS ./binary              # 选项-np指定 $SLURM_NPROCS 参数为总核心数，即nodes*tasks
```


### 2 第二步，通过上传或者在线编辑将模板上传到集群中

###### <font color='#dd0000'>假设您编辑的脚本例子的文件名为run.slurm，放置算例文件到算例目录中，以家目录中test目录为例(二进制文件binary所在目录)

### 3 第三步，接着通过如下命令即可提交作业 后台运行
```bash
sbatch run.slurm
```

***
***

### 附：监控作业内存及cpu占用脚本模板
```bash
#!/bin/bash
#SBATCH --nodes=1                   # 节点数量
#SBATCH --ntasks-per-node=56   	 # 每个节点核心数量
#SBATCH --ntasks=56                 # 总核心数
#SBATCH --partition=g1_share   	   # 队列分区且必须指定正确分区
#SBATCH --job-name=hello    	    # 作业名称
#SBATCH --output=hello.%j.out       # 正常日志输出 (%j 参数值为 jobId)
#SBATCH --error=hello.%j.err        # 错误日志输出 (%j 参数值为 jobId)

##############################################
# memeory total calculate #
##############################################
mkdir ./memory_total                # ben
nameflag=`scontrol show hostname $SLURM_JOB_NODELIST`
jobflag=`squeue | grep $SLURM_JOB_ID | wc -l`

function gather_job(){
while [[ "$jobflag" -eq 1 ]]
do
  for line in $nameflag
  do
    meminfo=`scontrol show node $line | grep Real | sed 's/.\{3\}//' | sed 's/.\{17\}.$//'`
    hostinfo=`scontrol show node $line | grep NodeName | awk '{print $1}'`
    cpuinfo=`scontrol show node $line | grep CPULoad | awk '{print $1}'`
    echo -e "$hostinfo\n$meminfo\n$cpuinfo\n" >> ./memory_total/`date '+%Y%m%d-%T' | sed 's/.\{2\}.$//'`
  done
  sleep 1m
done
}
gather_job &

##############################################
#          Software Envrironment             #
##############################################
unset I_MPI_PMI_LIBRARY                        # 取消默认mpi库，使用intel自带
export I_MPI_JOB_RESPECT_PROCESS_PLACEMENT=0   # intel 多节点作业所需修改参数 
module load intel/2022 intelmpi/2022           # intel 环境加载

##############################################
#               Run job                      #
##############################################
mpirun -np $SLURM_NPROCS ./binary              # 选项-np指定 $SLURM_NPROCS 参数为总核心数，即nodes*tasks
```

***

