---
title: ranger常用命令
date: 2021-12-27 16:54:55
categories:
- [arch个人笔记]
tags: arch
---

# ranger常用命令 

生成配置文件

ranger --copy-config=all

在rc.conf中map f console scout -ftsea%space 搜索指定文件并显示

shift+s 进入当前文件位置

**导航**

- f =向下翻页
- b =向上翻页
- gg =转到列表顶部
- G =转到列表底部
- H =返回导航历史记录
- h =移至上级目录
- J =下一页1/2页
- J =下移
- K =上一页1/2页
- k =向上移动
- L =前进浏览历史记录
- Q =退出

**处理文件**

- 我…显示文件
- E | I…编辑文件
- r…使用所选程序打开文件
- cw…重命名文件
- /…搜索文件(n | p跳至下一个/上一个匹配项)      +部分文件名 + tab 可以直接跳转
- dd ..将文件标记为剪切
- ud…未切割
- p…粘贴文件
- yy ..复制/粘贴文件
- zh…显示隐藏文件
- =选择当前文件
- ：delete =删除所选文件
- ：mkdir…创建目录
- ：touch…创建一个文件
- ：rename…重命名文件

[相关链接]([https://blog.csdn.net/HideOnLie/article/details/103719698?ops_request_misc=%7B%22request%5Fid%22%3A%22162843197416780265431441%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=162843197416780265431441&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-103719698.first_rank_v2_pc_rank_v29&utm_term=ranger预览图片&spm=1018.2226.3001.4187](https://blog.csdn.net/HideOnLie/article/details/103719698?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162843197416780265431441%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162843197416780265431441&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-103719698.first_rank_v2_pc_rank_v29&utm_term=ranger%E9%A2%84%E8%A7%88%E5%9B%BE%E7%89%87&spm=1018.2226.3001.4187))

显示图标的ico插件：`git clone https://github.com/alexanderjeurissen/ranger_devicons ~/.config/ranger/plugins/ranger_devicons`