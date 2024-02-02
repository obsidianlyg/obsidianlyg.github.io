---
title: AutoHotkey使用说明
date: 2024-2-2 
tags: 
---

[参考文档](https://blog.csdn.net/m0_37816922/article/details/130849724)

[官方文档（可以调整为中文）](https://www.autohotkey.com/docs/v2/howto/RunExamples.htm)

[下载地址](https://www.autohotkey.com/)

录入一个后缀为`.ahk`的文件，双击运行即可

## 基础配置

> 如果同时存在2.0与1.1程序，需要提前在文件开头声明 `#Requires AutoHotkey v2.0`
> 

**按键映射配置**

```bash
#Requires AutoHotkey v2.0
CapsLock::Esc
Esc::CapsLock
^Left::^#Left
^Right::^#Right
!Left::Home
!Right::End
+Del::+Ins
^Del::^Ins
```

**鼠标按键循环切换虚拟桌面（v2）**

```bash
XButton1:: {
    cur := RegRead("HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VirtualDesktops", "CurrentVirtualDesktop")
    all := RegRead("HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VirtualDesktops", "VirtualDesktopIDs")
    ix := floor(InStr(all, cur) / strlen(cur))
    ixa := floor(strlen(all) / strlen(cur))
    ; msgbox "current desktop index:" ix
    ; msgbox "current desktop index:" ixa
    if (ix == 0) {
        Loop (ixa - 1) {
            Send "^#{Right}"
            Sleep 10
        }
    } else {
        Send "^#{Left}"
    }
}

XButton2:: {
    cur := RegRead("HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VirtualDesktops", "CurrentVirtualDesktop")
    all := RegRead("HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\VirtualDesktops", "VirtualDesktopIDs")
    ix := floor(InStr(all, cur) / strlen(cur))
    ixa := floor(strlen(all) / strlen(cur))
    ; msgbox "current desktop index:" ix
    ; msgbox "current desktop index:" ixa
    if (ix == ixa - 1) {
        Loop (ixa - 1) {
            Send "^#{Left}"
            Sleep 10
        }
    } else {
        Send "^#{Right}"
    }
}
```

**在虚拟桌面之间移动应用(v1.1)**

```bash
^!Right::
;获取活动窗口ID
WinGet, active_id, ID, A
;在当前桌面隐藏该窗口
WinHide, ahk_id %active_id%
;移动到下一个桌面并显示该窗口
Send #^{Right}
WinShow, ahk_id %active_id%
;移动后激活当前窗口（可选）
WinActivate, ahk_id %active_id%
;移回当前桌面（可选）
;Send #^{Left}
return

^!Left::
WinGet, active_id, ID, A
WinHide, ahk_id %active_id%
Send #^{Left}
WinShow, ahk_id %active_id%
WinActivate, ahk_id %active_id%
;Send #^{Right}
return
```

**在虚拟桌面之间移动应用(v2)**

```bash
#Requires AutoHotkey v2.0
F11::{
    active_id := WinGetID("A")
    WinHide active_id
    Send "^#{Right}"
    WinShow active_id
    WinActivate active_id
}
F10::{
    active_id := WinGetID("A")
    WinHide active_id
    Send "^#{Left}"
    WinShow active_id
    WinActivate active_id
}
```

## 按键映射

### **功能键**

| Win | Alt | Cltr | Shift | Alt Gr |
| :---: | :---: | :---: | :---: | :---: |
| `#` | `!` | `^` | `+` | `<^>!` |

其中，`<, >`为修饰符，用于区分成对出现的按键，例如`<!`表示左侧的`Ctrl`。按照这个逻辑理解，`<^>!`应该表示左`Ctrl`+右`Alt`，对于某些具有`Alt Gr`键的电脑而言，则专门指代这个按键。

### 键盘按键

| 按键 | 说明 |
| --- | --- |
| F1 - F24 | 键盘顶部的12个或更多的功能键 |
| Up, Down, Left, Right | 上下左右方向键 |
| Space, Esc, BS, Del, Ins | 空格、退出、退格、删除、插入 |
| CapsLock, ScrollLock | 大小写锁定键、滚动锁定键 |
| Home, End, PgUp, PgDn | Home键，End键，上下翻页键 |
| Tab, Enter | Tab键，回车键 |
| LWin, LShift, LAlt, LCtrl | 左Win, Shift, Alt, Ctrl |
| Numpad0-9 | 数字键盘0-9 |

### 语法（v2）

显示消息弹窗

```bash
; 可以字符串，可以是赋值，空格隔开
MsgBox "The active window's ID is " active_id
```

隐藏窗口

```bash
; 获取当前窗口id并进行隐藏
active_id := WinGetID("A")
WinHide active_id
```

显示窗口

```bash
WinShow active_id
```

聚焦窗口

```bash
WinActivate active_id
```

发送指令

```bash
Send "^#Right"
```