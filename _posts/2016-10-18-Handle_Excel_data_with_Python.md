---
layout: post
title: 使用Python处理Excel数据
---


##	1. 简介

这篇文章将会介绍[xlrd](https://github.com/python-excel/xlrd)和[xlwt](https://github.com/python-excel/xlwt)，并利用它们进行Excel数据的读取和写入，以便实现复杂的但有规律的Excel数据处理逻辑。本文代码和示例托管于[GitHub](https://github.com/jJayyyyyyy/python-excel__demo)，可供参考。

[xlrd](https://github.com/python-excel/xlrd)和[xlwt](https://github.com/python-excel/xlwt)是两个基于python的库(Package)，它们能够处理`.xls`文件，前者可以读取Excel表格的数据，后者可以新建表格写入数据。

本文参考了[python-excel tutorial](https://github.com/python-excel/tutorial)，并结合实践应用整理而成。内容仅针对数据处理，不包括格式的设置。更多内容可参考[python-excel.org](http://www.python-excel.org/)及其[GitHub](https://github.com/python-excel)项目页。在此感谢[cjw296](https://github.com/cjw296)和[sjmachin](https://github.com/sjmachin)两位同学的成果~

ps: 其文档中存在部分小错误，整体理解即可。

<br/>

##	2. 为什么要用这两个package

1.	不知道你有没有这种经历：自己对于Excel的掌握一般，只会用菜单栏的基本功能对表格数据进行处理。但是如果更复杂的处理逻辑就非常蛋疼了，用基本功能很难描述，怎么问度娘都得不到合适的公式，对于VBA也不甚了解（反正我是完全不懂啊=.=）。但是需要处理的数据有很多，一次次地重复劳动简直是一种折磨啊。。

	这时候就该`python-excel`出场了~我们要做的，就是把重复的但是有规律的数据处理动作，抽象成为固定的逻辑顺序，然后用python描述出来，这样即可一劳永逸。以后再遇到这个问题你就可以去泡一杯喝咖啡静静等待电脑帮你完成任务，而不用半天甚至整天都盯着屏幕一行一行地手动处理了。

2.	python方便灵活容易上手，不需要编译。开发省时，性能损失不大。
	
3.	这两个库的接口还行，其他的库没试过，可能有更好的。其次是有方便的接口可以读写某行某列，可以快速定位到某张表，某个单元格，etc...不需要自己重复造轮子。

<br/>

##	3. 如何食用

1.	第一步是安装`python2.7`或`python3.4`，本文代码基于后者。

	利用[pip](https://pypi.python.org/pypi/pip)安装`xlrd`和`xlwt`
	
	```bash
	pip3 install xlrd xlwt
	```
	
	然后假设有如下这么一个表格`demo_read.xls`

	| Name | Age |
	:--- |:---:|
	Emily | 10 |
	Bob | 11 |
	Xiaoming | 9 |
	Monika | 7 |
	Steve | 8 |
	
	<br/>

2.	读取数据

	首先引入模块

	```python
	from xlrd import open_workbook
	```

	其次实例化一个`open_workbook`对象，从`demo_read.xls`读取文件信息

	```python
	wb = open_workbook('demo_read.xls')
	```

	读取第一张表，得到表单名字，总行数和总列数

	```python
	sheet_0 = wb.sheets()[0]
	total_rows = sheet_0.nrows
	total_cols = sheet_0.ncols
	```

	得到某个单元格的值，第2行第3列单元格坐标为(1, 2)

	```python
	cell_value = sheet_0.cell(1, 2).value
	```

3.	写入数据

	首先引入模块

	```python
	from xlwt import Workbook
	```

	其次实例化一个`Workbook`对象，添加新表单

	```python
	book = Workbook()
	sheet_new = book.add_sheet('Sheet_new')
	```

	结合3.2节，如果我们要复制一个表单，可以这么做

	```python
	for in range(sheet_0.nrows):
		for col in range(sheet_0.ncols):
			value = sheet_0.cell(row, col).value
			# write into the new sheet
			sheet_new.write(row, col, value)
	```

	最后保存

	```python
	book.save('demo_write.xls')
	```

4.	需要说明的是，读和写是两种不同的对象，只能通过中间变量联系，也就是3.3节中的`value`. 另外，如果是在windows下开发，有时候会出现莫名其妙的编码问题（cmd打印的时候）。

5.	如果想让电脑更懂你，就需要在你的代码中结合条件判断，循环语句，字符串的`len()`, `strip()`, `lstrip()`, `rstrip()`, `split()`, `replace()`等方法，如果掌握了`re`神器就更好了~

下面一章，我们就用实例来说明如何让电脑更懂你的需求。

<br/>

##	4. 实例

原来是结合工作任务写的一些脚本，由于涉及保密数据，所以需要处理一下。过两天更新。

<br/>

##	5. 参考

1.	[python-excel tutorial](https://github.com/python-excel/tutorial)

2.	[liaoxuefeng](http://www.liaoxuefeng.com)

<br/>

---

写于20161018

20161023第一次完善和上传

20161024第二次更新：增加表格，增加参考文献



