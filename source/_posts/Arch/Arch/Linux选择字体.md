---
title: Linux选择字体
date: 2021-12-27 16:54:55
categories:
- [arch个人笔记]
tags: arch
---
# 选择字体

vim ~/.config/fontconfig/fonts.conf

在family中设置字体的名称（也就是archwiki中所说的名字）

```html 
<alias>
   <family>serif</family>
   <prefer>
     <family>你喜欢的拉丁衬线字体名称</family>
     <family>你的中文衬线字体名称</family>
   </prefer>
 </alias>
```

修改字体配置后可以用 fc-match -a monospace | head 检查字体选择设置是否正确.