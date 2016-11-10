---
layout: post
title: 36kr新闻阅读器
---


*	环境：python3.4 + Chrome + terminal/cmd

*	本文代码可以参考[36kr_newsflash_reader](https://github.com/jJayyyyyyy/36kr_newsflash_reader)

这篇文章，我们来学习如何用python写一个轻量级的资讯阅读器。我们的抓取目标是[36Kr快讯](http://36kr.com/newsflashes)。可以看到，这是一个比较简洁的资讯网站，上面会更新一些科技时讯(以前质量不错，现在良莠不齐)。不过由于这个网站结构比较清晰，所以比较适合作为我们的第一个栗子。

我们要写的这个阅读器，可以算是一个蜘蛛(Spider)。Spider的特点是简单轻便目标明确，其实可以理解为针对某个网页的小爬虫(Crawler)。更多相关对比可见参考资料。

下面开始进入正题。

<br/>

##	1. 分析网页结构

首先我们打开36kr快讯的[主页](http://36kr.com/newsflashes)。我们看到网站内容结构是这样的：【一行标题+一段简介】。因此我们的目标是：

*	抓取【标题和简介】，呈现在命令行或者终端，以此代替浏览器

*	每次仅打印出标题，以便快速浏览

*	如果需要阅读更多，再输出新闻简介

有了目标，我们该怎么开始呢？这还要从浏览器开始说起。我们平时基本是用浏览器看网页。浏览器工作的原理大致是这样：先通过HTTP等协议把内容从服务器取回本地，然后再进行渲染和呈现。我们的阅读器不需要复杂的渲染，只需要从服务器拿回内容，然后简单地输出文本即可。

那么，上面提到的【标题和简介】从哪里来呢？我们可以使用Chrome的开发者工具来寻找资源。首先按下F12，在选项卡中选择【Network】，然后刷新一下网页，在该选项卡中就能看到浏览器通过网络下载了很多资源。

在这么多资源中，寻找我们想要的内容有时候比较麻烦。当然，有一种比较简单粗暴的方式：把整个网页下载下来，然后用正则表达式等方式进行提取。也可以专门分析出某个资源的来源，再针对性地下载。对于36kr，详细的寻找资源的过程大家可以当作 **练习1** (hint: 点击一下网页下方的【浏览更多】，再观察新的资源)。这儿就直接给出两个可以验证的结果:

[地址1](http://36kr.com/newsflashes.json) [地址2](http://36kr.com/api/newsflash)

我们可以看到，两者都是json形式的数据，内容包含了我们所需的【标题和简介】。其中地址1是比较早的接口。入口2是前段时间发现的，从地址中的这个`api`我们可以猜测这是正式的接口，短时间内不会取消。因此我们就用[地址2](http://36kr.com/api/newsflash)作为获取资源的入口。

<br/>

##	2. 获取网页内容

有了以上的分析作为基础，我们就可以使用python功能强大的网络库，来获取和分析网页内容了。如果有兴趣，可以尝试一个叫做[requests](https://github.com/kennethreitz/requests)的神器。不过这次我们就使用`python3.4`自带的包(Package)和模块(Module):

*	`urllib.request` 用于下载资源。

*	`json` 用于解析json数据，也可以配合文件操作，将[地址2](http://36kr.com/api/newsflash) 的数据保存为容易观察分析的格式。

我们首先新建一个名为`newsflash.py`的文件，然后写入如下内容:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
from urllib import request
```

所谓下载资源，大体上就是一个客户端发出请求，然后服务端进行响应，客户端再接收的过程。我们要做的，就是回到Chrome点开主页资源`newsflashes`，结合开发者工具显示的信息，然后用Python来模拟浏览器的行为。

我们先来构造一个http请求。因为不用登陆，所以简单地利用`url+UA`的方式构造请求头即可。`url`就是刚才的`地址2`，`UA`就是`User-Agent`。于是乎: 

```python
url = 'http://36kr.com/api/newsflash'
req = request.Request(url)
req.add_header('User-Agent', 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36')
```

接着我们把这个请求发送出去，并把接收到的响应保存为resp:

```python
resp = request.urlopen(req)
```

再回到Chrome，我们可以看到成功接收响应的时候会返回一个200的状态码。所以我们可以以此为依据，初步判断请求-响应是否成功:

```python
print('Status:', resp.status, resp.reason)
```

保存退出后运行，可以看到状态码是200，和Chrome上面一样，说明响应成功。以后在抓取其他网页的时候可能会遇到各种奇怪的问题，有时候用这个状态码可以帮助我们分析解决。下一步，我们把内容读取出来。由于网络上传输的内容都是编码后的bytes类型，我们要将其解码。解码方式可以看到Chrome中写明了是`utf-8`。当然，做这些的前提是响应成功，所以我们要加上一行判断：

```python
if 200 == resp.status:
	data = resp.read().decode('utf-8')
else:
	print('Status Error!\n')
	exit(0)
```

这样，内容就保存到`data`中了。当然我们也可以将它保存为文件，然后打开看看里面到底是什么东西：

```python
with open('data.txt', 'w') as f:
	f.write(data)
```

保存退出后运行，然后打开`data.txt`。对比一下可以发现，里面的内容和[地址2](http://36kr.com/api/newsflash)呈现的内容是一样的。至此，我们完成了第一个小目标~

<br/>

##	3. 解析数据，呈现内容

不过现在还有一个比较大的问题。我们所看到的内容，目前还只是一个很长的字符串，很难观察，也不便于关键信息的提取。所以接下来我们将其转化为json格式的数据。首先在最上面导入`json`模块:

```python
import json
```

然后修改刚才保存文件的过程:

```python
json_data = json.loads(data)
with open('json_data.txt', 'w', encoding='utf-8') as f:
	json.dump(json_data, f, indent=4, ensure_ascii=False)
```

再打开`json_data.txt`，可以发现它已经整齐地排好格式了。稍加观察，可以发现在`data->items`的每一个元素中，`title`是我们想要的标题，`description`是新闻的详细描述，而`updated_at`是发出新闻的时间。这些就是我们想要的信息。接下来，我们试着将第一条新闻输出到屏幕上:

```python
news = json_data['data']['items'][0]
title = news['title']
time = news['updated_at']
detail = news['description']
print(title + '\n')
print(time)
print(detail)
```

保存退出后运行，可以看到屏幕上输出了标题，时间，还有描述。如果我们想要把所有的新闻都打印出来，可以用for循环的方式。另外，一般阅读的时候我们看看标题就好了，如果对某个标题感兴趣，再告诉阅读器来输出细节，包括时间和内容。这样，我们会有更好的阅读体验，不会像刚才一样，屏幕上一堆东西影响阅读。我们回到编辑器，把上一段代码删掉，然后写一个循环:

```python
for news in json_data['data']['items']:
	us_input = input('\n' + news['title'])
	if 'q' == us_input:
		print('\nDone!\n')
		break
	elif 'd' == us_input:
		print(news['updated_at'] + '\n' + news['description'] + '\n\n')
	else:
		pass
print('\nDone!\n')
```

然后保存退出再运行，看看效果如何。按下回车可以输出一条新闻标题。输入`d`就可以得到时间和详细内容。如果看到一半不想看了，可以用`ctrl+c`结束。也可以输入`q`退出，屏幕上会打印一个`Done!`，表示完成。

至此，我们这个简单的新闻阅读器已经差不多完成啦。不过我们发现，一次只能看10条新闻。如何才能显示更多呢？这个就当作留给大家的 **练习2** 吧 (hint: 和练习1有关)~

最后我们还可以做一下适当的封装，把这些都包裹成函数，只留下接口。代码就不在这儿贴了，请参考 [36kr_newsflash_reader](https://github.com/jJayyyyyyy/36kr_newsflash_reader)

这么二三十行代码就能完成一个简单的新闻阅读器，用python是不是很方便~以后我们还会用python做一些其他的好玩的东西，我们下次再见~

<br/>

##	参考：

*	[Spider与Crawler不同点](http://www.admin5.com/article/20080825/100523.shtml)

*	[分析动态网页](https://www.zhihu.com/question/21471960/answer/81061538)

*	[python3-cookbook](http://python3-cookbook.readthedocs.io/zh_CN/latest/c06/p02_read-write_json_data.html)

*	[How do I write JSON data to a file in Python?](http://stackoverflow.com/questions/12309269/how-do-i-write-json-data-to-a-file-in-python)

*	[Saving utf-8 texts in json.dumps as UTF8, not as \u escape sequence](http://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence)

*	[liaoxuefeng](http://www.liaoxuefeng.com/)

*	[more](https://github.com/jJayyyyyyy/36kr_newsflash_reader/)

<br/>

---

*	20161110 更新(补充说明): 
	
	第2节中，我们用`if 200 == resp.status`来判断是否成功响应，虽然能够执行，但其实不是一个好的实践。
	
	反例1. 如果服务器直接返回`404`的状态码，比如输入错误的地址1: `http://36kr.com/newsflash.json`，那么python会直接抛出`urllib.error.HTTPError`
	
	反例2. 如果输入错误的地址2: `http://36kr.com/api/newsflashes`，服务器会返回`200`的状态码，但是响应的实际内容是一个`404`页面，其中肯定木有我们想要的数据。这时候再用`json.loads()`就会出现`ValueError`，因为内容格式不对。
	
	所以，更好的方式是利用`try...except...`，参见[这里](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143191375461417a222c54b7e4d65b258f491c093a515000)。这样，程序运行异常时，会跳到错误处理代码，而不是直接退出。