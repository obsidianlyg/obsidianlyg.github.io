---
title: Obsidian笔记软件(插件收藏)
date: 2024-2-23 11:20:39
tags: obsidian
---

### 快捷键：

生成properties `ctrl + ;` 或者用 三个`-` 

### 外观主题：

- AnuPpuccin（需要配合StyleSetting第三方插件使用-用于显示是否使用某种样式）
- Everforest （简洁但是很好看）
- Blue Topaz (配合StyleSetting,中文设置)

### 插件推荐：

- 日历插件：[obsidian-calendar-plugin](https://github.com/liamcain/obsidian-calendar-plugin)
- markdown表格插件：[advanced-tables-obsidian](https://github.com/tgrosinger/advanced-tables-obsidian)
- 卡片式显示多个md笔记：[sliding-panes-obsidian](https://github.com/deathau/sliding-panes-obsidian)
- 每日计划（有个时间轴，X点到X点干什么）：[obsidian-day-planner](https://github.com/lynchjames/obsidian-day-planner)
- 云同步：[remotely-save](https://github.com/remotely-save/remotely-save)
- 生成好看的图表：Charts
- 图表显示 [obsidian-chartsview-plugin](https://github.com/caronchen/obsidian-chartsview-plugin)
- 生成时间轴：timeliness
- 本地文件共享到notion的插件 [obsidian-to-notion](https://github.com/EasyChris/obsidian-to-notion)
- 关于图片的管理 [obsidian-gallery](https://github.com/Darakah/obsidian-gallery)
- 跟微信读书联动的插件 [obsidian-weread-plugin](https://github.com/zhaohongxuan/obsidian-weread-plugin)
- 通过鼠标缩放图片大小 [mousewheel-image-zoom](https://github.com/nicojeske/mousewheel-image-zoom)
- 对于侧边栏大纲样式、搜索、换位的定义 [obsidian-quiet-outline](https://github.com/guopenghui/obsidian-quiet-outline)
- 任务相关的面板设计 [obsidian-card-board](https://github.com/roovo/obsidian-card-board)
- 代码高亮 : **Editor Syntax Highlight**
- 插入icon（**Icon Shortcodes**）：[obsidian-card-board](https://github.com/aidenlx/obsidian-icon-shortcodes)
- 生成思维导图 mindmap （vscode也有这个插件）
- 快速书写LaTeX（**[Latex Suite](https://github.com/artisticat1/obsidian-latex-suite)**）
    - 其中xx为\times(文档没说)
- 设置选项的搜索 [**settings-search**](https://github.com/javalent/settings-search)
- 带有奇特主题的UML语法转图像 [obsidian-plantuml](https://github.com/joethei/obsidian-plantuml)
- 好像能直接显示视频链接 [obsidian-convert-url-to-iframe](https://github.com/FHachez/obsidian-convert-url-to-iframe)
- 多模块样式 [multi-column-markdown](https://github.com/ckRobinson/multi-column-markdown)
- 免费的数据同步插件 [obsidian-livesync](https://github.com/vrtmrz/obsidian-livesync)
- md格式化 [obsidian-prettify](https://github.com/cristianvasquez/obsidian-prettify)
- 文件夹卡片样式 [obsidian-folder-note-plugin](https://github.com/xpgo/obsidian-folder-note-plugin)
- 顶部编辑栏（标题大小颜色等选项） [obsidian-editing-toolbar](https://github.com/PKM-er/obsidian-editing-toolbar)
- 类似notion中顶部图片以及标题icon显示 [obsidian-banners](https://github.com/noatpad/obsidian-banners)
- 根据已有文件智能提示关联  [obsidian-various-complements-plugin](https://github.com/tadashi-aikawa/obsidian-various-complements-plugin)
- 类似于notion的数据库模式 [obsidian-dashboards](https://github.com/trey-wallis/obsidian-dashboards)
- 丰富右键菜单 [cMenu-Plugin](https://github.com/chetachiezikeuzor/cMenu-Plugin)
- 在obsidian内看pdf做笔记：[obsidian-annotator](https://github.com/elias-sundqvist/obsidian-annotator)
- 联动picgo剪切板上传图床：[obsidian-image-auto-upload-plugin](https://github.com/renmu123/obsidian-image-auto-upload-plugin)
- Dataview（生成数据库图表）：[obsidian-dataview](https://github.com/blacksmithgu/obsidian-dataview)
- Obsidian Icon Folder（文件夹显示icon）：[obsidian-icon-folder](https://github.com/FlorianWoelki/obsidian-icon-folder)
- **MySnippets （显示在右下角，可添加别人css主题之后会显示选项按钮用于选择样式显示）**
- 快速创建文件：QuickAdd
    - [参考文档](https://sspai.com/post/69375)
    - template的选项的用法
        - yaml写法
            
            ```bash
            ---
            title: {{NAME}}
            UID: {{DATE:YYYYMMDDHHmmss}}
            aliases: []
            tags: []
            项目状态: 提案
            主题: {{VALUE:主题是什么？}}
            date: {{DATE:YYYY-MM-DD HH:mm:ss}}
            ---
            ```
            
            - `{{NAME}}`是文件名的写法
            - `{{VALUE:主题是什么？}}`这种写法会作为一个提示输入弹窗，让我录入。
            - `{{DATE:YYYY-MM-DD HH:mm:ss}}`在创建文件时会变成当前的年月日时分秒
        - 特殊格式语法：在模板中这样写，可以在新建时出现选择框。
        
        ```bash
        {{VALUE:微信读书,纸书,bilibili,网络}}
        ```
        
    - capture的选项的用法
        - 指定文件，在内部直接插入模板的用法，语法同上 ，没有 `---`

制作模板时生成的语法：

- 时间相关
    - `{{date}}` 默认`YYYY-MM-DD`
    - 可自定义为`{{date:YYYY-MM-DD hh:mm:ss}}`
    - `{{time}}` 默认`hh:mm` 可与date连用
- 双链用法
    - `[[页面名称]]`
    - 或者直接按住相关标签拖动到编辑界面即可
- `{{title}}` 会自动获取文件名生成标题
- `tag:` 会生成在标签列表中
- 特殊是语法：
    
    ```java
    > [!tip]
    
    !后面的单词不同会出现不同的标识和背景颜色（默认有12种样式）
    同一行代表会显示相同的标识符和背景
    note
    abstract, summary, tldr
    info, todo
    tip, hint, important
    success, check, done
    question, help, faq
    warning, caution, attention
    failure, fail, missing
    danger, error
    bug
    example
    quote, cite
    ```
    
    
- 关于附件存储问题
    
    由于将复制的图片会默认存储到根目录下导致文件冗杂
    
    可到设置中`文件与连接`内下方的`附件默认存放路径` 选择`当前文件所在文件夹下指定的子文件夹中` 
    
    选中后在下方填写对应文件夹名称即可