## 个人修改自hexo中shoka的主题

依赖以下Hexo插件

插件名称|npm地址|功能|依赖程度
--|--|--|--
hexo-renderer-multi-markdown-it|[链接](https://www.npmjs.com/package/hexo-renderer-multi-markdown-it)|md文件渲染器，压缩css/js/html | 必需
hexo-autoprefixer|[链接](https://www.npmjs.com/package/hexo-autoprefixer)|给生成的css文件们添加浏览器前缀 | 必需
hexo-algoliasearch|[链接](https://www.npmjs.com/package/hexo-algoliasearch)|站内搜索功能 | 搜索按钮失灵
hexo-symbols-count-time|[链接](https://www.npmjs.com/package/hexo-symbols-count-time)|文章或站点字数及阅读时间统计 | 统计没有
hexo-feed|[链接](https://www.npmjs.com/package/hexo-feed)|生成Feed文件| Feed文件没有
hexo-generator-sitemap|[链接](https://www.npmjs.com/package/hexo-generator-sitemap)|生成站点地图|站点地图没有

增加备案设置

开启配置可在_config.shoka.yml中footer.filing中设置
设置备案信息目前暂时设置在shoka/language/zh-CN.yml中