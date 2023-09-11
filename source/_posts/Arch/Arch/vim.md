---
title: vim
date: 2021-12-27 16:54:55
categories:
- [arch个人笔记]
tags: arch
---
# vim
 
[https://www.ruanyifeng.com/blog/2018/09/vimrc.html](https://www.ruanyifeng.com/blog/2018/09/vimrc.html)

[Vim入门级基础配置](https://blog.csdn.net/weixin_34111790/article/details/88751335)

支持中文不乱码:

```bash
'设置编码'
set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
set encoding=utf-8
```

- vim分屏技巧
  
    `:sv` 垂直分屏
    
    `:sp` 水平分屏
    
    通过`<C-w>`开启窗口相关命令
    
    后面接上下左右就是将光标移动到指定方向的窗口
    
    若接`<` `>`则是左右调整窗口大小，`12>`则是窗口向右移动12个单位
    
    同理`+` `-`就是上下调整
    
    若后接等于则各个窗口调至初始状态

    `:wall` 全部保存

    `:qall` 全部退出

    `:only` 关闭其他窗口
    
    调整分屏大小：[https://blog.csdn.net/diyonglao4055/article/details/101184176](https://blog.csdn.net/diyonglao4055/article/details/101184176)
    
    使用技巧：[https://blog.csdn.net/wcy23580/article/details/81387188](https://blog.csdn.net/wcy23580/article/details/81387188)
    
- 安装侧边栏目录插件
  
    #下载压缩文件
    `wget http://www.vim.org/scripts/download_script.php?src_id=17123 -O nerdtree.zip`
    #解压
    `unzip nerdtree.zip`
    #在家目录下创建.vim/{plugin,doc}
    `mkdir -p ~/.vim/{plugin,doc}`
    #复制两个文件
    `cp plugin/NERD_tree.vim ~/.vim/plugin/`
    `cp doc/NERD_tree.txt ~/.vim/doc/`
    
    插件快捷键设置
    
    " 设置NerdTree
    `map <F3> :NERDTreeMirror<CR>`
    `map <F3> :NERDTreeToggle<CR>`
    
- 基础配置
  
    ```bash
    set nu
    syntax on
    set nocompatible
    set tabstop=2
     " 开启插件
    filetype plugin indent on
     " 搜索时忽略大小写，但有一个或以上大些字母时仍保持对大写敏感
    set ignorecase smartcase 
    set incsearch
    set mouse=a
    set encoding=utf-8
    set t_Co=256
    filetype indent on
    "set paste
    "set autoindent 
    set shiftwidth=2
    set expandtab
    set softtabstop=2
    set wrap
    set linebreak
    set wrapmargin=2
    set scrolloff=5
     "设置NerdTree
    map <F3> :NERDTreeMirror<CR>
    map <F3> :NERDTreeToggle<CR>
    ```
    
- 小技巧
    - 宏录制
      
        qa: 把你的操作记录在寄存器a中，操作完成后按q停止录制。
        
        于是`@a`会replay被录制的宏。
        
        `@@` 是一个快捷键用来replay最新录制的宏。
        
        `100@@`会执行100次
        
    - v选中相关使用
      
        选中后：
        
        `J` →把所有行连接起来
        
        `<` 或 `>` → 左右缩进
        
        `=` → 自动缩进
        
        未选中J单行执行连接,`<<`或`>>`单行缩进
        
    - 块选中
      
        `<C-v>`通过移动键选中区域后按`x`删除，
        
        或者按`I`(大写的i)进行多行插入
        
    - 一行文字大小写转换
      
        `Vu`或`guu` 一行文字全小写
        
        `VU` 或 `gUU` 一行文字全大写
        
    - 查看光标处的ASCII码：`ga`
    
    - 查看光标处的utf-8编码：`g8`
    
    - 打开光标处所指的文件：`gf`
    
    - 剪切一行：`dd`
    
    - 复制一行：`yy`
    
    - 到第N行
      
        N `G`
        
        N `gg`
        
        `:` N `<CR>`
        
    - 选中括号或引号内的内容
      
        vi 与 va
        
        ![Untitled](https://gitee.com/obsidianlyg/gallery/raw/master/image//Untitled.png)
        
    - 自带的自动提示功能
      
        前提：之前有输入过这个单词或汉字
        
        按`<C-p>` 或者`<C-n>`就可以自动补齐啦