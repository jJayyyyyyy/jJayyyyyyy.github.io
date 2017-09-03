---
layout: post
title: 升级Mac OS后无法使用Jekyll
---

<br/>


####	问题

升级系统以后总是会出现各种各样的问题，包括速度变慢，包括有的软件不能继续使用。

这不，对Mac OS来了一次大升级，结果原来的Jekyll不能用了，原因包括新的 Mac OS 取消了一些文件夹的读写权限，以及某些依赖关系的改变。

####	可选的解决方案

1.	sudo gem install -n /usr/local/bin jekyll bundle

2.	清空Gemfile.lock

3.	修改Gemfile里面的jekyll版本号

大致步骤是这样。接下来运行 `jekyll s` 以后，会出现一些警告

```
WARN: Unresolved specs during Gem::Specification.reset:
      listen (< 3.1, ~> 3.0)
      rb-fsevent (>= 0.9.4, ~> 0.9)
      rb-inotify (>= 0.9.7, ~> 0.9)
WARN: Clearing out unresolved specs.
Please report a bug if this causes problems.
/usr/local/lib/ruby/gems/2.4.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:317:in `check_for_activated_spec!': You have already activated sass 3.5.1, but your Gemfile requires sass 3.4.22. Prepending `bundle exec` to your command may solve this. (Gem::LoadError)
	from /usr/local/lib/ruby/gems/2.4.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:32:in `block in setup'
	from /usr/local/Cellar/ruby/2.4.1_1/lib/ruby/2.4.0/forwardable.rb:229:in `each'
	from /usr/local/Cellar/ruby/2.4.1_1/lib/ruby/2.4.0/forwardable.rb:229:in `each'
	from /usr/local/lib/ruby/gems/2.4.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:27:in `map'
	from /usr/local/lib/ruby/gems/2.4.0/gems/bundler-1.15.4/lib/bundler/runtime.rb:27:in `setup'
	from /usr/local/lib/ruby/gems/2.4.0/gems/bundler-1.15.4/lib/bundler.rb:101:in `setup'
	from /usr/local/lib/ruby/gems/2.4.0/gems/jekyll-3.3.0/lib/jekyll/plugin_manager.rb:36:in `require_from_bundler'
	from /usr/local/lib/ruby/gems/2.4.0/gems/jekyll-3.3.0/exe/jekyll:9:in `<top (required)>'
	from /usr/local/bin/jekyll:22:in `load'
	from /usr/local/bin/jekyll:22:in `<main>'
```

大概意思是说，原来的一些转换规则过时了，现在使用了新的规则，并且如果用 git status 查看一下文件内容的话，发现 _site/ 中生成的本地预览文件全部被修改了。满屏的警告信息，我还以为跑飞了。。

####	其他

*	或者干脆不要Jekyll了，每次直接推 _post 就可以了，只是少了本地预览。GitHub会完成剩下的工作。

*	Liquid 语法升级了，可能需要修改 commentit 的模板。我是把下面这一行的小括号去掉就好了。

	```
	assign sorted_comments = (page.comments | sort: 'date')
	```


<br/><br/>

本文地址：[https://jjayyyyyyy.github.io/2017/09/03/jekyll_in_sierra.html](https://jjayyyyyyy.github.io/2017/09/03/jekyll_in_sierra.html)

(END)

---

##	相关阅读

*	[MacOS Sierra broke Jekyll?](https://www.reddit.com/r/Jekyll/comments/542vvh/macos_sierra_broke_jekyll/)

*	[troubleshooting](http://jekyllrb.com/docs/troubleshooting/#jekyll--mac-os-x-1011)

*	[howto-install-jekyll_homebrew-rbenv](https://gist.github.com/r-brown/a0b50d56cfb3596e0d17)
