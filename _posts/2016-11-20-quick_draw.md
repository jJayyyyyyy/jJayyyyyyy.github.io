---
layout: post
title: QUICK, DRAW!
---


前几天看新闻，得知Google开发了这个叫做[QUICK, DRAW!](https://quickdraw.withgoogle.com/)的游戏(其实是作者和在Google工作的朋友开发的\)。游戏名直译过来是*快画*，也就是在规定时间内完成涂鸦。

玩法很简单: 

*	你会得到六个单词；

*	对于每个词，你可以在20秒内用画画的方式进行描述；

*	机器会对你的涂鸦进行识别，并判断与题目单词是否符合。

这个游戏其实是一个基于人工智能的项目，可以看到游戏主页中有如下文字说明:

>Can a neural network learn to recognize doodles?
See how well it does with your drawings and help teach it, just by playing.

大意就是说: 试试让神经网络来识别涂鸦吧~玩一玩，画一画，对机器进行识别训练，看看人工智能的表现如何~

在`About this game`中还有更为详细的描述:

>This is a game built with machine learning. You draw, and a neural network tries to guess what you’re drawing. Of course, it doesn’t always work. But the more you play with it, the more it will learn. So far we have trained it on a few hundred concepts, and we hope to add more over time. We made this as an example of how you can use machine learning in fun ways. Watch the video below to learn about how it works, and [check out more A.I. Experiments here.](https://aiexperiments.withgoogle.com/)

也就是说，这是一个基于机器学习的游戏。你画一些东西，然后神经网络会试着分析你画了啥。当然它现在还比较呆萌，不过你玩儿得越多，它学得也越多。现在机器已经学习了几百个词(及其对应的画风233)，今后我们还将教它认识更多。玩了这个游戏后你会发现，利用机器学习可以做很多有趣的事。下面的视频介绍了这个游戏的工作原理。更多信息还可以参考他们的[A.I. Experiments](https://aiexperiments.withgoogle.com/)主页。

<iframe width="560" height="315" src="https://www.youtube.com/embed/X8v1GWzZYJ4" frameborder="0" allowfullscreen></iframe>

(视频中提到，游戏用到了和 **Google翻译——手写输入识别**
相同的技术。)

另外，这个[A.I. Experiments](https://aiexperiments.withgoogle.com/)主页上展示了许多有意思的项目，每一个都配有说明视频，大部分还公开了代码，放在[GitHub-Google Creative Lab](https://github.com/googlecreativelab)上。虽然目前还木有这个游戏的代码(估计还在实验阶段，也可能是因为涉及到关键技术，有的项目是利用了Google的API，而不是展现核心算法)，但是项目本身还是很有意思的。说不定玩着玩着，你就称为了一个灵魂画手:)
