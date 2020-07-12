---
title: 搭建 GitHub 个人博客
date: 2019-10-08
---

# 搭建 GitHub 个人博客

## 1  GitHub 搭建个人博客的方式

[GitHub](https://github.com/) 是一个开源代码库及版本控制系统，它可以托管各种 git 库，号称程序员的 Facebook，影响力非常大。而 GitHub 里的 [Pages](https://pages.github.com/) 功能，就是用来为项目建立网站，使项目的展示能够简明易懂。我们可以通过它来建立托管在 GitHub 上的静态网页。

#### (1) [GitHub Pages](https://pages.github.com/) + [Hexo](https://hexo.io/)

Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 [Markdown](http://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

Hexo 可以理解为是基于 NodeJS 制作的一个博客工具，不是我们理解的一个开源的博客系统。其中的差别，有点意思。

Hexo 正常来说，不需要部署到我们的服务器上，我们的服务器上保存的，其实是基于在 Hexo 通过 Markdown 编写的文章，然后 Hexo 帮我们生成静态的 HTML 页面，然后将生成的 HTML 上传到我们的服务器。简而言之，Hexo 是个静态页面生成、上传的工具。

#### (2) [GitHub Pages](https://pages.github.com/) + [Jekyll](http://jekyll.com.cn/)

Jekyll 是一个简单的免费的博客生成工具，类似 WordPress。但是和 WordPress 又有很大的不同，原因是 Jekyll只是一个生成静态网页的工具，不需要数据库支持。但是可以配合第三方服务，例如 Disqus。最关键的是 Jekyll 可以免费部署在 GitHub 上，而且可以绑定自己的域名。

## 2 准备工作

1、在 GitHub 上找到自己喜欢的博客模板（[模板](https://github.com/xudailong/xudailong.github.io) ），fork 到我的仓库。

2、在 settings 中 rename 为 zoeeying.github.io，zoeeying 是我的 GitHub 用户名。此时，通过地址 zoeeying.github.io 就可以访问我的博客了。但是现在博客里的内容都是 fork 过来的模板中的内容。

3、把项目 clone 下来。

## 3 环境搭建  

#### (1) 下载安装 Ruby

下载安装包，傻瓜式 next 安装，记住，安装过程中需要勾选一下添加到环境变量的选项。安装成功后，在命令行中输入 `ruby -v` 得到 Ruby 版本号，即表示安装成功。

#### (2) 下载安装 RubyGems

在解压后的 RubyGems 文件夹中，打开命令窗口，执行命令：`ruby setup.rb`

#### (3) 用 RubyGems 安装 Jekyll

通过 `gem install jekyll` 命令安装 Jekyll。

国内安装太慢，需要修改一下 Ruby 源：

```bash
gem sources --remove
gem sources -a http://gems.ruby-china.com
```

#### (4) 开启服务

在 clone 下来的博客项目中，通过命令 `jekyll serve --watch` 开启服务。watch 是为了监测文件夹内的变化，修改后不需要重新启动 Jekyll。

如果在开启服务的时候报 jekyll-paginate 没有找到的错误，解决方法如下：

执行命令 `gem install jekyll-paginate` 安装 jekyll-paginate 插件；在 _config.yml 中增加：`plugins: [jekyll-paginate]`。

## 4 提交代码到 GitHub

1、通过 `git clone` 命令将代码 clone 到本地

2、修改或者添加文件后，使用 `git add .` 命令将文件 add 到本地缓冲区

3、通过 `git commit -m "some info"` 命令将文件提交到本地仓库

4、通过 `git push origin master` 命令 push 到 GitHub 仓库中
