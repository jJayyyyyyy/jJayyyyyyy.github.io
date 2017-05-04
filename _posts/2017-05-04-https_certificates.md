---
layout: post
title: HTTPS证书，类型及特点
---


#	1. 简介

HTTPS即Hypertext Transfer Protocol Secure。由于其安全层使用的是TLS/SSL，因此HTTPS也可以称为HTTP over TLS或HTTP over SSL。

“HTTPS证书”又叫“SSL证书”、“SSL安全证书”、“SSL数字证书”，目前应用广泛，发展迅速。

SSL证书需要向国际公认的证书证书认证机构（简称CA，Certificate Authority）申请。

<br/>

#	2. HTTPS证书分类

**2.1 按验证等级**

*	域名型SSL证书，DVSSL，Domain Validation SSL Certificate

	信任等级普通，只需验证网站的真实性便可颁发证书保护网站。无需人工审核，快速颁发。
	
*	企业组织型SSL证书，OVSSL，Organization Validation SSL Certificate

	信任等级强，须要验证企业的身份，审核严格，安全性更高。需要人工审核，证书包含企业认证信息。
	
*	增强扩展型SSL证书，EVSSL，Extended Validation SSL Certificate

	信任等级最高，一般用于银行证券等金融机构，人工审核，审核严格，颁发之后可以使网站域名在浏览器地址栏变成绿色。

<br/>

| DVSSL | OVSSL | EVSSL |
| --- | --- | --- |
| 信任等级普通，只需验证网站的真实性便可颁发证书保护网站 | 信任等级强，须要验证企业的身份，审核严格，安全性更高 | 信任等级最高，一般用于银行证券等金融机构 |
|  |  |
| 无需人工审核，快速颁发 | 需要人工审核，证书包含企业认证信息 | 人工严格审核，颁发之后可以使网站域名在浏览器地址栏变成绿色 |

<br/>

**2.2 按保护域名数量**

*	单域名型证书, Single Domain SSL Certificates

	一张SSL证书保护一个域名，一个网站的安全。

*	多域名型证书 	Multi Domain SSL Certificates

	一张SSL证书可以保护多个域名，多个网站的安全，多域型SSL证书。支持任何域名，可以是不同的顶级域名（如：hello.com, tv.com, example.com）。
	
*	通配符型证书, Wildcard SSL Certificates

	一张SSL证书可以保护相同主域名下所有的子域名的安全。通配型证书只支持*.domain.com通配符子域名（如：a.hello.com, b.hello.com、c.hello.com）。

其中，单域名型证书和多域名证书分为三种验证等级：DV, OV, EV。
而通配符型证书只有DV和OV两种。

<br/>

| 单域名型证书 | 多域名型证书 | 通配符型证书 |
| --- | --- | --- |
| 一张SSL证书保护一个域名，一个网站的安全 | 一张SSL证书可以保护多个域名，多个网站的安全，多域型SSL证书。支持任何域名，可以是不同的顶级域名 | 一张SSL证书可以保护相同主域名下所有的子域名的安全 |
|  |  |
| 仅支持1个域名 | 最多支持50个域名 | 不限制子域名数量 |
|  |  |
| 可选DV, OV, EV三种等级 | 可选DV, OV, EV三种等级 | 可选DV, OV两种等级 |

<br/>

##	3.参考文献

*	[baike](http://baike.baidu.com/view/5262561.htm)
*	[zhihu]( https://www.zhihu.com/question/19578422/answer/114210307)
*	[SSLzhengshu-SD_MD_Wildcard](https://www.sslzhengshu.com/article/post-36.html)
*	[SSLzhengshu-DV_OV_EV](https://www.sslzhengshu.com/article/post-35.html)
*	[EVTrust](http://www.evtrust.com/ssl.html)

<br/>

本文地址：[https://jjayyyyyyy.github.io/2017/05/04/https_certificates.html](https://jjayyyyyyy.github.io/2017/05/04/https_certificates.html)

<h6>注：本文最初整理于20161018。接下来将会陆续整理一些以前的学习笔记。</h6>

(END)

---

##	相关阅读
