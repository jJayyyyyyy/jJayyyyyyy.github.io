---
layout: post
title: python3重写SimpleHTTPServerWithUpload
---


##	注意

*	为方便区分，以下用`py2`指代`python 2.7.6`，用`py3`指代`python 3.4.3`

*	代码在[这里](https://github.com/jJayyyyyyy/cs/tree/master/just%20for%20fun/file_transfer/http)

<br/>

##	摘要

[bones7456](http://luy.li/2010/05/15/simplehttpserverwithupload/)同学和[BUPTGuo](http://buptguo.com/2015/11/07/simplehttpserver-with-upload-file/)同学制作和完善了`SimpleHTTPServerWithUpload`的`py2`版本。由于python的2.7和3.4有较多不同特性，因此我根据以上两位同学的思路，重写了很多函数，制作了基于`py3`的版本。主要改动如下：

*	改写为基于`py3`的版本

*	移除了`StringIO`，不使用`copyfile()`。需要传输的信息全都用`str`。处理完逻辑后，再用`utf-8`编码为`bytes`，直接用`wfile.write()`进行网络传输。

*	修改`html`的部分标签顺序

<br/>

## 正文

##	1. 背景介绍

如同[这篇文章](http://coolshell.cn/articles/1480.html)所说

> 如果你急需一个简单的Web Server，但你又不想去下载并安装那些复杂的HTTP服务程序，那么Python是一个不错的选择。

在`py2`中内置了一个`SimpleHTTPServer`模块，从名字可以看出这是一个简单的HTTP服务器程序。在终端输入如下命令：

```bash
#if it is py2
$ python -m SimpleHTTPServer [8000]

#if it is py3
$ python3 -m http.server [8000]
```

就可以在目录下快速建立一个HTTP服务器。用这个方法可以方便地共享文件，只需要在浏览器中输入`http://ip:8000`就可以访问并下载文件了，其中`ip`是你的`局域网ip`。不过python内置的模块并没有提供上传功能。如[这里](http://luy.li/2010/05/15/simplehttpserverwithupload/)所说：

> 但是，某一天，你需要从同学哪里复制一个文件到本机，然后你就会跟你同学说，XX，共享下某目录。当你以为可以用http来访问他的8000端口的时候，他却告诉你，不好意思，我是windows啦~~

为此，[bones7456](http://luy.li/2010/05/15/simplehttpserverwithupload/)同学对这个模块进行了改造，添加了上传功能，这样就让局域网内的分享变得更加方便了。后来[BUPTGuo](http://buptguo.com/2015/11/07/simplehttpserver-with-upload-file/)同学进行了[一些改进](http://buptguo.com/2015/11/07/simplehttpserver-with-upload-file/)。在这里再次感谢两位同学的成果和开源精神～

<br/>

##	2. 基于py3的模块

经过以上两位同学改造的模块是基于`py2`的，由于`py2`和`py3`有较多不同特性，直接用`$ python3 xxx`运行会产生很多错误。所以，让我们撸起袖子开始改造轮子吧~

改造过程从分析输出的错误信息开始。先把`py2`的代码全部复制到一个文件`py3server.py`，然后根据错误信息一步步修改：

###	2.1 实现访问和下载

*	print

	```bash
	......
		File "py3server.py", line 57
			print r, info, "by: ", self.client_address
				  ^
	SyntaxError: Missing parentheses in call to 'print'
	```
	
	这个很明显，直接全部改成`print()`。

*	BaseHTTPServer

	```bash
	......
	Traceback (most recent call last):
		File "py3server.py", line 16, in <module>
			import BaseHTTPServer
	ImportError: No module named 'BaseHTTPServer'
	```

	`py2`的`BaseHTTPServer`模块在`py3`中变成了`http.server`模块。后面的`class SimpleHTTPRequestHandler`将要继承该模块下的一个`handler`，所以也要改。最后在定义`test()`的地方也要修改。

	ps：对于`handler`，从STM32开发的经历来看，我觉得应该是一种类似于中断处理程序的东西。

*	`stringIO`

	通过google我们可以知道，`py3`区分了`BytesIO`和`StringIO`，而`py2`中只有`stringIO`。这个区别会带来很多问题。后面可以看到，为了代码不太丑陋，`xxIO`被愉快地弃用了。不过首先让我们`from io import StringIO, BytesIO`，看看接下来会发生什么。

	以上这样改完之后，我们发现`$ python3 py3server.py`已经能运行了～再用浏览器来访问一下。结果。。是一大堆错误信息。。不用急，一步一步分析。

*	unquote

	```bash
	......
		File "py3server.py", line 214, in translate_path
			path = posixpath.normpath(urllib.unquote(path))
	AttributeError: 'module' object has no attribute 'unquote'
	```

	错误信息中最关键的是最后的内容。这里是版本问题，`py3`中应当用`urllib.parse.unquote()`和`urllib.parse.quote()`，而不是直接`urllib.unquote()`。修改后运行，再通过浏览器访问。

*	stringIO

	```bash
	.....
		File "py3server.py", line 42, in do_GET
			self.copyfile(f, self.wfile)
		File "py3server.py", line 236, in copyfile
			shutil.copyfileobj(source, outputfile)
		File "/usr/lib/python3.4/shutil.py", line 70, in copyfileobj
			fdst.write(buf)
		File "/usr/lib/python3.4/socket.py", line 394, in write
			return self._sock.send(b)
	TypeError: 'str' does not support the buffer interface
	```

	这是类型错误，发生在浏览器进入根目录的时候。错误信息的意思是说`copyfileobj()`只接受`buffer-like`对象，而不能用`str-like`对象作为参数传入。阅读代码，追踪出问题的`f`，它是由`do_GET()`得到的，后者又经历了`send_head()`。

	在`send_head()`的最后可以看到它`return`了一个`f`，而它是由上面几行的`f = open(path, 'rb')`得到的。按理来说，`f`一个二进制打开的的文件，应该是`buffer-like`的对象，应该不会错在这里才对。(补充一下，打开的文件`f=open(filname, 'rb')`可以算是`buffer-like`，但是如果用`data=f.read()`，那么`data`是一个`bytes`对象。如果用`copyfileobj()`，会报错提示缺少`read`属性。)

	别急，我们再来仔细看看`send_head()`。可以发现，这个函数首先对所请求的`path`进行检查，如果`path`是目录则`return list_directory(path)`。如果`path`不是目录，那就说明已经定位到文件了(如果存在)，因此下半部分就是要展示(传输)文件了。

	在这儿我们可以简单验证一下。在根目录下新建一个`readme.txt`，里面输入`hello, world`，保存退出。然后在地址栏输入`http://ip:port:8000/`，这时还是会出现刚才的错误。但是如果输入`http://ip:port:8000/readme.txt`，就能发现屏幕上出现了`hello world`(另外可以看看终端的输出，不再是错误信息，而是`..."GET /readme.txt HTTP/1.1" 200 -`)。说明我们刚才的猜测是对的。

	至此，我们暂时把文件保存为`py3server_v1.py`，以便参照。接下来的`v2`，我们要让目录页也能正确显示。

*	正确显示目录
	
	接下来我们进入`list_directory()`内部，可以看到里面赫然写着`f = StringIO()`。好嘛，这不就是红果果的`str-like`对象吗！把这个传回给一个只接受`buffer-like`对象的家伙可不会出错嘛！从这里也能看出，`py3`对于数据类型的区分更严格了。另外，关于`py3`中`StringIO`和`BytesIO`的内容，可以参考[这里](http://www.kancloud.cn/thinkphp/python-guide/39358)和[这里](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431918785710e86a1a120ce04925bae155012c7fc71e000)。

	话不多说，我们先试着把这一行改成`f = BytesIO()`，重启服务，刷新网页。结果上一个错误没了，其他错误又冒出来一大堆。。

	```bash
	......
		File "py3server.py", line 40, in do_GET
			f = self.send_head()
		File "py3server.py", line 144, in send_head
			return self.list_directory(path)
		File "py3server.py", line 176, in list_directory
			f.write('<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">')
	TypeError: 'str' does not support the buffer interface
	```

	阅读错误信息，又是刚才的`TypeError: 'str' does not support the buffer interface`。得，这不又回来了？这刚才的力气白花了。。

	看官且慢！其实如果再往上读两行，发现出错地点是不一样滴～可以看到，错误出现在`f.write()`身上。原来的`f`是一个`StringIO`对象，用`f.write()`时的参数可以直接用字符串，比如`f.write('hi')`。不过当我们用了`f = BytesIO()`后，给`f`写值时就需要先进行编码了，也就是`f.write('hi'.encode('utf-8'))`，或者用`f.write(b'hi')`。我试过用这种方式把相应的地方进行改动，包括[这里](http://blog.csdn.net/myjiayan/article/details/46366825)提到的用`f.seek(0)`回到文件最开头，同时修改`do_POST()`的内容。不过，这里不打算对这样的修改再作进一步描述了，因为这种削足适履的方法会让代码变得很丑。。

	让我们回想一下，最终不就是要把内容复制到`wfile`上吗(放到这上面的内容，应该会有一个`handler`把它带到网络上进行传输)？产生这些错误，都是因为`copyfileobj()`(躺枪_(:з」∠)_)。不用它，直接写入`wfile`怎么样？

	结果证明是可行的。从[这里](https://docs.python.org/3/library/socketserver.html)可以看到可以用`wfile.write()`方法，写入类型为`bytes`的参数。现在问题就简单了，把`StringIO`还是`BytesIO`全部扔掉，然后把要传输的内容全都用字符串表示`f = 'hello'`，接着`f.encode('utf-8')`，最后直接`self.wfile.write(f)`进行传输就行了。

	有了这些说明，再去读这部分重写的代码，应该就很容易了。结构是这样的:

	```python
	# in list_directory()
	...
	f = (html_in_str)
	f = f.encode('utf-8')
	length = len(f)	# 注意, 内容长度是编码后的长度
	self.send_response(200)
	self.send_header("Content-type", "text/html")
	self.send_header("Content-Length", str(length))
	self.end_headers()
	return f

	# in do_GET()
	...
	self.wfile.write(f)
	```

	我们将这个文件定为`py3server_v2.py`，对应的改动有以下几点。
	
	*	`do_GET()`现在的内容：

		```python
		f = self.send_head()
		if f:
			self.wfile.write(f)
		```

	*	`do_HEAD()`现在的内容：

		```python
		f = self.send_head()
		```

	*	在`send_head()`的最后，将`return f`改为

		```python
		data = f.read()
		f.close()
		return data
		```
	*	`list_directory()`改动较大，参考`py3server_v2.py`文件(包含`html`的修改)。

	*	去掉`copyfile()`，去掉`from io import StringIO, BytesIO`，减少冗余。

*	`py3`原生版本的方法

	`http.server`模块的思路是这样：	

	> 建立一个`list`对象`r`--->元素为字符串，分别写入`html`--->用`join`连成字符串--->编码成`r_encoded`--->...
	
	到这一步两者总体思路是类似的。但是下一步，`py3`的原生版本还是做了`f = BytesIO()`，然后 `f.write(r_encoded)`。
	
	**我没有想明白，为什么一定要用`BytesIO`呢？直接新建字符串对象，然后编码传入`wfile`，同样是在内存中操作数据啊。**

	**可能是存在内存操作分配更加方便，整存整取，回收等原因？那也不应该啊，因为str和list都是很常见的对象，如果有很多缺点那还得了。。还是有其他原因？另一方面，`py3server.py`的方式目前也能工作正常。我在知乎提到了[这个问题](https://www.zhihu.com/question/50716575?from=profile_question_card)，希望能够得到解答。**

如果抛开这个问题不管，现在介个基于`py3`的`py3server_v2.py`服务端已经可以用浏览器正常访问和下载了。还有一些细节，比如`fs = os.fstat(f.fileno())`，这里不再详细描述，通过搜索引擎可以很快了解。让我们先去吃点东西。下一节，我们通过重写`do_POST()`来实现上传功能。

###	2.2 实现上传

有了上面一节的说明，又有前面两位同学的思路和框架，重写上传功能应该是驾轻就熟了。所以下面直接进入重点。

在`py3server_v2.py`中，我们已经写好了一个简单的用于上传的前端部件。

```html
<form ENCTYPE="multipart/form-data" method="post">
	<input name="file" type="file"/>
	<input type="submit" value="upload"/>
</form>
```

下面还要进行一些修改

*	`do_POST()`处理POST请求

	首先去掉`f=StringIO()`，直接用字符串，写入一个基本的`html`页面，用于呈现`upload`之后的信息(上传成功/失败)，并和响应头信息一起发回给浏览器。

*	`deal_post_data()`处理POST数据

	如果不逐行*研究其意义*的话，改起来也是很快的，找到`py2`和`py3`的区别，同时注意编码即可。具体代码可以参见`py3server_v3.py`

*	*还是想理解这几段代码？*

	`do_POST()`的代码还好，只要懂一点`HTML`和`HTTP`就能看懂。而`deal_post_data()`一开始我也看不太懂。幸好之前测试的时候发现了`wfile`和`rfile`的秘密，我们可以用这个来看看这个函数到底是`deal`了什么数据：

	首先在服务端，我们注释掉`do_POST()`和`deal_post_data()`两个函数。然后重新写一个`do_POST()`读出准备接收的所有数据:
	
	```python
	def do_POST(self):
		for i in range(8):
			print(self.rfile.readline().decode('utf-8'))
	```

	接着我们在客户端新建一个准备上传的文件，命名为`1.txt`，里面写入内容`test`。打开chrome开发者工具的`Network`。`Choose File`选择`1.txt`，点击`upload`。看看发生了什么有意思的事～
	
	终端输出的内容+`Network`中的内容，有这些做参照，加上`py3server_v3.py`中的小注释，代码比较很容易懂了。这个算作思考题吧:)

至此，我们完成了上传功能的重写。`py3_SimpleHTTPServerWithUpload.py`大功告成。我们也可以像[bones7456](http://luy.li/2010/05/15/simplehttpserverwithupload/)同学一样，喂它做一个`alias`，以后就可以方便地在局域网中共享文件了～

##	3. 其他内容

*	[这里](https://github.com/jJayyyyyyy/cs/tree/master/just%20for%20fun/file_transfer)还有另外几个文件传输的小脚本可以作为参考或者练练手。

*	`GitHub`的通过网站新建文件时的`preview`貌似要去访问服务器，而没有像[jser](http://mahua.jser.me/)一样用js本地化的预览。

*	BUPTGuo同学还在gist上面留了几个[TODO](https://gist.github.com/BUPTGuo/007a6e589c0d2e48aac6)，

	> TODO: 点击中文目录时，终端输出为 unicode 编码，回头尝试修改
	> 
	> TODO: 尝试 ipv6支持

以下是对于`TODO`的一些思考和尝试。

1.	终端输出的可能不算是unicode。根据[这里](http://stackoverflow.com/questions/912811/what-is-the-proper-way-to-url-encode-unicode-characters)和[这里](https://en.wikipedia.org/wiki/Percent-encoding)，称其为`percent-encoding`或`url-encoding`比较合适。

	以`中文`这两个字为例。根据[这里](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431664106267f12e9bef7ee14cf6a8776a479bdec9b9000): python3中的字符串是以Unicode编码的。如果知道字符的整数编码，还可以用十六进制这么写str：
	
	```python
	>>> '\u4e2d\u6587' #this is unicode
	'中文'
	```

	以`Unicode`表示的`str`通过`encode()`方法可以编码为指定的`bytes`，以便在网络上传输。
	
	```python
	>>> '中文'.encode('utf-8')
	b'\xe4\xb8\xad\xe6\x96\x87'
	```

	如果在server的根目录下建立一个叫做`中文`的目录，然后在浏览器中访问。通过观察Chrome的开发者工具，可以看到`Request Header`里面的`url`对应的`中文`是这样的
	
	```
	%E4%B8%AD%E6%96%87
	```

	通过对比可以看到，`utf-8-encoding`之后的编码的`\x`变成了`%`。在[这里](http://baike.baidu.com/view/73.htm)可以看到，两者都是`转义字符`，只不过应用场景不一样。
	
	另一方面，利用Chrome的开发者工具，可以看到在`Request Headers`里面，不管是用`GET`还是`POST`，如果路径是中文，`url`那一段就会被`percent-encoding`。所以我觉得，这一步编码应该是浏览器做的，在服务端的终端只是把收到的`GET`或者`POST`的`url`打印出来了。
	
	阅读代码后发现，不管是在`py2`的`BaseHTTPServer.py`还是在`py3`的`http.server.py`，打印这行信息靠的是`log_message()`中用的`sys.stderr.write()`函数(方法)。
	
	同时也发现，请求信息存放于`self.requestline`，其中包含了路径信息，可以用正则表达式路径提取出来。比如在`do_GET()`的最后加上这么几行：
	
	```python
	path = re.match(r'.* /(.*)/ HTTP',self.requestline).group(1)
	print(path)
	```
	
	至于如何实现，和`do_GET()`一样，我们可以重写`log_message()`或者调用它的`log_request()`。后者如下：
	
	```python
	def log_request(self, code='-', size='-'):
		path1 = self.requestline
		m = re.match(r'.* /(.*)/ HTTP', path1)
		if m:
			path2 = m.group(1)
			path3 = urllib.parse.unquote(path2)
			path4 = path1.replace(path2, path3)
			self.requestline = path4
		self.log_message('"%s" %s %s',self.requestline, str(code), str(size))
	```

	```python
	def do_POST(self):
		for i in range(8):
			print(self.rfile.readline().decode('utf-8'))
	```

	
	不过感觉这个没有必要，终端输出反正没人看。。复杂了还容易出错。另外，英文路径编码前后是一样的。

2.	【ipv6支持】估计要牵涉到更底层吧，到`BaseHTTPServer`这一层才import了`socket`，相当于对`SimpleHTTPServer`隐藏了`socket`。从[这里](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432004374523e495f640612f4b08975398796939ec3c000)我们可以知道，如果要用`ipv6`，则需要`s = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)`。

	所以我们的目的就是要修改socket的参数，首先找到`test()`这个函数，然后找到它的参数`ServerClass = http.server.HTTPServer`，在python目录中找到`http`这个文件夹中的`server.py`，搜索`HTTPServer`，得知它是继承了`socketserver.TCPServer`这个类，再去`python目录`下找到`socketserver.py`这个文件，在`TCPServer`这个类中可以看到`address_family = socket.AF_INET`(第415行)，也就是默认用的`ipv4`。如果修改为`AF_INET6`(可能需要sudo)并保存，然后在浏览器地址栏输入`http://[::1]:8000`，就可以通过ipv6访问了(`[::1]`是ipv6形式的localhost)。同时也注意到，ipv4仍然能够访问。(以上内容基于`py3`，但`py2`类似)

	还有其他测试方法：

	```bash
	$ ping6 xxxx%eth0:8000
	$ nc -zv -6 localhost 8000
	$ nc -zv -6 ::1 8000
	```

	不过话又说回来，要这么往下改就比较复杂了，失去了原来的轻便。

<br/><br/>

##	4. 参考

[bones](http://luy.li/2010/05/15/simplehttpserverwithupload/)同学

[BUPTGuo](http://buptguo.com/2015/11/07/simplehttpserver-with-upload-file/)同学

[google](https://www.google.com)

[Stack Overflow](http://stackoverflow.com/)的答友们

[python-docs](https://docs.python.org/3.4/)及源文件

[liaoxuefeng](http://www.liaoxuefeng.com)老师

[ztelur](http://ztelur.github.io/2016/05/28/Python%E4%B8%AD%E7%9A%84plisttext%E5%92%8CHTTP%E7%9A%84Content-Type/)

[许伟林](http://zhihu.com/question/19760989/answer/12949287)

[Lesca技术宅](https://lesca.me/archives/how-to-ping-ipv6-address.html/comment-page-1#comment-177260)

[chaimg](http://blog.csdn.net/chaimg/article/details/8234470)

由于前期一些搜索内容忘了保存地址了，所以参考资料的出处可能有一些遗漏。。。

<br/><br/>

##	后记(20161009 更新)

~~`Jekyll`默认使用的`kramdown`并不能很好地支持`markdown`的代码段，也就是类似下面这种形式的`code block`。~~

```
 ```
 code
 ```
```

一番寻觅之后找到了[Redcarpet](https://george-hawkins.github.io/basic-gfm-jekyll/redcarpet-extensions.html)， 食用方式如下：

1. `$ gem install redcarpet`

2. 修改`_config.yml`，注释掉`markdown: kramdown`，下面加上一行`markdown: redcarpet`

3. 如果有`Gemfile`，则添加一行`gem "redcarpet"`

4. `$ jekyll build` + `$ Jekyll serve`

<br/>

##	后后记(2016.10.10 00:12更新)

替换为`Redcarpet`后，GitHub竟然给我发来一封邮件说不再支持`Redcarpet`，而且可能随时停用这个markdown的渲染引擎，让我使用默认的`kramdown`，因为它已经支持了全部特效(包括我需要的`code block`)。无语了，难道要我全都使用Liquid的`highlight-code-language`这样的语法吗(这在原生的markdown语法中是没有的)。。。

后来无意中发现，其实`kramdown`还是支持`code block`的，只是它对`markdown`进行`parse`要求更加严格了，有点像python靠是否对齐来判断是否属于同一级。具体来说，就是在`*`或者`1.`后面不能接空格` `，而应该用`tab`对齐，否则下面用`tab`缩进的`code block`就不能正常显示。

目前已经换回`kramdown`，行首空格全部换为`tab`。
