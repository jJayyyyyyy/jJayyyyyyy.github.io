---
layout: post
title: Python课程基础测试
---

<br/>

##	说明

*	在课程开始之前，请先完成一些Python的基础测试题，以便我们更好地安排小朋友的课程计划。课程使用的是`Python3.6`，所以请升级Python到该版本，下载地址请点击 **[这里](https://www.python.org/downloads/release/python-361/)**。

*	对于每一个题目，首先请**仔细阅读代码**，然后在纸上**写下逻辑和过程**，以及你所**预期的结果**。最后，请**手动输入代码(不是复制粘贴)**并运行该程序，然后检查你预期的答案。

*	注意，测试题中可能存在一些**陷阱和错误**。请在阅读代码时记录并改正错误，或者在运行代码发生错误时进行debug，并做好相关记录。这部分内容不多，但可能存在一定的难度，所以需要鼓励小朋友勇敢尝试，不用担心做不出来。

*	请认真完成这些题目，并**记录**答题情况和答题时间。

##	知识点

*	判断
*	循环
*	字符串
*	列表
*	函数
*	模块

##	题目

*	Test00

	```python
	# 请输入自己的信息，然后查看运行结果。
	# Please input your info and check out the output.

	name = input('Please input your name: ')
	gender = input('Please input your gender(boy or girl): ')
	gender = gender.capitalize()

	if gender == 'boy':
		print('\n\n[%s] is a brave boy!' % name)
	elif gender == 'girl':
		print('\n\n[%s] is a pretty girl!' % name)
	else:
		print('Welcome!')
	```

<br/><br/>

*	Test01

	```python
	# 请写出运行结果。
	# Please write down the output of this program.

	num = 0
	top = 10
	for i in range(top):
		num = num + i
	print(num)
	```

<br/><br/>

*	Test02

	```python
	# 请写出运行结果。
	# Please write down the output of this program.

	weight = input('Please input your weight(kg): ')

	length = len(weight)
	print('Your weight is a %d digit number.\n\n' % length)

	if weight > 200:
		print('You\'d better do some exercises everyday!')
	```

<br/><br/>

*	Test03

	```python
	# 1. 请写出运行结果。
	#    Please write down the output of this program.
	
	# 2. 如何得到所有人的名字(去掉数字)?
	#    How to output only the names and skip all the numbers?
	
	# 3. 如何按照字典序输出所有姓名?
	#    And then, how to output this list of names in alphabet order?

	friends = [1, 'Ann', 'Chris', 'Dobby', 'Bob', 'Emma', 100]
	friends.append('Frederick')
	# friends.pop()

	print(friends[:])
	```

<br/><br/>

*	Test04

	```python
	# 请写出运行结果。
	# Please write down the output of this program.

	def get_cakes(num=1):
	    cakes = []
	    for i in range(num):
	        cakes.append('cake')
	    return cakes

	cakes = get_cakes(3)
	print(cakes)
	```

<br/><br/>

*	Test05(`time`模块的相关知识可以查看[Python Docs: time.localtime()](https://docs.python.org/3/library/time.html?highlight=time#time.localtime))

	```python
	# 请写出运行结果。
	# Please write down the output of this program.

	import time
	print(time.ctime())
	print(time.localtime()[0:3])
	```

<br/><br/>

<br/>

本文地址：[https://jjayyyyyyy.github.io/2017/06/20/pre_tests_for_python.html](https://jjayyyyyyy.github.io/2017/06/20/pre_tests_for_python.html)

(END)

---

##	相关阅读

*	[Python Docs](https://docs.python.org/3/library/functions.html)
*	零基础班上课的课件