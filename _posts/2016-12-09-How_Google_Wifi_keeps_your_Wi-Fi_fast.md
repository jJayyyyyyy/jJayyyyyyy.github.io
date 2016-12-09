---
layout: post
title: How Google Wifi keeps your Wi-Fi fast
---


<iframe width="560" height="315" src="https://www.youtube.com/embed/ty0SGNZi81U" frameborder="0" allowfullscreen></iframe>

这是Youtube上的一个视频，所述内容如下:

*	The router in your home sends Wi-Fi through the air on a channel like a radio. But other routers nearby might be on the same channel. Let's say those neighbor start streaming, gaming and downloading, all at the same time. Even if you have a fast Internet subscription, you may still be waiting for what you want. Come on... OK... 

*	So there are three main channels most routers can use. Generally they pick the least congested one and stay there. But over time, that channel can get crowded. So we designed Google Wifi to work around that. We built in congestion sensing technology that checks for bottlenecks by scanning each channel every five minutes. Then when the time is right, Google Wifi quietly switches to the fastest, least congested channel making sure you get the best possible Wifi.

大意是对标题进行解释，为啥用Google Wifi能够让你的无线网络更快。

下面是一些草稿，现在书不在身边，等找到资料了再重新整理一遍。

---

先说点别的。在《计算机网络》有专门一章是讲无线网络的，并对有线和无线进行了对比。其中重要的一点是防止碰撞。

有线网络(以太网)是用的碰撞检测，时间，xxus

而无线网络，由于种种原因，不能用xxxx，而采用了xxxx，它的目的就是没有碰撞，或者是碰撞后直接丢包？2.4GHz的xx，实际并不一定刚好是2.4G，而是有多个频道，比如在2.48G一个，xxx一个

这样在不同频道，就不会碰撞了(信号影响还是会有)

但是现在的网络环境比较复杂，比如小区里面，你一个房子，上下左右好多个路由器以及其他能收发2.4G信号的设备，导致干扰严重，经常跳频，以防冲撞。这个时候又要重新搜索与建立连接。。。

再回到Google Wifi这个设备，与一般的路由器不同，它不是等到真的冲突了碰撞了才切换，而是进行定时检测，类似于CDN内部发送探测包，来检测网络环境一样。这样就可以自动更新到拥塞最少的频道，进而提高网络质量。

根据视频所述，目前的路由器基本上都是固定在一个频道，然后堵塞了/碰撞了才会切？相比而言，Google Wifi的调优策略更有主动性。其算法还没有仔细研究过。不过。。我觉得在当前复杂的网络环境下，基本没有太多空闲的频道可言，这样的调优在人口密集的小区可能用处不大。可能需要拓展带宽，协议，

5G？
