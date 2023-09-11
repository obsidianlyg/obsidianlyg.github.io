---
title: ventoy主题配置并测试
date: 2022-07-14 13:11:45
tags: linux
---

[ventoy下载地址](https://www.ventoy.net/cn/download.html)

## 准备工作

ventoy软件

安装好ventoy的u盘

安装好VMware

选好主题（[主题链接](https://www.gnome-look.org/browse?cat=109&ord=rating)）

## 配置

1. 将主题包解压并放到U盘里
2. 打开ventoy安装位置，点击VentoyPlugson.exe启动插件并启动（启动后会弹出web配置界面）
3. 选择主题插件选项->主题配置文件栏点击新增->输入完整的绝对路径并确定
4. 停止并退出插件->![image-20220714133001837](https://i.imgtg.com/2023/06/27/OkzsLg.png)

## 测试

打开VMware新建虚拟机（基本新建步骤不做介绍，可自行百度）

关键步骤：

- 点击编辑虚拟机设置->硬件选项->点击添加->点击硬盘
- ![image-20220714133943441](https://i.imgtg.com/2023/06/27/Okz8cC.png)

- 选择SATA→使用物理磁盘 →设备一般选最后一个，之后一直下一步完成即可
- ![image-20220714142328396](https://i.imgtg.com/2023/06/27/OkzuOL.png)

- 最上边绿色三角，选择打开电源时进入固件
- ![image-20220714134305864](https://i.imgtg.com/2023/06/27/OkzINi.png)

- 最后开启虚拟机即可看到主题啦（温馨提示：更新完主题配置，重启虚拟机才能看到更新后的内容）

![image-20220714134421036](https://i.imgtg.com/2023/06/27/Okz3LX.png)
