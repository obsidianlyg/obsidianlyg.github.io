---
title: Windows快捷键使用
date: 2021-12-25 15:19:03
sticky: true
tags:
---

## 快捷键
- win+shift+s 自由截屏
    
    ![](https://gitee.com/obsidianlyg/gallery/raw/master/image//20211225152127.png)
    
- win+a 开启管理通知 
- win+s/q 搜索   
- win+e 快捷开启文件管理器 
- alt + z 直接打开桌面文件夹（自己设置的）
- win+x 相当于右键点击win图标
- win+v 剪切板
- win+g xbox控制台（可录制窗口内视频，不可录制桌面视频）
- win+。是自带ヾ(≧▽≦*)o表情包管理
- ctrl+win+d 创建桌面
- ctrl+win+左右键切换桌面
- ctrl+win+f4删除桌面
- win+r 并输入sysdm.cpl可以直接进入系统环境变量界面
- 删除efi分区
    
    [https://blog.csdn.net/weixin_39837402/article/details/79961912](https://blog.csdn.net/weixin_39837402/article/details/79961912)
    
- ctrl+shift+t 在浏览器中恢复已关闭的网页
- 神奇的cmd使用方式（在上面输入wt可以打开WindowsTerminal，如果有的话）
    
    ![](https://www.notion.so/image/https%3A%2F%2Fi.loli.net%2F2021%2F10%2F28%2FZ5evATRlJXqzjhO.png?table=block&id=dbce60fd-9a86-4777-944d-78b4efa9c24c&spaceId=8fa60c24-231e-42ac-926d-93c66dacb15f&width=2000&userId=e452371b-d0a7-48e0-8b53-3fe7854bbb99&cache=v2)
    
    按回车之后相当于在当前文件夹开启cmd

## 创建本地可以快捷搜索的方式
    
    C:\Users\lw\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\[Uzer.Me](http://uzer.me/)
    
    或者下面的位置
    
    C:\ProgramData\Microsoft\Windows\Start Menu\Programs
    
    这两个位置放入快捷方式，即可全局搜索（windows自带的也可以搜到）

## 修改资源管理其中右键打开方式
    
    打开注册表编辑器（打开方式win+r输入regedit）
    
    编辑右键打开文件方式输入路径： `计算机\HKEY_CLASSES_ROOT\*\shell`
    
    编辑右键打开文件夹方式输入路径： `计算机\HKEY_CLASSES_ROOT\Directory\shell`
    
    以下是相同的操作步骤：
    
### 添加节点
    
    【右键】shell节点 -> 新建(N) -> 项(K) -> 【命名】Typora
    
    【右键】Typora -> 新建(N) -> 项(K) -> 【命名】command（必须这个名字）
    
### 修改 Typora 节点注册表和图标
    
    1. 修改（默认）的数据：使用Typora打开（这玩意自己随意命名即可）
    2. 新增一个 字符串值 的类型名字命名（icon），设置数据为：D:\Program Files\Typora\Typora.exe
    
### 修改打开方式
    
    修改（默认）的数据为："D:\Program Files\Typora\Typora.exe" "%1"    （全是英文符号）
    
    [参考文档1](https://www.cnblogs.com/jloveu/p/how-to-add-item-to-windows-explorer-pop-menu.html)
    
    [参考文档2](https://www.cnblogs.com/clis/p/15132215.html)
    
## 给Windows命令行设置代理
    
### 设置 HTTP 代理：
    
```bash 
set http_proxy=http://127.0.0.1:1083
set https_proxy=http://127.0.0.1:1083
```
    
### socks5代理设置：
    
```bash 
set http_proxy=socks5://127.0.0.1:1083
set https_proxy=socks5://127.0.0.1:1083
```
    
### 取消代理：
    
```bash 
set http_proxy=
set https_proxy=
```
    
## Windows git bash 设置代理
### 设置 HTTP 代理：
    
```bash 
git config --global http.proxy [http://127.0.0.1:108](http://127.0.0.1:10899/)3
git config --global https.proxy [http://127.0.0.1:108](http://127.0.0.1:10899/)3
```
    
### 设置 socks5代理：
    
```bash 
git config --global http.proxy socks5://127.0.0.1:10899
git config --global https.proxy socks5://127.0.0.1:10899
```
    
### 取消代理:
    
```bash 
git config --global --unset http.proxy
git config --global --unset https.proxy
```
    
## Windows PowerShell 设置代理
### 设置代理：
    
```bash 
netsh winhttp set proxy 127.0.0.1:10899
```
    
### 代理测试:  curl [https://www.google.com](https://www.google.com/)
    CMD查看代理情况：netsh winhttp show proxy
    CMD取消代理：netsh winhttp reset proxy
