---
layout: post
title: "GET POST 区别"
---


<br>

##	GET POST 区别

*	一般来说，GET用于从服务器读取资源，POST 用于向服务器上传数据，比如上传表单

*	GET 一般使用 URL 向服务器传递消息，URL 长度一般会受到 浏览器、中间结点、服务器等的限制，POST 则可以通过 body 来传递数据

*	中间这些结点一般都会记录 URL 日志，但 body 一般很少记录

*	通过 GET 获取的资源可以做缓存，因为多次请求的结果是一样的，post 则不幂等，不能随意多次执行，不能缓存，刷新浏览器时会被告知是否要重复提交订单

*	但是，只要 Client 和 Server 达成共识，server 也可以接受 client 的 GET 请求携带 body

*	AJAX 或者 restful 接口在实现的时候，都可以定义使用 post 来做 get 的事情，get 也可以携带 json body（但在 restful 中一般不会在 get 中携带 body），只要 CS 双方约定好即可

*	安全性，刚才提到 post 比 get 稍微安全一点，但是其实可以忽略，只要使用明文的 http，都不安全

	<br>

##	参考资料

*	[GET 和 POST 到底有什么区别？](https://www.zhihu.com/question/28586791/answer/767316172)

<br><br>

本文地址：[https://jjayyyyyyy.github.io/2019/09/08/difference_between_get_and_post_method.html](https://jjayyyyyyy.github.io/2019/09/08/difference_between_get_and_post_method.html)

(END)

---

##	相关阅读
