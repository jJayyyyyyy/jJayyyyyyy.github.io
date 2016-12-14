---
layout: post
title: FFMPEG学习笔记
---


##	音频

*	剪切音乐

	比如有一首好听的歌，我们想把它作为铃声。但是整首歌太长，要截取最好听的那一段，就可以借助`ffmpeg`。根据[参考资料1](https://segmentfault.com/a/1190000000414341)，如果是作用在视频上，需要的参数是截取部分的开始时间`start_time`和结束时间`stop_time`，单位是秒。【注意，[参考资料1](https://segmentfault.com/a/1190000000414341)中原文的`剪切`一节，可能会由楼主所用起止时间变量名引起误解，具体说明可见链接中` @_ljj110719`的回复】
	
	```
	$ ffmpeg -i input.mp4 -ss **START_TIME** -to **STOP_TIME** -acodec copy -vcodec copy output.mp4
	```
	
	其中，参数`-to`表示定位到某个时间点，如果换成`-t`则表示持续时间。
	
	实践发现，以上方法同样可以作用在音频上。假设我们要截取的音乐是`I_do.mp3`，从`00:00:25`到`00:01:25`的片段，也就是从`25`秒到`85`秒。由于现在我们只需要音频(audio)，因此可以不要后面的`-vcodec copy`。所以我们的目标可以用如下命令实现:

	```
	$ ffmpeg -i I_do.mp3 -ss 25 -to 85 -acodec copy I_do__snippet.mp3
	```

##	视频

*	分离音频和图像

	参考用途: 去掉美剧中的视频，仅保留音频，用于听力练习。方便携带，同时减小体积。假设要处理的视频是`input_video.mp4`，输出音频保存为`output_audio.mp3`，则: 
	
	```
	$ ffmpeg -i input_video.mp4 -vn -ab 256 output_audio.mp3
	```
	
<br/>
	
(不断更新)

<br/>

##	参考资料

1.	[通过 ffmpeg 无损剪切/拼接视频](https://segmentfault.com/a/1190000000414341)

2.	[Useful FFmpeg Commands](http://www.labnol.org/internet/useful-ffmpeg-commands/28490/)