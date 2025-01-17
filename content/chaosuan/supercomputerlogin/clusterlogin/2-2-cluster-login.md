---
title: "集群登录"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 3
---



###### VPN登录成功之后，通过登录节点登录到HPC集群提交作业。

##### 山河一集群负载均衡登录IP地址：**10.100.19.41**
##### 山河二集群负载均衡登录IP地址：**10.100.19.45**
##### 山河三集群负载均衡登录IP地址：**10.100.19.42**

###### 所有节点皆为CentOS系统，请使用xshell、MobaXterm、Putty、SecureCRT等工具登录。
###### （登录推荐使用mobaxterm，下载地址：https://mobaxterm.mobatek.net/download.html）

***

##### 1. 使用工具登录(以mobaxterm工具举例)

###### 在输入密码的时候不是明文显示的，可正常输入

打开mobaxterm工具并根据下图步骤进行,填入对应集群登录节点IP地址，填入登录用户名（用户名请填写实际名字，下方以username用户为例），回车后输入密码即可登录进入

<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/login1-1.png" width = "%" />

第一次登录成功后，下次登录只需要点上次在左边栏

<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/login1-2.png" width = "%" />

<br>

##### 2. 使用ssh命令行登录，如使用mac,linux等系统（以mobaxterm工具举例）

###### 在输入密码的时候不是明文显示的，可正常输入

打开mobaxterm工具点击如下按钮
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/login2-1.png" width = "90%" />

输入如下命令回车输入密码即可登录（用户名请填写实际名字，下方以山河一集群的username用户为例）
```bash
ssh username@10.100.19.41
```

<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/login2-2.png" width = "90%" />



***



## 附 mobaxterm 故障处理
如果遇见 /etc/ssh_config line 1: Missing argument. 问题，如下图所示：
![png](/chaosuan/jnsupercomputer/supercomputerintroduction/_image/moba1.png)

处理方法是编辑报错的 /etc/ssh_config 文件，添加或修改第一行的引号内的内容为当前电脑的用户名，假设当前电脑的用户名为user，则：

![png](/chaosuan/jnsupercomputer/supercomputerintroduction/_image/moba2.png)
