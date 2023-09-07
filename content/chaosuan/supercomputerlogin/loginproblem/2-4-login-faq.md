---
title: "常见问题"
description: Test description
draft: false
enableToc: false
keyword: test
weight: 6
---

# 常见问题
***

### 1、使用Mac或命令行工具登录集群时，可能会报“Host Key verification failed”错误而无法登录

###### 出现这个错误的原因是因为登录地址为负载均衡登录节点，每次登录都是按空闲状态随机分配6个登录节点中的一个
###### 解决方法就是删除掉known_hosts文件中对应IP的一行即可（或者删除这个文件）
```bash
ssh-keygen -R 10.100.19.41
# rm -f ~/.ssh/known_hosts
```

### 2、 账户更新密码
```
如有需求，在本地个人目录下，输入cepasswd即可修改登录密码，修改成功后再次登录集群请使用新密码。
密码设定要求：最少8个字符，必须包含数字、字母、特殊字符（!@#$%^&*?_）
```

### 3、 查询存储空间使用情况
```bash
lfs quota -hu $USER $HOME
```

### 4、 VNC清理缓存
```bash
rm -rf /tmp/.X11-unix/*
rm -rf /tmp/.X*lock
```