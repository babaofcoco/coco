---
layout:     post
title:      "使用OpenCV检测移动的物体"
subtitle:   "视频监控中进行物体移动检测的技术"
date:       2017-3-29 12:10:00
author:     "Hux"
header-img: "img/post-bg-kuaidi.jpg.jpg"
tags:
    - OpenCV
    - 视频监控
    - 移动检测
    - Motion Detection
---

> 今天是2017年3月29日，距离清明节放假还有三天，想到马上就能见到YoYo和CoCo，内心还有点小激动，激动之余写个blog吧。Blog的内容取自在客户给的一个需求，需要在视频监控中进行物体变化的识别。

<br>一看到这个物体变化的识别，立马就想到了OpenCV(Open Source Computer Vision)[官网：<a href="http://www.opencv.org">http://www.opencv.org</a>]。移动检测的实现方法有很多, 今天先研究下Background subtraction[<a href="http://docs.opencv.org/master/db/d5c/tutorial_py_bg_subtraction.html">OpenCV介绍</a>]。下面引用一下互联网上对Background Subtraction的解释：

<br>
<i>
Background subtraction基本原理：首先取一张静态的背景图（不包含要检测的移动物体），然后比较监控图像（包含移动物体）和背景图，找到不同区域，这个区域就是要检测的物体。在现实环境中要复杂的多，我们还要考虑到光线变化、阴影、反射等等影响背景环境的因素。
</i>

```python
import cv2
import time
 
camera = cv2.VideoCapture(0)
if camerais None:
    print('请先连接摄像头')
    exit()
 
fps = 5 # 帧率
pre_frame = None  # 总是取前一帧做为背景（不用考虑环境影响）
 
play_music = False
 
while True:
    start = time.time()
    res, cur_frame = camera.read()
    if res != True:
        break
    end = time.time()
    seconds = end - start
    if seconds < 1.0/fps:
        time.sleep(1.0/fps - seconds)

    gray_img = cv2.cvtColor(cur_frame, cv2.COLOR_BGR2GRAY)
    gray_img = cv2.resize(gray_img, (500, 500))
    gray_img = cv2.GaussianBlur(gray_img, (21, 21), 0)
 
    if pre_frameis None:
        pre_frame = gray_img
    else:
        img_delta = cv2.absdiff(pre_frame, gray_img)
        thresh = cv2.threshold(img_delta, 25, 255, cv2.THRESH_BINARY)[1]
        thresh = cv2.dilate(thresh, None, iterations=2)
        image, contours, hierarchy = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
        for c in contours:
            if cv2.contourArea(c) < 1000: # 设置敏感度
                continue
            else:
                #print(cv2.contourArea(c))
                print("前一帧和当前帧不一样了, 有什么东西在动!")
                play_music = True
                break
 
        pre_frame = gray_img
 
camera.release()
cv2.destroyAllWindows()
```

<br><b>相关资源</b>
<ul>
<li>https://github.com/RobinDavid/Motion-detection-OpenCV </li>
<li>https://github.com/cedricve/motion-detection </li>
<li>motion－Linux下的运动检测工具 </li>
</ul>







