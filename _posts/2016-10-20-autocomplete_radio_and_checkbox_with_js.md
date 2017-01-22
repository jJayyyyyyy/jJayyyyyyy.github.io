---
layout: post
title: 利用js自动填充单选和多选框
---


##	前言

申请某线上机器权限之前要考试。测试后发现该考试题目和选项固定，又因为题目数量很多，正式帐号重做比较麻烦，于是想用脚本自动填充。这篇文章仅作大致记录，可以进行相关扩展。利用python获取答案以及其他细节从略。

另外，浏览器使用Chrome.

<br/><br/>

##	单选框

<div class="media-body well">
	<label class="radio inline"><input type="radio" name="question[1]" rel="1" value="A" autocomplete="off">A </label>
	<label class="radio inline"><input type="radio" name="question[1]" rel="1" value="B" autocomplete="off">B </label>
	<label class="radio inline"><input type="radio" name="question[1]" rel="1" value="C" autocomplete="off">C </label>
	<label class="radio inline"><input type="radio" name="question[1]" rel="1" value="D" autocomplete="off">D </label>
</div>

<br/>

上面是一个四个单选框。以A的选择框为例，右键---审查元素，可以看到关键部分：

```html
<input type="radio" name="question[1]" rel="1" value="A" autocomplete="off">
```

接下来利用js，在开发者工具中选择Console，逐行输入以下代码，即可依次选中单选题的ABCD：

```javascript
var single = document.getElementsByName("question[1]");
single[0].checked = 1;
sinlge[1].click();
sinlge[2].click();
single[3].checked = 1;
```

注：Name是唯一的。

<br/><br/>

##	多选框

<div class="media-body well">
	<label class="checkbox inline"><input type="checkbox" name="question[2][0]" rel="2" value="A" autocomplete="off">A </label>
	<label class="checkbox inline"><input type="checkbox" name="question[2][1]" rel="2" value="B" autocomplete="off">B </label>
	<label class="checkbox inline"><input type="checkbox" name="question[2][2]" rel="2" value="C" autocomplete="off">C </label>
	<label class="checkbox inline"><input type="checkbox" name="question[2][3]" rel="2" value="D" autocomplete="off">D </label>
	<label class="checkbox inline"><input type="checkbox" name="question[2][4]" rel="2" value="E" autocomplete="off">E </label>
</div>

<br/>

上面是五个多选框。首先也是审查元素，关键代码如下：

```html
<input type="checkbox" name="question[2][0]" rel="2" value="A" autocomplete="off">
```

在上一节我们看到，单选题中可以利用`.click()`或者`.checked = 1`两种方式来勾选，效果似乎相同。然而在多选框中，这两种方式就有区别了。从名字来看，前者是模拟鼠标点击，后者是直接编辑状态。在多选框中，如果先勾选一个框，接着再点一下鼠标（相当于两个`.click()`），就是取消勾选。如果网页没有缓存，可以使用如下方法:

```javascript
document.getElementsByName("question[2][0]")[0].click();
document.getElementsByName("question[2][1]")[0].checked = 1;
document.getElementsByName("question[2][2]")[0].checked = 1;
document.getElementsByName("question[2][3]")[0].checked = 1;
document.getElementsByName("question[2][4]")[0].click();
```

如果缓存了，可能会出现有的框已经被选中的情况。这时如果用`.click()`就会取消选中，而用`.checked = 1`则不受影响。所以需要按照实际情况使用。

有了以上基础，就能扩展出很多自动化脚本啦~

<br/>

(END)
