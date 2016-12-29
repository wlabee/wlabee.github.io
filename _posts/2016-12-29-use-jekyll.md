---
layout: post
title:  "用jekyll在github上搭建博客"
date:   2016-12-29 15:15:47 +0800
categories: [normal]
excerpt: "这两天捣鼓的这个github page ，然后安装jekyll来搭建项目。"
tags:
  - jekyll
  - github
---

用github-page 之前就弄过，只不过只弄了一个hello world，然后就没搞了。之前也看到了jekyll的，只不过看到安装还需要依赖ruby嫌麻烦就没搞。

安装jekyll其实很简单，官网看一下就，几个命令就解决了。先要安装好Ruby 和 RubyGems（包管理工具）

然后
{% highlight cmd %}
$ gem install jekyll		
$ jekyll new myblog		
$ cd myblog		
$ jekyll build			
$ jekyll serve		
{% endhighlight %}

代码直接推到 github 上，配置githubpage就可以了。当然githubpage配置也简单，主要保证项目名要用 用户名+github.io 例如（wlabee.github.io）。然后配置要展示的分支，还有一点就是现在访问的地址是https的！

然后就是 jekyll 的主题了，是可以直接安装使用的，不过我直接下的一个，文件覆盖的防止做的（傻）。后面再看看主题安装吧。

博客搭建都是简单的事情。重点是博客内容，你得要写！！！在这儿就先写这篇文章试试。

再补充一句吧，博文的YAML头信息如果格式，错误的话是不能成功生成文章的。刚刚就对比了半天，发现date的格式不一样导致生成失败。