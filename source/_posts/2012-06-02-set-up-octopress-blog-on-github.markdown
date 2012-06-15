---
layout: post
title: "基于Octopress和Github搭建个人博客"
date: 2012-06-02 12:47
comments: true
categories: 软件工具
---
本博的搭建主要参考一些网络文章和文档，来源于网络，回馈网络，本文对参考的文章稍微加以整理。
搭建、撰写博客的整体思路：利用Markdown标记语言（具体是什么东东可以先跳过）在本地撰写好博文，利用Octopress将其转换成静态页面，即HTML文档，然后将静态页面上传至Github，通过浏览器即可访问。下面一一介绍步骤。

<!-- more -->
#Github Repository
你需要拥有一个Github帐号和Repo来存放你的静态页面，即你的博文。

1. 参考github[帮助文档](http://help.github.com/win-set-up-git/)，注册Github，完成Git的安装、SSH key的设置以及个人信息的完善：	
2. 参考github帮助文档——[创建仓库](http://help.github.com/create-a-repo/)。此处需要注意的是你的仓库名一定要为username.github.com，其中username为你的github用户名。

#搭建本地环境
本地环境的搭建主要包括安装Ruby、Python、Gem、设置环境变量等内容。具体步骤参考下面的博文——[利用GitHub Pages安装部署Octopress博客](http://blog.zhourunsheng.com/2012/04/%E5%88%A9%E7%94%A8github-pages%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2octopress%E5%8D%9A%E5%AE%A2/)

_关于Ruby、Python、Gem的安装，需要遵循以下原则，否则可能导致代码无法高亮显示：_

{% blockquote Exception on generate codeblock with "lang:" on Windows https://github.com/imathis/octopress/issues/262 %}

I'm on Win7, with ruby1.9.3 (from rubyinstaller. why 1.9.2 is emphasized? my 1.9.3 is ok.), devkit, Python2.6. Just make sure that :

   1. there's no blankcharacter in the exec path above,
   2. exec path must be included in system path var, and
   3. bundle update to update the gems.

Then everything is ok. without addtional things like python-dev or modification to RubyPython/Pygment.

{% endblockquote %}
#安装Octopress
参考上面提及的博文[“利用GitHub Pages安装部署Octopress博客”](http://blog.zhourunsheng.com/2012/04/%E5%88%A9%E7%94%A8github-pages%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2octopress%E5%8D%9A%E5%AE%A2/)。
#发布博客到github pages
参考上面提及的博文[“利用GitHub Pages安装部署Octopress博客”](http://blog.zhourunsheng.com/2012/04/%E5%88%A9%E7%94%A8github-pages%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2octopress%E5%8D%9A%E5%AE%A2/)。
#配置 Octopress
参考上面提及的博文[“利用GitHub Pages安装部署Octopress博客”](http://blog.zhourunsheng.com/2012/04/%E5%88%A9%E7%94%A8github-pages%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2octopress%E5%8D%9A%E5%AE%A2/)。
#可能遇到的问题及解决方法
安装过程中遇到疑惑或者错误，可参考以下博文：

* [Windows 8安装Octopress记录](http://hivan.me/octopress-install-to-windows8/)
* [Windows下使用Octopress嵌入Python代码的问题](http://blog.yesmryang.net/windows-octopress-python/)
* [在Octopress中使用代码高亮](http://netwjx.github.com/blog/2012/04/21/using-code-in-octopress/)
* [【译文】用Jekyll构建静态网站](http://chen.yanping.me/cn/blog/2011/12/15/building-static-sites-with-jekyll/)
* [试用Octopress](http://www.blogjava.net/lishunli/archive/2012/03/18/372115.html)
* [转用octopress了](http://blog.yxwang.me/2011/11/migrated-to-octopress/)
* [Ruby开源项目介绍(1)：octopress——像黑客一样写博客](http://www.yangzhiping.com/tech/octopress.html)
* [Octopress 試架方法](http://coding.memory-forest.com/octopress-%E8%A9%A6%E6%9E%B6%E6%96%B9%E6%B3%95.html#more-606)
* [用Octopress搭建基于github的博客](http://ihacklog.com/version-control/git/how-to-setup-an-octopress-blog-on-github.html)
	
#Markdown
Markdown的语法参考下面两篇文章：

* [Markdown: Basics （快速入门）](http://wowubuntu.com/markdown/basic.html)
* [Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/#precode)
	
Markdown相关工具可参考博文[Markdown 工具补完](http://www.appinn.com/markdown-tools/)
#博客设置
##Octopress博客主题
暂附上两个主题的链接:[主题1](http://zespia.tw/blog/categories/%E4%B8%BB%E9%A1%8C/)、[主题2](http://zespia.tw/Octopress-Theme-Slash/index_tw.html#install)。

##增加评论模块
* 参考[How to Use Disqus in Octopress](http://gangmax.github.com/blog/2012/01/20/how-to-use-disqus-in-octopress/)

##为默认模板增加categories和tags
* category功能采用插件[tokkonopapa / octopress-tagcloud](https://github.com/tokkonopapa/octopress-tagcloud)
* tags功能采用插件[robbyedwards / octopress-tag-pages](https://github.com/robbyedwards/octopress-tag-pages)和[robbyedwards / octopress-tag-cloud](https://github.com/robbyedwards/octopress-tag-cloud)
* 参考[给 Octopress 加上标签功能](http://log4d.com/2012/05/tag-cloud/)

##octopress官方文档

