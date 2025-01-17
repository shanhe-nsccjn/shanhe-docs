---
title: "slurm使用"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 3
---

## Slurm使用

***

###### 1. sinfo    查看分区和节点的状态信息

###### 2. sbatch   提交作业 (**详细操作请参考下面3.3小节**)

###### 3. squeue   查看已提交的作业状态

###### 4. scancel  取消已提交的作业

###### 5. scontrol 查看作业相关信息

<br>

##### （1）sinfo 查看作业信息
```bash
# sinfo 查询各分区节点的空闲状态

$ sinfo
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
test           up   infinite      1   idle   n00001

# 节点状态STATE，有如下几种
# idle    状态为空闲
# mix     状态为节点中有部分核心资源可用
# alloc   状态为已被完全占用
# drain   节点故障
# down    节点下线
```

##### （2）sbatch 作业提交（详细操作请参考下面3.3小节）
```bash
# sbatch 用于提交作业到作业调度系统中

$ sbatch jobscript.slurm

# sbatch提交完脚本后，作业运行正常的情况下，提交后可获取到一个jobid作业号，作业等所需资源满足后开始运行
```

##### （3）squeue 查看作业信息
```bash
# squeue 查看提交作业的排队情况

$ squeue
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)

# squeue的输出分别是作业号，分区，作业名，用户，作业状态，运行时间，节点数量，运行节点(如果还在排队则显示排队原因)

# 作业状态ST，包括R(正在运行)，PD(正在排队)，CG(即将完成)，CD(已完成)。
```

##### （4）scancel 作业取消
```bash
# scancel取消队列中已提交的作业

# 通过squeue命令获取要取消的作业jobid，取消指定jobid的作业，以jobid为111为例，取消的命令为
scancel 111

# 如果要取消当前登录账号所有作业
scancel -u `whoami`
```

##### （5）scontrol 查看和修改作业参数
```bash
# 显示该用户的全部作业的详细信息
$ scontrol show job

# 如果要查看指定作业的详细信息，在命令最后添加作业jobid，以jobid为111为例
$ scontrol show job 123

# 显示全部节点的详细信息（不推荐执行，这将打印许多信息）
$ scontrol show node

# 显示指定节点的详细信息（推荐），通过squeue命令获取到作业指定的节点，通过如下命令可以获取到作业指定节点的cpu及内存信息，以节点n00001为例
$ scontrol show node n00001
```
