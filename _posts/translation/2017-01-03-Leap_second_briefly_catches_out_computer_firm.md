---
layout: translation
title: 翻译：Leap second briefly catches out computer firm
---


这是**翻译**系列的一篇文章。原文来自BBC的新闻[Leap second briefly catches out computer firm](http://www.bbc.com/news/technology-38488246)，发布于2017年1月2日。以下是译文。

<br/>

##	由闰秒带来的故障

<img alt="leap second" src="http://ichef.bbci.co.uk/news/660/cpsprodpb/1025A/production/_93183166_hi006664199.jpg">

<h6>“闰秒”造成了Cloudflare公司的服务器出现故障。</h6>

互联网公司Cloudflare提供的服务出现短暂故障，原来是闰秒惹的祸。

Cloudflare公司的少数服务器未能正确处理2016年的闰秒，导致故障出现，并使其部分客户在2017年最初的几个小时内无法接入互联网。

由于[UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)时间制和地球绕日公转周期之间存在误差，因此偶尔需要引入[闰秒](https://en.wikipedia.org/wiki/Leap_second)进行调整，并以此与使用GMT时间制的国家进行时间同步。

#### 丢失的时间

Cloudflare公司发表[声明](https://blog.cloudflare.com/how-and-why-the-leap-second-affected-cloudflare-dns/)，称其工程师已经在90分钟内修复了出现故障的机器。

如果当时用户想要访问受影响的服务器，可能会收到该页无法显示等错误。

Cloudflare公司提供CDN服务，可以加快用户访问网站的速度，也能防止恶意流量对源站的直接攻击。

据悉，在故障期间约1%的用户访问请求受到影响。

经过深入分析，不同网络之间的时间戳在闰秒前后存在差异，造成了内部系统的"恐慌"，最终引发了服务器故障。

<br/>

<h5>注</h5>

<ol><span>

<li><span><h5>
leap, 跳, 闰年的. leap second, 闰秒. 2016年的12月31日增加了一闰秒, 也就是说12月31日11点59分59秒之后不是2017年, 而是59分60秒.
</h5></span></li>

<li><span><h5>
GMT可以理解为以太阳为标准, 太阳直射格林尼治子午线时为正午, 两个正午可以确定一天; UTC可以理解为原子钟, 如1s定义可以被为铯原子最外层电子跃迁N次所经过的时间.
</h5></span></li>

<li><span><h5>
CDN, Content Delivery Network, 内容分发网络, 可以理解为分布式缓存, 这样用户可以就近获取网页内容, 而不需要每次都去访问源站. 这种做法加快了用户的访问速度并减小了源站压力. 提供CDN服务的公司, 国外有Cloudflare, Akamai等, 国内有网宿, 蓝汛, 七牛, 阿里, 腾讯等.
</h5></span></li>

</span></ol>

<br/>

(END)

---

##  相关阅读

*	[闰秒, Leap Second](https://zh.wikipedia.org/wiki/%E9%97%B0%E7%A7%92)
*	[格林尼治标准时间, GMT](https://zh.wikipedia.org/wiki/%E6%A0%BC%E6%9E%97%E5%B0%BC%E6%B2%BB%E5%B9%B3%E6%97%B6)
*	[协调世界时, UTC](https://zh.wikipedia.org/wiki/%E5%8D%8F%E8%B0%83%E4%B8%96%E7%95%8C%E6%97%B6)
*	[How Earth Moves](https://www.youtube.com/watch?v=IJhgZBn-LHg)
*	[文章1: Google公共NTP服务器将通过“降速”增加闰秒](http://www.solidot.org/story?sid=50596)  
	今年的12月31日将增加一闰秒，也就是说12月31日11点59分59秒之后不是2017年，而是59分60秒。服务器通常无法处理有61秒的一分钟，因此闰秒曾经导致网站的服务器发生问题。Google云平台官方博客介绍了它的NTP时间服务器如何处理闰秒的方法：不是额外增加一秒，而是闰秒引入前后的10小时内让时钟速度减慢0.0014%，此种做法将在二十小时内使得时钟增加1秒。12月31日将和其他日子没有差别。Google的所有服务包括API将使用上面描述的方法同步闰秒。
*	[文章2: 闰秒导致部分Linux服务器高CPU使用率](http://www.solidot.org/story?sid=30309)  
	国际地球自转和参考坐标系统服务（IERS）在2012年6月30日午夜(北京时间7月1号7点59分59秒)增加一闰秒（即出现7：59：60）。由于Linux kernel 2.6.29之前版本存在bug，在进行闰秒调整时可能会引起系统时钟服务ntpd进程死锁。Debian Lenny、RHEL/CentOS 5等旧发行版今天仍被广泛使用，部分供应商早已经发布了补丁。但除了Linux服务器外，一些服务器程序也因为闰秒出现了问题，如Reddit、Mozilla、FourSquare、Yelp、LinkedIn和Gawker等网站都短暂遭遇了技术问题，国内的一家云储存供应商发现运行在CentOS 6.2上的Java和MySQL因闰秒出现了不同程度的CPU利用率增长，猜测是JVM和MySQL试图通过CPU硬件晶振的数据获得当前精确的时间，由于闰秒的关系，这个时间和操作系统维持的墙上时间（Wall Time，也就是显示给用户看的时间）不一致，导致了这个问题。简单的修正方法是强制重置系统时间，让系统中所有时间回到同步的状态。

