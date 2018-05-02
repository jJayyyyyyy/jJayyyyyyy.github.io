---
layout: post
title: LeetCode Tips
---


<br>

*   io加速

    在最前面( `Solution` 之前 ), 加上如下代码, 禁用多余功能, 进行io加速

    ```cpp
    static const auto io_speed_up = []() {
    	std::ios::sync_with_stdio(false);
    	cin.tie(0);
    	return 0;
    } ();
    ```
    
*   VS 文本编码方式, GBK --- UTF-8

    FILE ---> Advanced Save Options ---> UTF-8 without signiture

    
<br><br>

本文地址：[https://jjayyyyyyy.github.io/2018/05/02/LeetCodeTips.html](https://jjayyyyyyy.github.io/2018/05/02/LeetCodeTips.html)

(END)

---

##	相关阅读
