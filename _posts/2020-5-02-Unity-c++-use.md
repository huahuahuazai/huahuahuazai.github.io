---
layout: post
title: "unity 各平台C++库的调用"
categories: unity
tags: c++
author: FSH
---

* content
{:toc}


#### 一、unity 使用C++文件的方式

1、windows下需把c++文件编译为dll；

2、android下需把c++文件编译为.so文件；

3、IOS下需把c++文件编译为.a文件；






#### 二、各平台c++文件编译

##### 1、windows下dll编译 - 此处使用VS2017

- 创建动态链接库工程
    - 文件 》 新建 》 项目
        - ![1.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghltneb553j30fz01ddfq.jpg)

    - 创建c++动态链接库工程
        - ![2.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghltr3n261j30nn0gtjsc.jpg)
    
- 删除默认创建的c++文件

&ensp;&ensp;&ensp;&ensp;![3.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlts9rzi9j307m09eq32.jpg)

- 拷贝自己的C++文件到工程目录

![5.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghltx91ax9j30fo07q3yv.jpg)

- 把拷贝过来的C++源文件及头部文件分别添加到项目内

![4.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghltzhmxqfj30n504g74t.jpg)

- 修改项目属性取消使用预编译头

![6.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlu4gdfszj30ns0c5t91.jpg)

- 根据需要生成对应平台的debug或者release即可

##### 2、android下.so编译 - 使用NDK编译

- 创建jni文件夹
- 把自己的C++文件放置到jni文件夹内
- 创建Android.mk文件，并设置如下

![7.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghluaajrefj30ox0880t4.jpg)

- 创建Application.mk文件夹，并设置如下

![8.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlubaq1g4j30ie072jrl.jpg)

- 打开cmd，cd到jni目录下执行ndk-build即可，生成的.so在上级目录libs内

![9.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlucnssf1j30cq05rmx3.jpg)

##### 3、IOS内.a文件生成

- 在xcode内创建IOS的静态链接库项目
    - ![10.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghluh3dllnj30c807nmy3.jpg)
    - ![11.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghluhryk9hj30jk0dijs7.jpg)
    - ![12.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghluiqii2jj30g80b3mxg.jpg)
    
- 删除默认的c++文件
    - ![13.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlujrtwumj308005aglj.jpg)
    
- 导入自己的C++文件
    - ![14.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlul7ywurj308z05vdg3.jpg)
    - ![15.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlum11wppj307903k3ye.jpg)

- 修改项目配置，添加头文件配置
    - ![16.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghluosawgbj30nj06fmy1.jpg)
    - ![17.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlup9giu4j30nl0e00tc.jpg)
    - 添加弹出框内所有.h
        - ![18.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlupxuz0rj30ar0cdwep.jpg)

- 修改编译指令集 debug - yes; release - no
    - ![19.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghluru5lcgj30ls08q3ze.jpg)

- Command+B 进行编译生成对应的.a文件
    - ![20.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghlut3nbkbj307i06874a.jpg)