---
layout: post
title: HTTP与缓存相关的头部信息
---


#	1. HTTP headers简介

HTTP是一个应用层协议，由请求和响应构成。和其他很多数据格式一样，请求和响应由**头部**和**数据**组成，

<br/>

| 常见请求头 | 意义 |
| --- | --- |
| Accept: text/html; image/webp; */*	浏览器可接收的类型 | Accept-Encoding: gzip,compress 	浏览器可接收的压缩编码类型 |
| If-Modified-Since: Mon, 1 Aug 2016 18:23:51 GMT | 页面缓存时间 |
| Cache-Control：max-age=300 | 缓存的最长时间 300s |

<br/>

| 常见响应头 | 意义 |
| --- | --- |
| Content-Encoding: gzip | 服务器发送的压缩编码方式 |
| Content-Length: 80 | 服务器发送显示的字节码长度 |
| Content-Type: text/html; charset=UTF-8 | 服务器发送内容的类型和编码类型 |
| Last-Modified: Tue, 11 Jul 2000 18:23:51GMT | 服务器最后一次修改的时间 |
| Date:Mon, 01 Aug 2016 13:38:43 GMT | 资源起始时间 |
| Expires:Mon, 01 Aug 2016 13:38:53 GMT | 资源过期时间 |
| ETag:W/"578303987844e7989822712c1e153fc9" | 资源实体的标识(唯一标识，类似md5值，文件有修改md5就不一样) |
| Cache-Control: max-age=10 | 缓存时间 |

<br/>

##	2. 与缓存相关的头部信息

在请求静态文件的时候，由于这些文件不经常变化，因此把静态文件储起来是一种优化用户浏览体验的方法，同时也可以释放链路资源，缓解网络压力。

那么，应当如何确定缓存文件的有效时间呢？对此，我们需要用到HTTP头中的Expires和Cache-Control。

**2.1.1 Expires**

当客户端第一次访问一个文件资源的时候，服务端在返回资源内容的同时也返回了：

```
Expires: Mon, 1 Aug 2016 22:43:02 GMT
```

也就是服务端告诉浏览器，先把这个文件缓存起来，在这个过期时间之前，该文件都不会变化了。

下一次浏览器又要访问这个资源，并且访问的时间在`Mon, 1 Aug 2016 22:43:02 GMT`之前，那浏览器就不去服务器那边获取文件了，而是直接从缓存中取文件。

**2.1.2 Cache-Control**

由于Expires给定的是绝对时间，而客户端的系统时间可以由用户任意修改，比如Expires设定的过期时间是

```
Mon, 1 Aug 2016 22:43:02 GMT
```

现在用户把系统时间改为

```
Tue, 2 Aug 2016 22:43:02 GMT
```

则缓存会被判为过期（虽然实际上还没到那个时间）。因此在HTTP1.1中引入了Cache-Control，这就是一个相对时间，比如

```
Cache-Control: max-age=80
```

那就是说这份缓存的有效期是80秒，而没有给定过期的绝对时间。

由于Cache-Control是HTTP1.1中才有的，因此可能会有Expires和Cache-Control同时出现的情况，这时以Cache-Control为准。

**2.2 Last-Modified / If-Modified-Since**

现在有另外一个问题，服务端有个文件可能会更新，因此希望客户端时不时过来问一下这个文件是否过期。

如果没有过期，服务端不返回数据给浏览器，只返回304状态码，告诉浏览器目前的缓存还没有过期，然后浏览器继续使用已有缓存。

这个就叫做条件请求。这里就要用到以下两个头部信息

*	Last-Modified	(response header)
*	If-Modified-Since	(request header)

以上一小节的响应头和请求头为例，浏览器第一次请求资源的时候，服务端返回资源内容，同时也返回了

```
Last-Modified:Mon, 01 Aug 2016 13:48:44 GMT
```

也就是服务端在告诉客户端这个文件在服务器上的最后修改时间。

浏览器第二次访问的时候（假设这里没有设置Expires或者Cache-Control）。那么浏览器在访问资源的时候会在请求头上带上

```
If-Modified-Since:Mon, 01 Aug 2016 13:48:44 GMT
```

服务端收到后对比目前文件的最后修改时间和该请求头的信息，如果没有修改，那就直接返回304给浏览器，而不返回实际资源。如果有变化了，就返回200，并且带上新的资源内容。

**2.3 Etag / If-None-Match**

条件请求还有另外一种方法——打标签，也就是使用Etag。

道理和上一小节类似：

*	第一次拿到资源的时候，服务器的响应头中包含了Etag，用来作为时间标签
*	下一次浏览器再次请求资源的时候会把原来的Etag标签带上（在请求头中变成了If-None-Match）作为校验标准，
*	若这个文件如果发生了改变，则Etag也会改变。
*	服务器对比浏览器请求头中的的If-None-Match：
	*	如果相同就返回304，而不返回实际资源
	*	如果不同，就返回200和新的资源。

由于Etag需要通过服务器计算得出，每次都进行计算需要额外的开销，有时候这也是一种负担。

<br/>

##	3. 实例

下面以第2篇参考资料为例。在chrome中打开开发者工具的Network，然后进入[http://www.cnblogs.com/yjf512/p/3244882.html](http://www.cnblogs.com/yjf512/p/3244882.html)，选择名为`3244882.html`的文件，可以看到其Response Headers中有如下内容：

```
Cache-Control:private, max-age=10
Connection:keep-alive
Content-Encoding:gzip
Content-Type:text/html; charset=utf-8
Date:Mon, 01 Aug 2016 13:48:44 GMT
Expires:Mon, 01 Aug 2016 13:48:54 GMT
Last-Modified:Mon, 01 Aug 2016 13:48:44 GMT
Transfer-Encoding:chunked
Vary:Accept-Encoding
X-UA-Compatible:IE=10
```

第1行的`Cache-Control:private, max-age=10`，表示有效时间为10s，且其优先级高于`Expires`。响应头中出现了`private`，其作用是通知浏览器只针对单个用户进行缓存，且可以具体指定某个字段，如`private–"username"`。


第2行的`Keep-Alive`功能使客户端到服务器端的连接持续有效。当出现对服务器的后继请求时，`Keep-Alive`功能避免了重新建立连接，即认为之前的连接还是有效的。

第3行表示响应的压缩方式。压缩后再传输可以提高效率。

第4行表示响应的文件类型和字符编码方式。

第5行的`Date`表示生成文件的绝对时间。

第6行`Expires`表示文件过期的绝对时间。同时上面也提到了，其优先级低于`Cache-Control`。

第7行的`Last-Modified`是服务器告诉浏览器该文件的最后修改时间。

综上可以看出，该页面的缓存有效时间是10秒。如果不清空缓存，在2016.08.01的13:48:44~13:48:54这个时间段中再次访问服务器，则不会再得到新的页面，而是直接呈现本地缓存。

<br/>

##	4.参考文献

*	[HTTP缓存相关的概念](http://blog.csdn.net/zhsh87/article/details/8186753)
*	[HTTP缓存相关头](http://www.cnblogs.com/yjf512/p/3244882.html)

<br/>

本文地址：[https://jjayyyyyyy.github.io/2017/05/01/HTTP_headers.html](https://jjayyyyyyy.github.io/2017/05/01/HTTP_headers.html)

<h6>注：本文最初整理于20160801。接下来将会陆续整理一些以前的学习笔记。</h6>

(END)

---

##	相关阅读

*	[HTTP权威指南](https://book.douban.com/subject/10746113/)