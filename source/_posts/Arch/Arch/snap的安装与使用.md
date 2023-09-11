---
title: snap的安装与使用
date: 2021-12-27 16:54:55
categories:
- [arch个人笔记]
tags: arch
---
# snap的安装与使用

### **依次执行**

### **安装**

`sudo pacman -S snapd`

### **启动并设置开启启动并立即启动**

`sudo systemctl enable --now snapd.socket`

### **添加 snap 的命令**

`sudo ln -s /var/lib/snapd/snap /snap`

### **安装完了 snap 后，如果你用的是桌面系统，那么你可以下载安装 snap store 来进行 GUI 界面管理**

`sudo snap install snap-store`

目前 snap 官方给出支持以下发行版的 linux： Arch Linux CentOS Debian Deepin Elementary OS Fedora GalliumOS KDE Neon Kubuntu Linux Mint Lubuntu Manjaro Linux openSUSE Parrot Security OS Raspbian Solus Ubuntu Xubuntu Zorin OS

理论上如果你的 linux 是基于以上某个发行版本的话应该也可以使用相同的方式安装 之后你就可以使用  sudo snap install packageName 的命令来安装应用了，如果安装了 snap store 的话可以打开 snap store 来选择安装 如果没有安装 snap store 的话可以到 snap store 网页中选择自己想要下载的应用，然后选择自己想要或喜欢的应用点击图标进入详情页，在详情页中点击 install 就可以看到安装的命令了，复制到终端执行即可。