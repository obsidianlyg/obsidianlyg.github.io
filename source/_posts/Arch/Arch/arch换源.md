---
title: arch换源
date: 2021-12-27 16:54:55
categories:
- [arch个人笔记]
tags: arch
---
# arch换源 

sudo wget -O /etc/pacman.d/mirrorlist [https://www.archlinux.org/mirrorlist/all/](https://www.archlinux.org/mirrorlist/all/)
sudo vim  /etc/pacman.d/mirrorlist
#进入后对china底下的注释删除
pacman -Syy
pacman -S pacman-mirrorlist#可以不写

#注意 aur助手yay，yaourt等需要添加官方源

```
然后很多教程在这里配置就结束了，就遇到一些问题，比如我们安装某些软件的时候会提示gpg签名错误损坏等这些，这个是因为没有导入key的原因，我们需要导入一下GPG KEY，具体操作就是
安装archlinuxcn-keyring 包导入 GPG key。

```

`# sudo pacman -S archlinuxcn-keyring`

中文社区仓库

编辑/etc/pacman.conf配置文件

sudo vim /etc/pacman.conf

添加源

[archlinuxcn]

#The Chinese Arch Linux communities packages.

#SigLevel = Optional TrustedOnly

#SigLevel = Optional TrustAll

#官方源

Server   = [http://repo.archlinuxcn.org/$arch](http://repo.archlinuxcn.org/$arch)

#163源

Server = [http://mirrors.163.com/archlinux-cn/$arch](http://mirrors.163.com/archlinux-cn/$arch)

#清华大学

Server = [https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch](https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch)

**以上源只能添加一个**

yay用户（选择性换源）

执行以下命令修改 aururl :

yay --aururl "[https://aur.tuna.tsinghua.edu.cn](https://aur.tuna.tsinghua.edu.cn/)" --save

修改的配置文件(如果上方不行就去配置文件改成英文引号）

vim ~/.config/yay/config.json

查看配置

yay -P -g

手动更改源排名

sudo pacman-mirrors -i -c China -m rank