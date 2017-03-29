---
layout:     post
title:      "使用OpenCV检测移动的物体"
subtitle:   "视频监控中进行物体移动检测的技术"
date:       2017-3-29 12:10:00
author:     "Hux"
header-img: "img/post_bg_helicopter.jpg"
tags:
    - OpenCV
    - 视频监控
    - 移动检测
    - Motion Detection
---

> 今天是2017年3月29日，距离清明节还有清明节还有三天，想到马上就能见到YoYo和CoCo，内心还有点小激动，激动之余写个blog吧。Blog的内容取自在客户给的一个需求，需要在视频监控中进行物体变化的识别。

<br>一看到这个物体变化的识别，立马就想到了OpenCV(Open Source Computer Vision)[官网：<a href="http://www.opencv.org">http://www.opencv.org</a>]。移动检测的实现方法有很多, 今天先研究下Background subtraction[<a href="http://docs.opencv.org/master/db/d5c/tutorial_py_bg_subtraction.html">OpenCV介绍</a>]。下面引用一下互联网上对Background Subtraction的解释：

<br>
<i>
Background subtraction基本原理：首先取一张静态的背景图（不包含要检测的移动物体），然后比较监控图像（包含移动物体）和背景图，找到不同区域，这个区域就是要检测的物体。在现实环境中要复杂的多，我们还要考虑到光线变化、阴影、反射等等影响背景环境的因素。
</i>







