常用hexo命令

常见命令

```
hexo new postName #新建文章

hexo new page pageName #新建页面

hexo generate #生成静态页面至public目录

hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）

hexo deploy #部署到GitHub

hexo help  # 查看帮助

hexo version  #查看Hexo的版本
```

缩写：
```
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

组合命令：

```
hexo s -g #生成并本地预览
hexo d -g #生成并上传
```

默认情况下，生成的博文目录会显示全部的文章内容，如何设置文章摘要的长度呢？
答案是在合适的位置加上<!--more-->即可，例如：
```<escape><!-- more --></escape>```
