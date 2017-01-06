---
layout: post
title: 再谈python中的url编码
---


本文来自<a href="https://github.com/ianva/vim-youdao-translater">这个项目</a>引出的问题。该项目是一个vim插件，可以利用有道词典翻译英文单词。挺棒的一个项目，不过代码中对url进行编码的部分可能存在一些问题，已提交Issue，这里进行记录备份与细节补充。

文中用`py2`指代python2.7，用`py3`指代python3.4。

<br/>

##	1. 问题描述

对于url的编码，应该进行`percent-encoding`，而不是`str.encode()`。

py3中应当用`urllib.parse.quote()`进行`percent-encoding`，py2中对应的方法是`urllib.pathname2url()`。

<br/>

##	2. 问题定位

[ydt.vim](https://github.com/ianva/vim-youdao-translater/blob/master/plugin/ydt.vim#LC53)的第53行

<br/>

##	3. 示例

原项目中，主要通过

```python
url = 'http://dict.youdao.com/fsearch?q=' + word.encode('utf-8')
```

来获取翻译内容。下面以`word='hello'`和`word='你好'`为例，分别进行说明。

*	py2+英文(结果相同，不影响使用):

```python
>>> 'hello'.encode('utf-8')
'hello'
>>>
>>> urllib.pathname2url('hello')
'hello'
```

*	py2+中文(结果不同):

```python
>>> '你好'.encode('utf-8')
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)
>>>
>>> urllib.pathname2url('你好')	
'%E4%BD%A0%E5%A5%BD'
```

可以看到对中文用`str.encode('utf-8')`会直接报错，换成`gbk`, `gbk2312`也是一样。

*	py3+英文(结果不同)

```python
>>> 'hello'.encode('utf-8')
b'hello'
>>>
>>> urllib.parse.quote('hello')
'hello'
```

注意到`str.encode()`之后的结果中多了一个`b`，根据[这里](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431664106267f12e9bef7ee14cf6a8776a479bdec9b9000)

>如果要在网络上传输，或者保存到磁盘上，就需要把str变为以字节为单位的bytes。

这时如果再用

```python
url = 'http://dict.youdao.com/fsearch?q=' + word.encode('utf-8')
```

也会报错，因为`str`与`bytes`类型不能直接拼接。

*	py3+中文(结果不同)

```python
>>> '你好'.encode('utf-8')
b'\xe4\xbd\xa0\xe5\xa5\xbd'
>>>
>>> urllib.parse.quote('你好')
'%E4%BD%A0%E5%A5%BD'
```

不能用`str.encode()`，原因同上。另外，同样根据[这里](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431664106267f12e9bef7ee14cf6a8776a479bdec9b9000)

>在bytes中，无法显示为ASCII字符的字节，用 \x## 显示。

<br/>

##	4. 结论

如果使用`str.encode()`，把不同结果组合成url用于获取资源

```python
url = 'http://dict.youdao.com/fsearch?q=' + word.encode('utf-8')
```

除了`py2+英文`的组合无影响，其他三者都不是期望结果。

因此:

*	如果使用`str.encode()`，py2英文无影响，中文有影响。py3中英文都有影响。
*	应使用`percent-encoding`。
*	由于是作为vim插件，环境是`py2+英文`(一般不会出现中文)，所以无影响。此条Issue仅作为建议。

<br/>

综上可以看出`py3`对数据类型和编码的控制更为精细与明确。所以在使用`urlopen()`时，如果url中包含了未经编码的中文，是无法正确获取资源的。

另外，我们可以在浏览器地址栏直接输入中文进行搜索，这是因为浏览器帮我们完成了编码和转换的过程。比如你先输入中文进行搜索，然后再把url复制下来，会发现中文变成了很多`%`和英文组成的字符串。

最后，因为原来的`有道api`不知道啥情况不能用了，感谢[vim-youdao-translater](https://github.com/ianva/vim-youdao-translater)项目提供了一种获取资源的新思路，我在[GitHub](https://github.com/jJayyyyyyy/cs/tree/master/just%20for%20fun/youdao_translation)上传了不需要key和keyfrom的新版本查词器，代码也精简了不少。

<br/>

(END)

---

##	相关阅读

*	[这篇文章](https://jjayyyyyyy.github.io/2016/10/07/python3%E9%87%8D%E5%86%99SimpleHTTPServerWithUpload.html#section-6)的**其他内容**一节

*	[Stack Overflow: Equivalent urllib.parse.quote() in python 2.7](http://stackoverflow.com/questions/35149861/equivalent-urllib-parse-quote-in-python-2-7)

*	[字符串和编码](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431664106267f12e9bef7ee14cf6a8776a479bdec9b9000)