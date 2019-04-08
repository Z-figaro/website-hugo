---
title: "ARKit总结"
date: 2018-12-19T15:40:19+08:00


tags: 
  - ios
---
# ARkit是什么？
    
    ARKit是WWDC 17中公开的系统库，可以提供增强现实的体验。
    
## ARKit的工作原理：
    
    1.相机捕捉现实景象---由ARKit实现
    2.在现实景象中显示2d/3d虚拟图像--scenneKit对应3d实现，spriteKit实现2d
    
    
![arkit](https://lh3.googleusercontent.com/-ZqTZUBgF1ig/W8f4EGb3-GI/AAAAAAAAB5s/ja-zOUSGE1QDrqLKA0cuYIAnUr5m1vi9ACHMYCw/I/arkit.png)


### ARscnview

用于显示3d虚拟的视图
作用：管理一个ARsession，一个arscnview实例默认持有一个arsession。

### ARsession

ARSession主要用于协调在构建增强现实体验的过程中各个关键步骤，如读取传感器数据，控制相机，分析图片等。

### ARconfiguration

ARWorldTrackingConfiguration：跟踪设备的位置、方向、平面检测和hit testing。
AROrientationTrackingConfiguration：仅用于方向跟踪。
ARFaceTrackingConfiguration：前置摄像头仅用于人脸跟踪。

### ARframe

相机的位置数据，实质就是各种位置数据

配合ARSession使用（currentFrame属性）,ARSession不停的获取视频帧信息，并结合底层的传感器信息，将处理好的跟踪信息和图像参数放到这个类中。


