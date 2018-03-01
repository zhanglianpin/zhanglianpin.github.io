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

### github-pages和Jekyll介绍

github-pages是github提供的Web服务，为注册github的用户、组织或者项目提供Web服务。其主要用于用户、组织宣传，或者项目介绍等，当然是用github-pages提供的服务制作自己的blog系统也是很不错的选择。这么做也是符合github分享价值观的，blog分享也是一种值得鼓励的分享行为。专业的介绍在这里[What is GitHub Pages?][11]. 但是，github-pages提供的Web服务是static的，不支持动态执行环境(比如:PHP、Ruby、Python)和数据库服务。那有些小伙伴就有疑惑了，难道我的每一篇都要使用html+js手动编写吗？NO,NO，我们有Jekyll，Jekyll是一个简单的，适合博客的（blog aware），静态网站生成器。输入一个模板目录（代表网站的原始形态），经过Texitile和Liquid转换，输出一个完全静态的网站。你可以发布在任何你喜爱的服务器上。Jekyll 也可以运行在 GitHub Page 上，也就是说，你可以使用 GitHub 的服务来搭建你的项目页面、博客或者网站，而且是完全免费的。使用Jekyll构建的网站源目录上传到github指定的仓库中，github就会自动生成静态页面，非常方便。Jekyll作者（也是GitHub的共同创始人） Tom Preston-Werner，在github上的名字叫[mojombo][12]。还是忍不住再提一次它的[Blogging Like a Hacker] [1]，值得一读。

### 有关Jekyll
#### Jekyll

Jekyll 是一个简单的博客形态的静态站点生产机器。它有一个模版目录，其中包含原始文本格式的文档，通过一个转换器（如 Markdown）和我们的 Liquid 渲染器转化成一个完整的可发布的静态网站，你可以发布在任何你喜爱的服务器上。Jekyll 也可以运行在 GitHub Page 上，也就是说，你可以使用 GitHub 的服务来搭建你的项目页面、博客或者网站，而且是完全免费的。

Jekyll is a simple, blog-aware, static site generator perfect for personal, project, or organization sites. Think of it like a file-based CMS, without all the complexity. Jekyll takes your content, renders Markdown and Liquid templates, and spits out a complete, static website ready to be served by Apache, Nginx or another web server. Jekyll is the engine behind GitHub Pages, which you can use to host sites right from your GitHub repositories.

官方网站[jekyllrb][13]、github地址[jekyll][14]。

#### Jekyll的本地环境搭建

搭建Jekyll本地执行环境的目的是本地预览效果。在本地编辑好文件后，在本地可以看到效果，这样只需要一次git push就可以了。如果本地不安装jekyll，github pages中一旦出现显示问题，就需要不断地修改，并不断git push提交修改。

基本思路：从技术角度来看，Jekyll是使用Ruby语言编写的一个软件，因此要运行Jekyll需要首先安装Ruby执行环境。Jekyll作为一个使用Ruby语言开发的软件一定有其方便开发者和使用者的发布方式和安装方式。Ruby软件包管理软件叫[RubyGems][17]，它可以辅助你发布或者安装用Ruby语言写的软件，而且能自动处理依赖关系。为了方便我们安装上这个Gem。
依赖：

- [Ruby][20]（including development headers, Jekyll 2 需要 v1.9.3 及以上版本，Jekyll 3 需要 v2 及以上版本）
- [RubyGems][17]
- Linux, Un ix, or Mac OS X
- [NodeJS][21], 或其他 JavaScript 运行环境（Jekyll 2 或更早版本需要 CoffeeScript 支持）。
- [Python-2.7][22] （Jekyll 2 或更早版本）

上述依赖安装完毕之后，就可以安装Jekyll了。
我的开发环境使用的是Win10下面的Bash on Ubuntu on Windows，一个windows仿真ubuntu的虚拟环境，不用安装Vmware虚拟机软件了，而且网络、文件都可以和Win10共享，十分方便。

*Note:* You must have [Bash on Ubuntu on Windows][BASH-WSL] enabled.

First let's make sure all our packages / repositories are up to date. Open a new Command Prompt instance, and type the following:

```sh
bash
```
Your Command Prompt instance should now be a Bash instance. Now we must update our repo lists and packages.

```sh
sudo apt-get update -y && sudo apt-get upgrade -y
```
Now we can install Ruby. To do this we will use a repository from [BrightBox](https://www.brightbox.com/docs/ruby/ubuntu/), which hosts optimized versions of Ruby for Ubuntu.

```sh
sudo apt-add-repository ppa:brightbox/ruby-ng
sudo apt-get update
sudo apt-get install ruby2.3 ruby2.3-dev build-essential dh-autoreconf
```

Next let's update our Ruby gems:

```sh
sudo gem update
```

Now all that is left to do is install Jekyll.

```sh
sudo gem install jekyll bundler
```

Check if Jekyll installed properly by running:

```sh
jekyll -v
```

**And that's it!**

#### 使用Jekyll创建blog系统

To start a new project named `my_blog`, just run:

```sh
jekyll new my_blog
```

You can make sure time management is working properly by inspecting your `_posts` folder. You should see a markdown file with the current date in the filename.

**Note:** Bash on Ubuntu on Windows is still under development, so you may run into issues.

#### Jekyll目录结构和解释
Jekyll 的核心其实是一个文本转换引擎。它的概念其实就是：你用你最喜欢的标记语言来写文章，可以是 Markdown, 也可以是 Textile, 或者就是简单的 HTML, 然后 Jekyll 就会帮你套入一个或一系列的布局中。在整个过程中你可以设置 URL 路径，你的文本在布局中的显示样式等等。这些都可以通过纯文本编辑来实现，最终生成的静态页面就是你的成品了。

一个基本的 Jekyll 网站的目录结构一般是像这样的：
{% highlight bash %}
.
├── _config.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.textile
|   └── on-simplicity-in-technology.markdown
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
|   └── 2009-04-26-barcamp-boston-4-roundup.textile
├── _site
├── .jekyll-metadata
└── index.html
{% endhighlight %}

来看看这些都有什么用：

<div class="mobile-side-scroller">
<table>
  <thead>
    <tr>
      <th>文件 / 目录</th>
      <th>描述</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        <p><code>_config.yml</code></p>
      </td>
      <td>
        <p>

          保存配置数据。很多配置选项都可以直接在命令行中进行设置，但是如果你把那些配置写在这儿，你就不用非要去记住那些命令了。

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p><code>_drafts</code></p>
      </td>
      <td>
        <p>

          drafts（草稿）是未发布的文章。这些文件的格式中都没有 <code>title.MARKUP</code> 数据。学习如何使用草稿.

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p><code>_includes</code></p>
      </td>
      <td>
        <p>

          你可以加载这些包含部分到你的布局或者文章中以方便重用。可以用这个标签
          <code>{% raw %}{% include file.ext %}{% endraw %}</code>
          来把文件 <code>_includes/file.ext</code> 包含进来。

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p><code>_layouts</code></p>
      </td>
      <td>
        <p>

          layouts（布局）是包裹在文章外部的模板。布局可以在 YAML 头信息中根据不同文章进行选择。
          这将在下一个部分进行介绍。标签
          <code>{% raw %}{{ content }}{% endraw %}</code>
          可以将content插入页面中。

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p><code>_posts</code></p>
      </td>
      <td>
        <p>

          这里放的就是你的文章了。文件格式很重要，必须要符合:
          <code>YEAR-MONTH-DAY-title.MARKUP</code>。
          永久链接可以在文章中自己定制，但是数据和标记语言都是根据文件名来确定的。

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p><code>_data</code></p>
      </td>
      <td>
        <p>

          格式化好的网站数据应放在这里。jekyll 的引擎会自动加载在该目录下所有的 yaml 文件（后缀是 <code>.yml</code>, <code>.yaml</code>, <code>.json</code> 或者 <code>.csv</code> ）。这些文件可以经由 ｀site.data｀ 访问。如果有一个 <code>members.yml</code> 文件在该目录下，你就可以通过 <code>site.data.members</code> 获取该文件的内容。

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p><code>_site</code></p>
      </td>
      <td>
        <p>

          一旦 Jekyll 完成转换，就会将生成的页面放在这里（默认）。最好将这个目录放进你的 <code>.gitignore</code> 文件中。

        </p>
      </td>
    </tr>
     <tr>
      <td>
        <p><code>.jekyll-metadata</code></p>
      </td>
      <td>
        <p>

          该文件帮助 Jekyll 跟踪哪些文件从上次建立站点开始到现在没有被修改，哪些文件需要在下一次站点建立时重新生成。该文件不会被包含在生成的站点中。将它加入到你的 <code>.gitignore</code> 文件可能是一个好注意。

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p><code>index.html</code> and other HTML, Markdown, Textile files</p>
      </td>
      <td>
        <p>

          如果这些文件中包含 YAML 头信息部分，Jekyll 就会自动将它们进行转换。当然，其他的如 <code>.html</code>, <code>.markdown</code>, <code>.md</code>, 或者 <code>.textile</code> 等在你的站点根目录下或者不是以上提到的目录中的文件也会被转换。

        </p>
      </td>
    </tr>
    <tr>
      <td>
        <p>Other Files/Folders</p>
      </td>
      <td>
        <p>

          其他一些未被提及的目录和文件如
          <code>css</code> 还有 <code>images</code> 文件夹，
          <code>favicon.ico</code> 等文件都将被完全拷贝到生成的 site 中。这里有一些使用 <a href="https://github.com/jekyll/jekyll/wiki/Sites">Jekyll 的站点</a>  ，如果你感兴趣就来看看吧。

        </p>
      </td>
    </tr>
  </tbody>
</table>
</div>

#### 发布到github-pages

将本地生成的网站目录结构上传到github仓库的master分支，我的用户仓库是zhanglianpin.github.io。

```sh
git push origin master
```

稍等1---2分钟，github-pages会自动将上传的Jekyll网站框架和内容生成静态网站内容，然后通过浏览器就可以访问网站内容了。

#### Gem和bundler

刚接触Ruby，对Gem和bundler之间的关系和区别有些疑惑。Gem比较好理解，就是一个软件管理器(安装、更新、卸载软件)，就像ubuntu里的apt，CentOS里的yum。那这个bundler又是干什么用的呐？简单的说它为一个具体的Project提供一个统一的运行环境，处理各种包的依赖关系，确保兼容性。一个Project也许会用到不同的Gem包，这些包之间的依赖关系和版本号如果人工管理的话还是挺没意思的，快乐的程序员们怎么会允许这样的事情发生，bundler诞生了。它使用一个Gemfile文件自动维护各个包的依赖关系。具体用法见[bundler][18]，[github-bundler][19]。Jekyll这个项目默认使用bundler作为项目各个包的依赖关系处理。

#### Liquid

Liquid是一个模板语言，支持基本的变量，逻辑控制等等基本的功能。使用Ruby语言编写，Jekyll中就是使用Liquid这种模块语言做页面的动态部分的处理。项目主页[Liquid][15]，github项目地址[github-liquid][16]。打开帮助文档，有一点编程基础的人花十来分钟就能掌握了，十分精简的模板语言。

#### YAML

YAML 是一种格式化标记语言。官方解释：YAML (/ˈjæməl/, rhymes with camel) is a human-readable data serialization language. It is commonly used for configuration files, but could be used in many applications where data is being stored (e.g. debugging output) or transmitted (e.g. document headers).官方网址[yaml][yaml]。

#### blog动态语言和静态语言

前面一直在说Jekyll是一个静态网站生成器。那这个Jekyll肯定支持一部分动态特性，要是只支持静态标记语言那就没意思了。blog的主要核心部分：一是支持好的编写blog内容的方式，像Markdown、Textile等轻量级标记语言; 二是支持一些动态特性，比如Tag、文章目录等。静态内容支持的语言有：Markdown（或 Textile）、Liquid 和 HTML & CSS。动态语言主要是模块语言Liquid(可以使用YAML的格式化数据)。


###  使用开源的Jekyll、Theme快速搭建博客

github上有特别多的使用Jekyll&&github-pages搭建的blog系统，且都是开源的。我使用的是[mzlogin/mzlogin.github.io][mzlogin] 。

Fork 指南

Fork 本项目之后，还需要做一些事情才能让你的页面「正确」跑起来。

1. 正确设置项目名称与分支。

   按照 GitHub Pages 的规定，名称为 `username.github.io` 的项目的 master 分支，或者其它名称的项目的 gh-pages 分支可以自动生成 GitHub Pages 页面。

2. 修改域名。

   如果你需要绑定自己的域名，那么修改 CNAME 文件的内容；如果不需要绑定自己的域名，那么删掉 CNAME 文件。

3. 修改配置。

   网站的配置基本都集中在 \_config.yml 文件中，将其中与个人信息相关的部分替换成你自己的，比如网站的 url、title、subtitle 和第三方评论模块的配置等。

   **评论模块：** 目前支持 disqus、gitment 和 gitalk，选用其中一种就可以了，推荐使用 gitalk。它们各自的配置指南链接在 \_config.yml 文件的 Comments 一节里都贴出来了。

   **注意：** 如果使用 disqus，因为 disqus 处理用户名与域名白名单的策略存在缺陷，请一定将 disqus.username 修改成你自己的，否则请将该字段留空。我对该缺陷的记录见 [Issues#2][3]。

4. 删除我的文章与图片。

   如下文件夹中除了 template.md 文件外，都可以全部删除，然后添加你自己的内容。

   * \_posts 文件夹中是我已发布的博客文章。
   * \_drafts 文件夹中是我尚未发布的博客文章。
   * \_wiki 文件夹中是我已发布的 wiki 页面。
   * images 文件夹中是我的文章和页面里使用的图片。

5. 修改「关于」页面。

   pages/about.md 文件内容对应网站的「关于」页面，里面的内容多为个人相关，将它们替换成你自己的信息，包括 \_data 目录下的 skills.yml 和 social.yml 文件里的数据。


#### 评论系统使用gitalk

Jekyll的工作模式意味着像评论这样的系统必须借助第三方评论插件，我选择使用了[gitalk][gitalk]，一个使用github自带的issue系统做的评论插件，不过只支持github用户登录、评论。还好吧，现在没有github账号的程序员也不多了。使用gitalk涉及到两个问题：1，自己的blog如何加载gitalk并显示？2，如何授权使用github用户登录自己的blog评论系统进行评论？

第一个问题比较简单，gitalk官网的install说明里说的比较清楚，使用links方式进行从外网加载到本地，在本地执行简单逻辑功能。

```javascript
<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
```
融合到Jekyll中使用的是[_inclus/comments.html][github-zhanglianpin.io-includes-comments.html]这个文件。

```javascript
var gitalk = new Gitalk({
            id: 'page.url',
            clientID: 'clientID',
            clientSecret: 'clientSecret',
            repo: 'repo',
            owner: 'owner ',
            admin: ['owner'],
            labels: ['gitment'],
            perPage: 50,
        })
```

初始化gitalk时需要一些参数，因为这些参数不用的blog需要使用自己的github账号等一些信息，因此是变量。这些参数通过Liquid模板语言提取出来作为了变量放在了格式化数据YAML文件[_config.yml][zhanglianpin.github.io-_config.yml]中。具体参数如下：

```
# https://github.com/gitalk/gitalk#install
gitalk:
    owner: zhanglianpin
    repo: blog-comments
    clientID: decb151c48f4a658ec81
    clientSecret: c2a95f788d773b3a25b68d0a3f25b78bedc80370
```

参考liquid语法很容易知道，引用上述变量的方法：

```javascript
{% raw %}
var gitalk = new Gitalk({
            id: '{{ page.url }}',
            clientID: '{{ site.gitalk.clientID }}',
            clientSecret: '{{ site.gitalk.clientSecret }}',
            repo: '{{ site.gitalk.repo }}',
            owner: '{{ site.gitalk.owner }}',
            admin: ['{{ site.gitalk.owner }}'],
            labels: ['gitment'],
            perPage: 50,
        })
        gitalk.render('gitalk-container')
		
{% endraw %}

```

			
简单说明一下上述参数，前面说了gitalk使用的是github project的issue功能实现的，因此上述参数大部分都和project的issue有关系。
repo指定那个github 仓库作为存储issue的地方，我的仓库是：[blog-comments][github-zhanglianpin.io-blog-comments]。owner和admin都是github的账号，我的是zhanglianpin。
clientID和clientSecret是用户授权github账号登陆第三方网站使用的。就像我现在的blog系统，如果在网站上点击使用github登陆按钮会跳转到授权页面，让你授权github账号登陆本blog的评论系统。
![gitalk-login](/images/posts/2018-02-27-build-blog-using-jekyll-and-github-pages/gitalk-login.png "gitalk-login")

这个clientID和clientSecret是你自己在github申请的。具体的申请步骤见[Creating an OAuth App][Creating-an-OAuth-App]。
具体填写内容可能比较关键，以我的blog为例，我写的参数如下：

![Creating-an-OAuth-App](/images/posts/2018-02-27-build-blog-using-jekyll-and-github-pages/Creating-an-OAuth-App.png "Creating-an-OAuth-App")

#### 搜索使用JS库

搜索使用的也是JS库的方式，只能搜索文章标题。关于搜索的代码还没分析，分析之后再分享，权当了解一下JavaScript语言了。


#### 数学公式支持使用JS库-MathJax

Markdown默认没有数学公式的支持，当然需要第三方插件，最简单的方式当然是外部JS库喽。使用MathJax的方法很简单：

```javascript
<script type="text/javascript"
        src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

我把这个外部js库放在了Jekyll的[_inclus/header.html][github-zhanglianpin.io-includes-header.html]。


#### 访问统计和字数统计等功能

@zhanglianpin

mojombo#1


### 引用资源

1. [jekyll](https://jekyllrb.com/ )
2. [GitHub Pages](https://pages.github.com/)
3. [Ruby](https://www.ruby-lang.org/en/)
4. [liquid](https://shopify.github.io/liquid/)
5. [yaml](http://yaml.org/)
6. [RubyGems](https://rubygems.org/)
7. [bundler](http://bundler.io/)
8. [Gitalk](https://github.com/gitalk/gitalk/)
9. [MathJax](https://www.mathjax.org/)
10. [使用Jekyll&&github-pages搭建的blog列表](https://github.com/jekyll/jekyll/wiki/Sites)




[1]: http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html        "Blogging Like a Hacker"
[2]: https://github.com/topics/jekyll       									 "jekyll"
[3]: https://github.com/topics/github-pages       							 	 "github-pages"
[4]: https://wanwang.aliyun.com/       							 	 			 "万网---阿里云旗下产品"
[5]: https://help.aliyun.com/knowledge_detail/35881.html?spm=a2c4e.11155515.0.0.xDePmY       							 	 			 "阿里云域名实名制认证"
[6]: https://www.bahutou.cn      							 	 			 	 "bahutou's blog"
[7]: https://pages.github.com/      							 	 			 "GitHub Pages"
[8]: https://zhanglianpin.github.io      							 	 		 "zhanglianpin.github.io"
[9]: https://help.github.com/articles/using-a-custom-domain-with-github-pages/   "Using a custom domain with GitHub Pages"
[10]: https://help.github.com/articles/adding-or-removing-a-custom-domain-for-your-github-pages-site/    "Adding or removing a custom domain for your GitHub Pages site"
[11]: https://help.github.com/articles/what-is-github-pages/     			    "What is GitHub Pages?"
[12]: https://github.com/mojombo     			    							"mojombo"
[13]: https://jekyllrb.com/     			    								"jekyllrb"
[14]: https://github.com/jekyll/jekyll     			    						"github-jekyll"
[15]: https://shopify.github.io/liquid/     			    					"liquid"
[16]: https://github.com/Shopify/liquid     			    					"github-liquid"
[17]: https://rubygems.org/    			    									"RubyGems"
[18]: http://bundler.io/    			    									"bundler"
[19]: https://github.com/bundler/bundler    			    					"github-bundler"
[20]: http://www.ruby-lang.org/en/downloads/    			    				"Ruby"
[21]: http://nodejs.org/					    			    				"NodeJS"
[22]: https://www.python.org/downloads/					    			    	"Python 2.7"
[BASH-WSL]: https://msdn.microsoft.com/en-us/commandline/wsl/about
[yaml]: http://yaml.org/
[mzlogin]: https://github.com/mzlogin/mzlogin.github.io
[gitalk]: https://github.com/gitalk/gitalk/
[github-zhanglianpin.io-includes-comments.html]: https://github.com/zhanglianpin/zhanglianpin.github.io/blob/master/_includes/comments.html
[zhanglianpin.github.io-_config.yml]: https://github.com/zhanglianpin/zhanglianpin.github.io/blob/master/_config.yml
[github-zhanglianpin.io-blog-comments]: https://github.com/zhanglianpin/blog-comments
[Creating-an-OAuth-App]: https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/
[github-zhanglianpin.io-includes-header.html]: https://github.com/zhanglianpin/zhanglianpin.github.io/blob/master/_includes/header.html