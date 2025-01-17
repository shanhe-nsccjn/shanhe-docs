---
title: "centos系统"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 3
---

## VPN登录流程

***

获取到登录方式后，使用VPN用户名及密码登录（**注意区分vpn及集群登录信息**）。在浏览器中登录如下地址，输入 VPN 账户名和密码，登录成功后会在桌面右下角显示 VPN 客户端托盘，此时浏览器会提示下载插件，将插件下载安装后，在浏览器主页面中显示可用资源。

***
###### VPN登录地址为：https://vpn.shanhe.com
***

### Centos 登录VPN详细过程如下(以Centos7.6为例)：

#### 1.	打开浏览器，输入地址 https://vpn.shanhe.com ，如图所示
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/ubuntu-vpn-1.png" width = "60%" />
<br><br>

#### 2.  根据客户端操作系统位数版本，下载对应的客户端（如javascript错误，请更换浏览器。）
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/centos-vpn-2.png" width = "60%" />
<br>

#### 3.  打开terminal终端，移动到下载的位置，使用yum命令进行安装。

```bash
$ ls
EasyConnect_x64_7_6_7_3.rpm

# 使用如下命令输入密码后进行安装
$  install ./EasyConnect_x64_7_6_7_3.rpm -y
```

<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/centos-vpn-3.png" width = "60%" />
<br><br>

#### 4.  安装完成后，点击左下角，或者使用星标开始键，输入easyConnect，找到程序，点击打开
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/centos-vpn-4.png" width = "60%" />
<br><br>

#### 5.  输入用户账户信息以登录
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/vpn5.png" width = "60%" />
<br><br>

#### 6.  登录VPN后显示可访问的资源，此时已登录成功
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/vpn6.png" width = "60%" />
