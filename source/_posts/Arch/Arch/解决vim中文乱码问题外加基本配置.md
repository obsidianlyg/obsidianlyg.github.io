---
title: vim乱码问题
date: 2021-12-27 16:54:55
categories:
- [arch个人笔记]
tags: arch
---
# 解决vim中文乱码问题外加基本配置

vim ~/.vimrc
set fileencodings=utf-8,gb2312,gb18030,gbk,ucs-bom,cp936,latin1
set enc=utf8
set fencs=utf8,gbk,gb2312,gb18030
set nu

set mouse=a 

" 显示颜色

set t_Co=256