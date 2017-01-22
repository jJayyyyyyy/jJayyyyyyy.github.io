---
layout: post
title: 使用Jekyll生成博客
comments:
  - author:
      type: full
      displayName: jJayyyyyyy
      url: 'https://github.com/jJayyyyyyy'
      picture: 'https://avatars.githubusercontent.com/u/8694379?v=3&s=73'
    content: |-
      comment zone test

      markdown test

      &grave;&grave;&grave;python
      print(&apos;hello world&apos;)
      &grave;&grave;&grave;
    date: 2017-01-22T09:44:34.957Z

---


本博客利用Jekyll生成，并且放在GitHub Pages上面。

* ### 关于如何搭建  

	关于如何搭建，网络上已经有太多范例。这里不再赘述。


* ### 一些经验

	1. 首先，网络状况要好。`gem`不像`apt-get install`一样会有进度提示，如果出网限制很严重，更会让人抓狂。

		早上比晚上稍微好点。不需要等太久。如果可以自由全速访问Google+Stack Overflow+GitHub，下面的问题都不是问题。
		
		当然也可以不用下载按照这么多软件，这些都是为了本地运行看效果用的。直接按照[Jekyll的目录结构](https://jekyllrb.com/docs/structure/)写好文件，然后上传到你的Pages项目，GitHub后台会帮你处理并生成网页。

	2. Ruby原意是红宝石。Gem原意是宝石，在这里里它是软件包管理工具。
		
		`Rubygems`最好升级到最新。如果是老一点的版本，会提示你`Rubygems is not threadsafe, so your gem will be installed one at a time.`

		升级后是否像迅雷一样多线程加快速度呢？我不知道，傻乎乎地下载安装完了所有组件之后才升级的。

	3. Jekyll的出现，其本意是为了省时间(我猜的)。所以最快搭建的方法是搜索`jekyll theme`然后选一个主题(模板)，修改`_config.yml`并标注主题出处就好了。

		另一方面，想要自己搭建博客的人，估计都有一颗爱折腾的心。如果时间充裕，倒是可以摸索一下。
		
		我试过一些主题，不过目前还是选择了原生的`minima`，特点就是简洁。
		
		> Minima is a one-size-fits-all Jekyll theme for writers. It's Jekyll's default (and first) theme. It's what you get when you run jekyll new.
		
		从github上下载[minima](https://github.com/jekyll/minima)，到处修修改改，配合浏览器的开发者工具看看效果即可。不过需要注意版本问题。

	4. 版本问题

		使用`$ gem install jekyll bundler`下载的可以理解为稳定版。从github上拿下来的是正在开发的，所以和稳定版稍有差别。
		
		`$ jekyll new .`之后，利用`$ bundle show minima`可以看到`minima`的路径，这是默认的模板。
		
		从github上拿下[minima](https://github.com/jekyll/minima)的最新版，从中拿出`_includes/head.html`，覆盖默认的`head.html`时，	发现网页的样式不见了！
		
		![???](http://newsimg.5054399.com/uploads/userup/1609/14145AK119.jpg)
		
		最终通过浏览器的Inspect，发现样式的路径不对，稳定版的`head.html`第11行是` ... "/css/main.css" ...`，而最新的已经在5天前换到了`... "/assets/main.css" ... `所以浏览器找不到这个`css`了
		
		所以使用的时候要用github上的release版本，而不是直接在项目页clone或者download下来。

	5. 其他问题
		
		想把所有步骤全都写下来，不过这太繁琐了。而且目前博客还没有完全定型。上面说道，只要有很好的网络，可以自由连接Google+Stack Overflow+GitHub，这些都不会是问题。需要的时候再找，没必要全部记下来。
		
* TODO

	1. 接下来准备写的内容

		* 改进: 支持upload文件的SimpleHTTPServer(py2+py3)
		* newsflash.py: 利用python获取36kr新闻标题
		* translate.py: 基于有道API的翻译工具
		* weather.py: 获取天气情况
		* nc+crontab+python: 定时批量扫描端口，邮件通知结果
		* 其他知识总结

	2. 基于<多说>的<评论区>
	
	3. 文章底部增加: 上一篇 / 下一篇
	
	4. 同步微信公众号

	5. 增加<邮箱>

	6. <关于>，增加内容


本文参考链接(包括搭建博客时遇到的一些有意思的网站)

> [阮一峰的博客](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)
>
> [GitHub Pages](https://pages.github.com/)
>
> [w3school](http://www.w3school.com.cn/index.html)
> 
> [hex code of colors](http://www.color-hex.com)
> 
> [Character Entity Reference Chart](https://dev.w3.org/html5/html-author/charref)
> 
> [基于图片显示与隐藏](http://www.nowamagic.net/librarys/veda/detail/1017) (Labtocat)
> 
> [HTML的footer置于页面最底部的方法](http://blog.csdn.net/jazywoo123/article/details/9866765) (还没改好，只是在<footer>中添加了`style="..."`)
> 
> [许可证-知乎](https://www.zhihu.com/question/28292322)
> 
> [许可证-博客园](http://www.cnblogs.com/ncore/p/3232294.html)
> 
> [黑人小哥](http://newsimg.5054399.com/uploads/userup/1609/14145AK119.jpg)