---
title: 关于obsidian的免费同步方法
date: 2024-2-22 11:05:39
tags: obsidian
---

## 前提条件
下载插件 [`Remotely Save`](https://github.com/remotely-save/remotely-save)
注册[infini-cloud云端存储网站](https://account.teracloud.jp/RegistForm.php/index/)
## 配置云端存储
注册infini-cloud后，进入MyPage页面在`Apps Connection`下勾选Turn on Apps Connection
[![pFNkizq.png](https://s11.ax1x.com/2024/02/22/pFNkizq.png)](https://imgse.com/i/pFNkizq)

## 配置插件
选择远程服务：Webdav
服务器地址：`Apps Connection`中的URL
用户名和密码对应`Apps Connection`的ID和Password
鉴权类型为：basic

**配置完成后，点击侧边栏更新按钮即可同步**
[![pFNkYwD.png](https://s11.ax1x.com/2024/02/22/pFNkYwD.png)](https://imgse.com/i/pFNkYwD)

