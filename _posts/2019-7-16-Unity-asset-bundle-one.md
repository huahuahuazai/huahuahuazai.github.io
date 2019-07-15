---
layout: post
title:  "Unity Asset Bundle 入门讲解"
categories: unity
tags: assetbudle
author: FSH
---

* content
{:toc}


# Asset Bundle 是什么？

* 可以归为两点：

    * 它是一个存在于硬盘上的文件。可以称之为压缩包。这个压缩包可以认为是一个文件夹，里面包含了多个文件。这些文件可以分为两类：serialized file(序列化文件) 和 resource files(资源文件);
    serialized file：资源被打碎放在一个对象中，最后统一被写进一个单独的文件(只有一个);
    resource files：某些二进制资源（图片、声音）被单独保存，方便快速加载。

    * 它是一个AssetBundle对象，我们可以通过代码从一个特定的压缩包加载出来的对象.这个对
    象包含了所有我们当初添加到这个压缩包里面的内容，我们可以通过这个对象加载出来使用。






# Asset Bundle 的作用

* AssetBundle是一个压缩包，包含模型、贴图、预制体、声音、甚至整个场景，可以在游戏运行的时候被加载；
* AssetBundle自身保存着互相的依赖关系；
* 压缩包可以使用LZMA和LZ4压缩算法，减少包大小，更快的进行网络传输；
* 把一些可以下载内容放在AssetBundle里面，可以减少安装包的大小；

# Asset Bundle 使用流程（简称AB）

![](http://ww1.sinaimg.cn/large/006zwgbUly1g50j5fwwgwj30fa043759.jpg)

![](http://ww1.sinaimg.cn/large/006zwgbUly1g50j5i6rymj30fa04d3zg.jpg)

* 指定资源的AssetBundle属性（xxxa/xxx）这里xxxa会生成目录，名字为xxx
* 构建AssetBundle包
* 上传AB包
* 加载AB包和包里面的资源

## 使用详解

### 1、构建Asset Bundle

* 构建时unity是根据AssetBundle的标签进行工作的，所有构建之前需要对要打成Asset Bundle的对象指定AssetBundle标签；

![](http://ww1.sinaimg.cn/large/006zwgbUly1g50mzbartqj30e503waa0.jpg)

![](http://ww1.sinaimg.cn/large/006zwgbUly1g50n1y2ehhj309s04ddfu.jpg)

    new:新增标签，格式xx/xx/xx代表路径；
    Remove Unused Names:移除未使用的标签；
    后缀可随意，也可不设置，没有影响。适当的定义后缀，可在一定程度上防止被别人破解资源。
**标签一经创建不可改名，写错的话，只能取消使用，移除后重新创建**。

* AssetBundle构建API
    * tarPath 目标文件夹
        > asset bundles 保存的位置；
        > 路径可以是xx/xx/xx，根据自己项目情况而定；
        > 单文件名称，则与Assets同级；
        > 若目标文件夹不存在需要先创建，unity不会自动创建。
    * BuildAssetBundleOptions 压缩方式
        > BuildAssetBundleOptions.None：使用LZMA算法压缩，压缩的包更小，但是加载时间更长。使用之前需要整体解压。一旦被解压，这个包会使用LZ4重新压缩。使用资源的时候不需要整体解压。在下载的时候可以使用LZMA算法，一旦它被下载了之后，它会使用LZ4算法保存到本地上。
        > BuildAssetBundleOptions.UncompressedAssetBundle：不压缩，包大，加载快
        BuildAssetBundleOptions.ChunkBasedCompression：使用LZ4压缩，压缩率没有LZMA高，但是我们可以加载指定资源而不用解压全部。
        ** 注意使用LZ4压缩，可以获得可以跟不压缩想媲美的加载速度，而且比不压缩文件要小**
    * BuildTarget 目标平台
        >选择build出来的AB包要使用的平台
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g50oa0yw41j30ji04kq31.jpg)

```
//assetbundle保存路径文件夹
string tarPath = "AssetBundles";
//若文件夹不存在，需要先创建文件夹，不会自动创建
if (!Directory.Exists(tarPath))
    Directory.CreateDirectory(tarPath);
//构建Asset Bundles
BuildPipeline.BuildAssetBundles(tarPath, BuildAssetBundleOptions.None, BuildTarget.StandaloneWindows64);
```