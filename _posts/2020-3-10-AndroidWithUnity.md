---
layout: post
title:  "Android与Unity交互小白向学习记录"
categories: unity
tags: build
author: FSH
---

* content
{:toc}


# Android与unity交互的几种方式

**本人安卓开发纯小白，主要做unity开发，下面都是自己学习的一些理解，说的不合适的地方多多见谅**

* android主导
    * 在android中拓展unity activity并导出JAR包；
    * 在android中拓展unity activity并导出AAR包；
    * 在android中拓展类并导出JAR包。

* unity主导
    * 全部在c#内进行作业；






# 详细步骤

一、android主导

1、在android中拓展unity activity并导出JAR包

**该方法适用广，较复杂，目前unity官方已不推荐使用**

* 在android studio 中新建安卓工程

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp5mu6gznj30zu0c60u2.jpg)

* 创建一个新的android library

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp5rz3vkpj30ib0e1dh3.jpg)

* 导入对应的unity jar包
    * 因为是对unity 的项目进行扩展需要导入unityJAR包，路径：unity安装目录下 Editor\Data\PlaybackEngines\AndroidPlayer\Variations\\**mono**\Release\Classes，其中momo也可以是il2cpp，根据自己项目设置选择，然后拷贝粘贴上面创建的library中

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp60o3meej30kb0iygp9.jpg)

* 创建空的Activity
    * 由于是拓展unity activity所以要把附带创建的界面也删掉
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp67igz07j30l70msafh.jpg)

* 修改mainactivity
    * 删除爆红的代码并继承自UnityPlayerActivity
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp6b4jrtdj30er0fz0uv.jpg)

* 修改manifest
    * 删除爆红的字段，保留并修改lable，切记添加以下代码
    ``` java
        <meta-data android:name="unityplayer.UnityActivity" android:value="true" />
    ```

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp6fmpk3hj30rg0dywg1.jpg)

* 在mainactivity中添加方法
    * 添加自己需要用的到函数，这里我添加了一个调用toast多的方法

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp6ofv9zbj30pd09sdgv.jpg)

* build自己的库

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp6qe9rs4j30gz077wex.jpg)

* 找出自己需要的东西修改并放到unity中（Pulgins/Android）中
    * 需要修改AndroidManifest.xml中的包名
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp6yr0c1xj30ja048aad.jpg)

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp6unku4lj30u30gdwht.jpg)

* 在unity中使用

    ``` c#
        AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
        AndroidJavaObject jo = jc.GetStatic<AndroidJavaObject>("currentActivity");
        jo.Call("ShowToast","send form unity");
    ```

2、在android中拓展unity activity并导出AAR包

**该方法适用广，较复杂，unity官方推荐使用**

* 前面步骤与JAR包的使用完全相同，只是build之后拿给Unity使用的资源不同；其实AAR就是一个压缩包，包含了JAR需要的资源，官方给整合了

* 拷贝并修改AAR

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp7fcsc60j30ht0p4gr1.jpg)

* unity中使用与JAR完全相同

3、在android中拓展类并导出JAR包，不修改unity activity

**该方法适用少，简单**

* 新建java类

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp7kffcfuj30e10d9js6.jpg)

* 添加需要的函数

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp7l6lscwj30gr091mxb.jpg)

* build和使用jar包

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp7qpryu6j30od07eac2.jpg)

    ``` c#
        AndroidJavaObject jo = new AndroidJavaObject("com.huazai.toasttest.toasttest");
        jo.Call("ShowLogErr","UnityTest","tttttttttttttt");
    ```

二、unity主导

* 完全通过c#实现，改方法和上面3基本一样，只不过不用导出JAR包，调用的是android SDK中的函数而已

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1gcp7x1gfhej311q0bbq5h.jpg)

**以上只是我的学习记录而已，有点乱，应该只有自己能看的懂了，哈哈、、、**