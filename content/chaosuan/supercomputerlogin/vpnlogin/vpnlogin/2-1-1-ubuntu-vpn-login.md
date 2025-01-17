---
title: "ubuntu系统"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 2
---

## VPN登录流程

***

获取到登录方式后，使用VPN用户名及密码登录（**注意区分vpn及集群登录信息**）。在浏览器中登录如下地址，输入 VPN 账户名和密码，登录成功后会在桌面右下角显示 VPN 客户端托盘，此时浏览器会提示下载插件，将插件下载安装后，在浏览器主页面中显示可用资源。

***
###### VPN登录地址为：https://vpn.shanhe.com
***


### Ubuntu 登录VPN详细过程如下(以Ubuntu22.04为例)：

#### 1.	打开浏览器，输入地址 https://vpn.shanhe.com ，如图所示
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/ubuntu-vpn-1.png" width = "60%" />
<br><br>

#### 2.  根据客户端操作系统位数版本，下载对应的客户端（如javascript错误，请更换浏览器。）
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/ubuntu-vpn-2.png" width = "60%" />
<br>

#### 3.  打开terminal终端，移动到下载的位置（一般为 ~/Downloads ），使用apt命令进行安装。

```bash
$ cd ~/Downloads
$ ls
EasyConnect_x64_7_6_7_3.deb

# 使用如下命令输入密码后进行安装
$ sudo apt install ./EasyConnect_x64_7_6_7_3.deb
```

<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/ubuntu-vpn-3.png" width = "60%" />
<br><br>

#### 4.  安装完成后，点击左下角，或者使用星标开始键，输入easyConnect，找到程序，点击打开
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/ubuntu-vpn-4.png" width = "60%" />
<br><br>

#### 5.  打开easyconnect程序后会遇到闪退问题，无法打开程序，如下图所示
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/ubuntu-vpn-5.png" width = "60%" />
<br><br>

#### 6.  解决的方法是libpango三个依赖库降级
（推荐使用a方法，对系统无影响性；b方法会降低系统库依赖版本，可能产生不可预期的问题）

a. 可使用现成的lib库
```bash
# 首先，使用下方链接下载三个库 
# https://pan.baidu.com/s/1pseXzbxqYQh3DWNC_nSM0Q?pwd=2hcf 

# 将下载好的三个库，移动到vpn安装目录
$ sudo mv libpango* /usr/share/sangfor/EasyConnect/EasyConnect 

# 安装 canberra-gtk-module 
$ sudo apt-get install libcanberra-gtk-module -y
```

b. 使用官方下载的库进行降级

```bash
$ cd ~/Downloads
$ wget http://security.ubuntu.com/ubuntu/pool/main/p/pango1.0/libpango-1.0-0_1.40.14-1ubuntu0.1_amd64.deb
$ wget http://security.ubuntu.com/ubuntu/pool/main/p/pango1.0/libpangoft2-1.0-0_1.40.14-1ubuntu0.1_amd64.deb
$ wget http://security.ubuntu.com/ubuntu/pool/main/p/pango1.0/libpangocairo-1.0-0_1.40.14-1ubuntu0.1_amd64.deb
$ sudo apt install ./libpango*
$ sudo apt-get install libcanberra-gtk-module -y
```

使用如上其中任意方法（推荐a方法），重复第4步操作，即能打开软件，如下图所示。

<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/ubuntu-vpn-6.png" width = "60%" />
<br><br>

#### 7.  输入用户账户信息以登录
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/vpn5.png" width = "60%" />
<br><br>

#### 8.  登录VPN后显示可访问的资源，此时已登录成功
<img src="/chaosuan/jnsupercomputer/supercomputerintroduction/_image/vpn6.png" width = "60%" />
