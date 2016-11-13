---
layout: post
title: FFMPEG学习笔记
---


##	音频

*	剪切音乐

	比如有一首好听的歌，我们想把它作为铃声。但是整首歌太长，要截取最好听的那一段，就可以借助`ffmpeg`。根据[参考资料1](https://segmentfault.com/a/1190000000414341)，如果是作用在视频上，需要的参数是截取部分的开始时间`start_time`和结束时间`stop_time`，单位是秒。【注意，[参考资料1](https://segmentfault.com/a/1190000000414341)中原文的`剪切`一节，可能会由楼主所用变量名引起误解，具体说明可见下面` @_ljj110719`的回复】
	
	```
	$ ffmpeg -i input.mp4 -ss **START_TIME** -to **STOP_TIME** -acodec copy -vcodec copy output.mp4
	```
	
	其中参数`-to`表示定位到某个时间点，如果换成`-t`则表示持续时间。实践发现，以上方法同样可以作用在音频上，比如把输入输出换成`.mp3`，这时候只需要audio就行了，因此可以不要后面的`-vcodec copy`。假设我们要截取的音乐是`I_do.mp3`，从`00:00:25`到`00:01:25`的片段，也就是从`25`秒到`85`秒，那么可以用如下命令:

	```
	$ ffmpeg -i I_do.mp3 -ss 25 -to 85 -acodec copy -vcodec copy output.mp3
	```

##	视频

##	参考

1.	[通过 ffmpeg 无损剪切/拼接视频](https://segmentfault.com/a/1190000000414341)
