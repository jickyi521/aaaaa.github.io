---
layout: post
title: "octopress和gitcafe 的搭建"
date: 2014-06-11 20:49:53 +0800
comments: true
categories: 
---

Octopress安装步骤可以参考 [象写程序一样写博客：搭建基于github的博客](http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/) 

在mac mini 和 mbp 上安装过程额外发现的问题如下:

##bundle install 安装失败##

	先安ruby的rvm环境

    rvm --force install 1.9.2  
    gem install bundle --no-ri --no-rdoc
    bundle install

  [Reference](http://stackoverflow.com/questions/12119138/failed-to-build-gem-native-extension-when-install-redcloth-4-2-9-install-linux)


##Zsh下octopress命令不识别##

	rake new_post["test"]
	->zsh: no matches found: new_post[test]

	*1、 由于zsh的通配符造成的 *, (, |, <, [, ?, 可以用引号括起来 
    rake "new_post[test]"

    *2、要根治的话需要改zsh配置文件 加 alias rake="noglob rake" 取消通配    
	vim ~/.zshrc 

##中文问题##
	
     `中文标题`
	rake new_post["测试"]
	rake generate 
    Creating new post: source/_posts/2014-06-10-ce-shi-zhong-wen.markdown

    octopress自动将中文转到了拼音，如果强制将 md 改成中文标题，博客可以显示标题但点进详情后找不着对应的index.html文件，需要手工补全才能显示详情页

    推荐做法
    rake new_post["Test"]
    编辑新生成的md文件中将 title:Test 改成 title:测试

    ‘中文内容’
    rake generate 可能出现error: invalid byte sequence in US-ASCII
    将编码格式设为 utf-8
    vim ~/.profile 加入(登录默认载入的配置文件) 
    NG=en_US.UTF-8
    LC_ALL=en_US.UTF-8


## Commands ##

* `rake setup_github_pages` 设置github_pages
* `rake new post['article name']` 生成博文框架，编辑md文件即可
* `rake watch` 检测文件变化，实时生成新内容
* `rake generate` 生成静态文件 
* `rake preview` 在本机4000端口生成访问内容 
* `rake deploy` 发布文件，同步到github master 

##好卡##

已经去掉了google 自定义字体去掉，打开发现还是卡的不行
发现浏览器下边加载状态栏一直在loading这货: ajax.googleapis.com
{% img /images/2014-06-11-ajax.googleapi.jpg %}

于是猜想这玩意应该跟字体一样也被墙了吧，于是查找下：

`find .* | xargs grep "ajax.googleapis.com"`
发现果然在 /source/_include/head.html 用了
`<script src="//ajax.googleapis.com//ajax/libs/jquery/1.9.1/jquery.min.js"></script>`
找了下网上替换成下面的
`<script src="//ajax.useso.com//ajax/libs/jquery/1.9.1/jquery.min.js"></script>`

重新deploy，流畅得都没有抓着加载 ajax.useso.com 状态


* [多台电脑协同使用octopress](http://www.orcame.com/blog/2013/12/26/octopress-multi-compoter/)
* [Gitcafe教程](http://www.tuicool.com/articles/N7bYfy)
