---
layout: post
title: 使用Jekyll和github-pages搭建个人blog系统
categories: Jekyll&&github-pages
description: 描述使用Jekyll和jithub-pages搭建个人博客的基本思路和方法
keywords: Jekyll github-pages
---

朋友，你是否也想拥有一个自主可控的blog系统？先看看我自己的博客：[bahutou's blog](http://www.bahutou.cn)。现在就可以用很低的成本实现，这篇文章就详细描述了如何利用现有的开源技术(Jekyll github-pages)搭建属于自己的blog系统。开始行动吧，小伙伴！

### 动机
	
一直以来就很喜欢分享知识，使用过CSDN、CNBLOG、51CTO，使用最多的博客系统是CSDN，在此感谢CSDN提供这样的技术分享平台。但总感觉没有找到那种随心所欲编写的那种快感。后来看到一篇博文[Blogging Like a Hacker] [1] ，触动了我的心弦。对啊，写blog就应该只关注内容，其他的都是辅助，并不是重点。核心的重点就是你的想法能顺畅的表达，排版是顺带手的。这次爽了，用合适方法去做有意思的事情。我就有了动手搭建属于自己的顺畅书写小天地的动机。技术当然也选定了：[Jekyll][2]+[github-pages][3]。

### 整体套路介绍

干啥事情都得有个名字，所谓*名正而言顺*。自己的blog也得有个个性的名字---域名(用于在网络中识别一个具体的Host)。然后我想利用github-pages服务做内容服务器，将自己注册的域名指向github-pages。这样我就拥有了自己的blog系统，且功能、风格完全可以由自己控制，并且还自带git版本管理，真是超爽。

### 域名申请和域名设置

给自己孩子起名字要先想好一个容易记、有含义的汉字组合，然后去办理出生证明的地方注册。注册网络上的域名也是一样，先想好一个好名字(当然要符合域名命名规范喽)，然后去域名服务商那里注册。国外、国内的域名服务商都很多，鉴于目前我服务的人群大部分是国内技术人员，所以我选择了国内aliyun旗下的[wanwang][4]。在阿里云---万网上注册域名的流程这里就不再赘述了。这里顺便说一下域名和内容服务在国内的一些相关政策。域名的话，需要实名认证，我的域名是个人属性，因此走的是个人实名认证。网站内容服务(提供网站服务的主机在国内)需要进行备案，且要求域名持有者名称与备案主体名称一致，并完成域名实名认证。而我的网站内容服务使用的github-pages做的内容服务，他们的服务器在国外，因此无需进行网站备案，只需要做域名实名认证就好了。域名实名认证方法见[域名实名制认证][5]。我申请的域名是[bahutou.cn][6]有了域名之后，怎么设置才能实现通过域名访问到自己的github-pages呐？以我的域名和github为例来进行说明具体的设置方法。先说一下github-pages提供的服务，github-pages提供两种基本的web服务：**User, Organization** and **Project** Pages 。一个是以个人或者组织为主，一个是以项目为主。我使用的是User Pages这种模式。这种方式的使用方法见[GitHub Pages][7]。我的github个人主页地址[zhanglianpin.github.io][8]。下一个问题就是如何将我申请的域名指向到我的github-pages上。不同的需求操作方法也不一样，我的需求是域名[www.bahutou.cn][6]和[bahutou.cn][6]都重定向到[zhanglianpin.github.io][8]。设置方法见 [Using a custom domain with GitHub Pages][9]。我的具体设置过程是这样的。首先设置我的github-pages：zhanglianpin/zhanglianpin.github.io--->setting--->GitHub Pages--->Custom domain。我设置的值是：www.bahutou.cn。官方设置步骤见[Adding or removing a custom domain for your GitHub Pages site][10]。有图有真相：
![github-pages-using-Custom domains ](/images/posts/2018-02-27-build-blog-using-jekyll-and-github-pages/github-pages-using-Custom-domains.png "Custom domains")
然后我在我的aliyun里设置DNS，根据github-pages里面的说明，需要同时设置一条A记录和一条CNAME记录。其中A记录负责解析[bahutou.cn][6]这个Domain，CNAME负责解析[www.bahutou.cn][6]这个Sub-domain。
> A @ 151.101.229.147

> CNAME	www zhanglianpin.github.io

还是上张图吧。
![aliyun-dns-setting ](/images/posts/2018-02-27-build-blog-using-jekyll-and-github-pages/aliyun-dns-setting.png "DNS-setting")
通过上述的设置，我申请的域名指向了我的github上的github-pages。可以通过我的域名访问我自己的blog喽，还有点小激动呐。

### Jekyll和github-pages介绍
### Jekyll本地环境搭建
#### Jekyll
#### Gem和bundle
#### Liquid
#### YAML
#### Jekyll目录结构和解释
#### blog动态语言和静态语言
###  使用开源的Jekyll、Theme快速搭建博客
#### 评论系统使用gitalk
#### 搜索使用JS库
#### 数学公式支持使用JS库-MathJax
#### 访问统计和字数统计等功能
### 引用资源



[1]: http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html        "Blogging Like a Hacker"
[2]: https://github.com/topics/jekyll       									 "jekyll"
[3]: https://github.com/topics/github-pages       							 	 "github-pages"
[4]: https://wanwang.aliyun.com/       							 	 			 "万网---阿里云旗下产品"
[5]: https://help.aliyun.com/knowledge_detail/35881.html?spm=a2c4e.11155515.0.0.xDePmY       							 	 			 "阿里云域名实名制认证"
[6]: https://www.bahutou.cn      							 	 			 	"bahutou's blog"
[7]: https://pages.github.com/      							 	 			 "GitHub Pages"
[8]: https://zhanglianpin.github.io      							 	 		 "zhanglianpin.github.io"
[9]: https://help.github.com/articles/using-a-custom-domain-with-github-pages/      							 	 		 "Using a custom domain with GitHub Pages"
[10]: https://help.github.com/articles/adding-or-removing-a-custom-domain-for-your-github-pages-site/      							 	 		 "Adding or removing a custom domain for your GitHub Pages site"

