---
title: Ubuntu安装后无法加载全部磁盘空间问题
date: 2024-04-11 10:32:00
tags: ubuntu
---

# 原因
在安装UbuntuServer时由于第一次载入镜像选择默认镜像源，导致apt配置始终无法加载但已经自动分配好空间了，强制退出后重新选择镜像源重复默认安装步骤后没有手动分配空间，导致磁盘自动搜索剩余空间进行安装。
# 解决方案
### 查看当前卷组
```bash
 sudo vgdisplay
```
### 扩展现有卷组
```bash
sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
```
### 重新计算磁盘空间
```bash
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```
# 参考链接
[ubuntu安装空间只有磁盘一半](https://blog.csdn.net/liutit/article/details/132364616)
