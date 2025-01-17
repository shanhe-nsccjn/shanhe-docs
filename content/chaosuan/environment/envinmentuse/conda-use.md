---
title: "conda"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 4
---

## conda python环境创建及调试

集群默认python环境为 2.7，且没有相关的依赖包。假设您需要使用python依赖包或者更高版本的python环境，推荐您自行创建python环境以及安装依赖包（module中只有一个 3.9 环境）

1. 编辑家目录中的 .condarc 文件，使用如下命令内容填充
```bash 
cat > ~/.condarc << "EOF"
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.shanhe.com/anaconda/pkgs/main
  - https://mirrors.shanhe.com/anaconda/pkgs/r
  - https://mirrors.shanhe.com/anaconda/pkgs/msys2
custom_channels:
  bioconda: https://mirrors.shanhe.com/anaconda/cloud
  conda-forge: https://mirrors.shanhe.com/anaconda/cloud
ssl_verify: false
EOF
```

2. 加载conda环境
```bash 
module load conda3
```

3. 创建conda环境
```bash
conda create -n python39 python=3.9
# -n: 设置新的环境的名字
# python=3.9 指定新环境的python的版本，非必须参数
# 这里也可以用一个-y参数，可以直接跳过安装的确认过程。
```

4. conda环境使用
```bash
# 启动加载python环境
conda activate python39
```
```bash
# conda 安装命令
conda install gatk 
```
```bash
# 安装指定版本 
# conda install 软件名=版本号 
conda install gatk=3.7
```
```bash
# conda 搜索需要的安装包
conda search gatk
```
```bash
# 更新指定软件
conda update gatk
```
```bash
# 卸载指定软件
conda remove gatk
```
5. 账户初始化conda环境
```bash
# 将 conda 环境以及创建 ，账户自动加载
# 将如下命令加入 ~/.bashrc 文件中
module load conda3
conda activate python39
```

***

### 附：使用pip命令安装软件
```bash
# 下面举例了pip安装numpy库
pip install numpy -i https://mirrors.shanhe.com/simple --trusted-host mirrors.shanhe.com
```
