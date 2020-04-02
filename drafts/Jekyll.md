# Jekyll 学习

Jekyll 是一个简单的博客形态的静态站点生产机器，它有一个模版目录，其中包含原始文本格式的文档，通过一个转换器（如：Markdown）和 Liquid 渲染器转化成一个完整的可发布的静态网站，可以发布在任何服务器上。Jekyll 也可以运行在 GitHub Page 上，也就是说，可以使用 GitHub 的服务来搭建项目页面、博客或者网站，而且是完全免费的。Jekyll 的核心其实是一个文本转换引擎。它的概念其实就是：用喜欢的标记语言来写文章，可以是 Markdown，也可以是 Textile，或者就是简单的 HTML，然后 Jekyll 就会帮你套入一个或一系列的布局中。在整个过程中可以设置 URL 路径，文本在布局中的显示样式等等。这些都可以通过纯文本编辑来实现，最终生成的静态页面就是成品。Jekyll 的配置功能很强大，可以配置在网站根目录下的 _config.yml 文件中，也可以作为命令行的标记来配置。 	

## 1 简单的博客

 获取最简单 Jekyll 模板并生成静态页面：

```shell
~ $ gem install jekyll
~ $ jekyll new myblog
~ $ cd myblog
~/myblog $ jekyll serve
# => Now browse to http://localhost:4000
```

如果在当前目录安装，并且当前目录非空，可以执行下面的命令：

```shell
jekyll new . --force
```

## 2 服务器

 Jekyll 集成了一个开发用的服务，可以使用浏览器在本地进行预览。

```shell
$ jekyll serve
# 一个开发服务器将会运行在http://localhost:4000/
# Auto-regeneration（自动再生成文件）开启。使用--no-watch来关闭

$ jekyll serve --detach
# 功能和jekyll serve命令相同，但是会脱离终端在后台运行
# 如果想关闭服务器，可以使用kill -9 1234命令，"1234" 是进程号（PID）
# 如果找不到进程号，那么就用ps aux | grep jekyll命令来查看，然后关闭服务器

$ jekyll serve --no-watch
# 和jekyll serve一样，但不会监测变化
```

