---
layout: post
title: 使用python获取天气预报
---


*	工具：python3.4 (配合 `re` 或 `BeautifulSoup`)

*	目标：获取天气预报，内容包括【时间】+【天气】+【气温】

*	[代码参考](https://github.com/jJayyyyyyy/cs/tree/master/just%20for%20fun/weather)

有了之前的[36kr新闻阅读器](https://jjayyyyyyy.github.io/2016/10/30/36kr_newsflash_reader.html)作为基础，我们不难写出更多基于python网络库的小程序。这次我们来写一个简单实用的脚本来获取天气预报，数据来源是[中国天气网](http://www.weather.com.cn/)。最终呈现在终端或者命令行的内容类似于: 

```bash

6号（今天） 多云 23 ~ 16℃

7号（明天） 小雨转中雨 22 ~ 15℃ 

```

<br/><br/>

##		1. 分析网页

这次的脚本也是一个Spider，所以第一步当然也是分析网页结构。打开[中国天气网](http://www.weather.com.cn/)

在搜索栏输入城市，这里以上海为例，可以看到地址栏变成了`http://www.weather.com.cn/weather/101020100.shtml`. 接着右键点击`7天`预报中的`今天`，选择`Inspect`(检查元素(N))，可以看到这段代码

```html
<ul class="t clearfix">
<li class="sky skyid lv2 on">
<h1>6日（今天）</h1>
<big class="png40 d00"></big>
<big class="png40 n00"></big>
<p title="多云" class="wea">多云</p>
<p class="tem">
	<span>23</span>/<i>16℃</i>
</p>
...
...
</li>
```

第2~7天的内容结构也是这样。

<br/><br/>

##		2. 获取网页内容

####	2.1 利用正则表达式

我们利用`urllib.request`获取网页内容，然后用[36kr新闻阅读器](https://jjayyyyyyy.github.io/2016/10/30/36kr_newsflash_reader.html)中提到的`re`模块来"暴力"提取关键信息。所谓`re`，就是Regular Expression，即正则表达式。它可以被分为很多类，差异比较可以看[这里](http://www.cnblogs.com/chengmo/archive/2010/10/10/1847287.html)。对于python所用的正则表达式，可以参考[官方文档](https://docs.python.org/3/library/re.html)，以及[这里](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143193331387014ccd1040c814dee8b2164bb4f064cff000)。

首先，我们引入两个模块:

```python
#!/urs/bin/enc python3
# -*- coding: utf-8 -*-
from urllib import request
import re
```

接着，下载网页内容:

```python
url = 'http://www.weather.com.cn/weather/101020100.shtml'
req = request.Request(url)
weekly_weather = request.urlopen(req).read().decode('utf-8')
```

这样，我们就把内容保存在了`weekly_weather`。接下来，我们分析第1节中的网页源码，进行正则匹配。观察以后可以发现:

*	对于`时间`，其结构是这样的: `<h1>([1-9].*)</h1>`。其中`[1-9]`表示匹配1~9开头的任意数字，`.*`表示匹配任意字符串。由此可以匹配出这一天是几号，如(11月)`6号`。

*	对于`天气`，其结构是这样的: `.*class="wea">(.*)</p>`，可以匹配出这一天是晴天、多云，或者其他天气。

*	对于`气温`，其结构是这样的: `<span>([0-9]*)</span>/<i>([0-9].*)</i>`。这样可以匹配出这一天是几度到几度，如`23~16℃`。

因此可以利用python的`re.findall()`提取出以上三项内容，并保存于`seven_days`，注意不要遗漏换行符`\n`:

```python
seven_days = re.findall(r'\<h1\>([1-9].*)\</h1>\n.*\n.*\n.*\>(.*)\</p>\n.*\n\<span\>([0-9]*).*\>([0-9].*)\</i\>\n', weekly_weather)
```

`re.findall()`会以`list`的形式返回所有匹配的字符串。由于每一天有三个匹配项，因此`seven_days`是一个`list`，而其每一个元素都是一个元组`tuple`。

最后就是将结果打印在屏幕上了:

```python
for day in seven_days:
	print(day[0], day[1], day[2], day[3], '\n')
```

<br/><br/>

####	2.2 使用BeautifulSoup

此外，还可以使用[BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/)这一利器来帮我们解析html，进而得到我们想要的内容。 `BeautifulSoup`的特点就是直观，可以通过标签类别、名字和属性等方式获得内容。

要使用`BeautifulSoup`，首先要安装必要的包:

```bash
$ pip3 install beautifulsoup4
```

接着引入模块:

```python
#!/urs/bin/enc python3
# -*- coding: utf-8 -*-
from urllib import request
from bs4 import BeautifulSoup
```

然后和之前一样，下载资源:

```python
url = 'http://www.weather.com.cn/weather/101020100.shtml'
req = request.Request(url)
weekly_weather = request.urlopen(req).read().decode('utf-8')
```

下一步，我们生成一个实例化一个BeautifulSoup对象，用html.parser去解析下载到的html资源，保存为一个有层次结构的soup:

```python
soup = BeautifulSoup(weekly_weather, 'html.parser')
```

如果分析一下上一节的html代码，我们可以发现`class="t clearfix"`是独一无二的。因此我们可以利用它来寻找7天的天气数据。需要注意的是里面的参数名是`class_`而非`class`:

```python
seven_days = soup.find('ul', class_='t clearfix').get_text()
```

现在seven_days的内容就变成了那一段html代码中去掉所有标签的内容，由于比较长，这里就不贴出来了。直接用`print(seven_days)`就能看到。

观察其特点，我们根据`\n`将其拆分，并舍去空白段，将有用信息存到一个`list`里面:

```python
seven_days = seven_days.split('\n')
days = []
for day in seven_days:
	if day != '':
		days.append(day)
	else:
		pass
```

至此，我们就可以在屏幕上输出天气信息了:

```python
for i in range(2):
	print(days[i*4])
	print(days[i*4+1])
	print(days[i*4+2])
	print(days[i*4+3])
	print('\n')
```

<br/><br/>

####	2.3 练习

以上，我们用两种方式完成了获取天气预报的小程序，作为脚本它是足够用了，不过我们还可以稍作改进，在参考github上的代码之前，以下几点就作为练习啦~

1.	简化优化代码，抽象成函数，模块化。

2.	如果在晚上使用，可以发现屏幕上输出的是明天加后天的天气。那么，如何获取今晚的天气（如气温）呢？

<br/><br/>

##	参考

1.	[Linux shell 正则表达式(BREs,EREs,PREs)差异比较](http://www.cnblogs.com/chengmo/archive/2010/10/10/1847287.html)

2.	[Python3 Library: re](https://docs.python.org/3/library/re.html)

3.	[廖雪峰_python教程_正则表达式](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143193331387014ccd1040c814dee8b2164bb4f064cff000)

4.	[BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/)

5.	[中国天气网](http://www.weather.com.cn/weather/101020100.shtml)

6.	[廖雪峰_python教程_map/reduce](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317852443934a86aa5bb5ea47fbbd5f35282b331335000)


