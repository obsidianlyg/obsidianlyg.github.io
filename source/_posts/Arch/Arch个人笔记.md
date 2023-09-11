---
title: arch个人笔记
date: 2021-12-27 12:53:55
categories:
- [arch个人笔记]
tags: arch
---

# Arch个人笔记 

## [arch换源](/Arch/Arch/arch换源/)

## [解决vim中文乱码问题外加基本配置](/Arch/Arch/解决vim中文乱码问题外加基本配置/)

## [v2ray](/Arch/Arch/v2ray/) 

## [snap的安装与使用](/Arch/Arch/snap的安装与使用/)

## [ArcoLinux](/Arch/Arch/ArcoLinux/)

## [选择字体](/Arch/Arch/选择字体/)

## [ranger常用命令](/Arch/Arch/ranger常用命令/)

## [vim](/Arch/Arch/vim/)

## [WallPapers](/Arch/Arch/WallPaper/)

## 网站引入

很强的web导航：[https://noisedh.cn/](https://noisedh.cn/)

相关arch显卡，娱乐等配置推荐[https://archlinuxstudio.github.io/ArchLinuxTutorial/#/](https://archlinuxstudio.github.io/ArchLinuxTutorial/#/)

修改ranger默认编辑器：[https://blog.csdn.net/zmhzmhzm/article/details/106765480](https://blog.csdn.net/zmhzmhzm/article/details/106765480)

解决aur缺少依赖问题：[https://blog.csdn.net/mightbxg/article/details/112272350](https://blog.csdn.net/mightbxg/article/details/112272350)

flameshot(火焰截图)

不知道文件是否是文件夹用：ls -F

## shell常设快捷命令

# 命令别名 

```bash 
alias cp='cp -i'

alias mv='mv -i'

alias rm='rm -i'

alias ls='ls  -F --color=auto'

alias ll='ls -al'

alias grep='grep --color=auto'

alias la='ls -a'

alias pacman='sudo pacman-color'

alias p='sudo pacman-color'

alias y='yaourt'

alias h='htop'

alias vim='sudo vim'
```

## 解决谷歌无法输入中文问题

```bash 
vim .zshrc

export LC_CTYPE=zh_CN.UTF-8
```

## ArchLinux同步网络时间

`sudo timedatectl set-ntp true`

## tim和weixin安装github链接

[https://github.com/countstarlight/deepin-wine-tim-arch](https://github.com/countstarlight/deepin-wine-tim-arch)

## 查找命令集及用法

1. **`find` <指定目录> <指定条件> <指定动作>**
    - `find . -name "my*"` #搜索当前目录（含子目录，以下同）中，所有文件名以my开头的文件。
    - `find . -name "my*" -ls` #搜索当前目录中所有文件名以my开头的文件并显示它们的详细信息。
    - `find . -type f -mmin -10` #搜索当前目录中，所有过去10分钟中更新过的普通文件。如果不加-type f参数，则搜索普通文件+特殊文件+目录
2. **`locate`**
    - `locate /etc/sh` #搜索etc目录下所有以sh开头的文件。
    - `locate ~/m` # 搜索用户主目录下，所有以m开头的文件。
    - `locate -i ~/m` #搜索用户主目录下，所有以m开头的文件，并且忽略大小写。
3. **`whereis`**
    - `whereis`命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。
4. **`which`**
    - `which`命令的作用是，在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。也就是说，使用which命令，就可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令。

## 基础bash头配置

```bash
export PS1="\[\e[36;40m\]\u\[\e[m\]\[\e[34;40m\][\[\e[m\]\[\e[32;40m\]\w\[\e[m\]\[\e[34;40m\]]\[\e[m\]\[\e[32;40m\]:\[\e[m\]"
```

查看完整系统版本信息：`cat /proc/version`

查看指定程序名的pid：`pgrep -l redis`

查看redis的进程是否开启：`ps -ef | grep redis`

## 查看列表型系统版本信息：lsb_release -a

![leasea.png](https://gitee.com/obsidianlyg/gallery/raw/master/image//leasea.png)

## 配置环境变量

**1、直接在命令行中设置**

`PATH# PATH=$PATH:/usr/local/apache/bin`

使用这种方法,只对当前会话有效，也就是说每当登出或注销系统以后，PATH设置就会失效。

**2、在profile中设置PATH**

`vi /etc/profile`找到export行，

在下面新增加一行，内容为：

`export PATH=$PATH:/usr/local/apache/bin`

注：＝ 等号两边不能有任何空格。这种方法最好,除非手动强制修改PATH的值,否则将不会被改变。编辑/etc/profile后PATH的修改不会立马生效，

如果需要立即生效的话，可以执行# source profile命令。

**3、在当前用户的profile中设置PATH（推荐）**

`vi ~/.bash_profile`

修改PATH行,把/usr/local/apache/bin添加进去,如：

`PATH=$PATH:$HOME/bin:/usr/local/apache/bin`。

`source ~/.bash_profile`让这次的修改生效。 

注：这种方法只对当前用户起作用的,其他用户该修改无效。 

## 查看端口

`netstat -nlt|grep 6379`