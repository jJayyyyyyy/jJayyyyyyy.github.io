---
layout: post
title: C语言中的注释 /* */ 会被替换成什么？
---

<br/>


[问题链接](http://www.icourse163.org/learn/ZJU-199001?tid=1002303013#/learn/content?type=detail&id=1003068782&cid=1003618738)

	在把代码翻译成汇编代码前，还有一个步骤叫预处理。在预处理当中会把注释代码干掉。
	可以使用以下命令来进行预处理：
	gcc -E hello.c -o hello.i
	hello.i  里面就是预处理后的代码。

	经过实验发现有3种处理方式，大约是：
	一条语句前的注释 => 等长的空格；
	一条语句中的注释 => 一个空格；
	一条语句后的注释 => 直接删除。


<br/><br/>

本文地址：[https://jjayyyyyyy.github.io/2017/10/04/what_will_annotation_be_replaced_by.html](https://jjayyyyyyy.github.io/2017/10/04/what_will_annotation_be_replaced_by.html)


(END)

---

##	相关阅读
