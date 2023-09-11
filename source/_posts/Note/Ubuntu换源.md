---
title: Ubuntu笔记
date: 2022-05-01 10:29:28
tags: ubuntu
---

## 查看信息

查看完整整的Ubuntu系统信息：cat /proc/version

查看列表型系统版本信息：lsb_release -a

## 换源

Ubuntu系统中，软件源文件地址为：/etc/apt/sources.list

替换文件内容

```shell
#添加阿里源
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
#添加清华源
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse multiverse
```

## ssh允许远程root连接

```shell
vim /etc/ssh/sshd_config
PermitRootLogin yes
```

## 修改root密码 

`sudo passwd root`

## 删除用户

```shell
#删除用户（在root权限下）
userdel -f (强制删除用户)
userdel -r (同时删除用户以及家目录)
userdel -fr xiyang

#查看指定位置文件最后一行
tail -1 /etc/passwd
```

## 解决启动终端无法加载.bashrc导致文件无法高亮

添加.bash_profile

```shell
if test -f .bashrc ; then
source .bashrc
fi
```

