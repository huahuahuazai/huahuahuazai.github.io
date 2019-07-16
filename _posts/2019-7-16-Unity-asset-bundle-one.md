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

# 使用详解

## 1、构建Asset Bundle

* 构建时unity是根据AssetBundle的标签进行工作的，所有构建之前需要对要打成Asset Bundle的对象指定AssetBundle标签；

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g50mzbartqj30e503waa0.jpg)

    * new:新增标签，格式xx/xx/xx代表路径,构建后在保存在对应路径下；
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g50n1y2ehhj309s04ddfu.jpg)![](http://ww1.sinaimg.cn/large/006zwgbUly1g51mjf3tgvj305w0223ya.jpg)![](http://ww1.sinaimg.cn/large/006zwgbUly1g51lumyh2pj305g022a9v.jpg)![](http://ww1.sinaimg.cn/large/006zwgbUly1g51lv3e722j306t02wdfo.jpg)

    * Remove Unused Names:移除未使用的标签；
    * 后缀可随意，也可不设置，没有影响。适当的定义后缀，可在一定程度上防止被别人破解资源。

    **标签一经创建不可改名，写错的话，只能取消使用，移除后重新创建**。

* AssetBundle构建API

    ``` c#
        //assetbundle保存路径文件夹
        string tarPath = "AssetBundles";
        //若文件夹不存在，需要先创建文件夹，不会自动创建
        if (!Directory.Exists(tarPath))
            Directory.CreateDirectory(tarPath);
        //构建Asset Bundles
        BuildPipeline.BuildAssetBundles(tarPath, BuildAssetBundleOptions.None, BuildTarget.StandaloneWindows64);
    ```
* tarPath 目标路径
    * asset bundles 保存的位置；
    * 路径可以是xx/xx/xx，根据自己项目情况而定；
    * 单文件名称，则与Assets同级；
    * 若目标文件夹不存在需要先创建，unity不会自动创建。

* BuildAssetBundleOptions 压缩方式
    * BuildAssetBundleOptions.None：使用LZMA算法压缩，压缩的包更小，但是加载时间更长。使用之前需要整体解压。一旦被解压，这个包会使用LZ4重新压缩。使用资源的时候不需要整体解压。在下载的时候可以使用LZMA算法，一旦它被下载了之后，它会使用LZ4算法保存到本地上。
    * BuildAssetBundleOptions.UncompressedAssetBundle：不压缩，包大，加载快
    BuildAssetBundleOptions.ChunkBasedCompression：使用LZ4压缩，压缩率没有LZMA高，但是我们可以加载指定资源而不用解压全部。

    **注意:使用LZ4压缩，可以获得可以跟不压缩想媲美的加载速度，而且比不压缩文件要小**

* BuildTarget 目标平台
    * 选择build出来的AB包要使用的平台,不同平台之间的资源只能在对应的平台使用。
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g50oa0yw41j30ji04kq31.jpg)

## 2、Asset Bundle 依赖

* 游戏中有很多复用的资源，图中两个对象分别使用了相同的资源，分别打成assetbundle,资源就会打两份，包体会变大；我们可以把公用的资源打在一起，独有的信息另外单独打在一起，就有了依赖，两个prefab分别依赖于材质的assetbundle。

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g51t7w6zxtj30jv08gglx.jpg)

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g51ttbs3jqj30xa05u74x.jpg)
    
## 3、Asset Bundles 加载

* 从本地加载
    * 同步加载(AssetBundle.LoadFromFile)
        ``` c#
            /// <summary>
            /// 从本地同步加载
            /// </summary>
            void RealCreateFromLoacl()
            {
                string cubeAbPath = "AssetBundles/wall/cubewall.unity";
                AssetBundle ab = AssetBundle.LoadFromFile(cubeAbPath);
                GameObject obj = ab.LoadAsset<GameObject>("cubewall");

                Instantiate(obj);
            }
        ```

    * 异步加载(AssetBundle.LoadFromFileAsync)
        ``` c#
            /// <summary>
            /// 从本地异步加载
            /// </summary>
            /// <returns></returns>
            IEnumerator AsyncCreateFromLocal ()
            {
                string cubeAbPath = "AssetBundles/wall/cubewall.unity";
                AssetBundleCreateRequest abRequest = AssetBundle.LoadFromFileAsync(cubeAbPath);
                yield return abRequest;
                AssetBundle ab = abRequest.assetBundle;
                GameObject obj = ab.LoadAsset<GameObject>("cubewall");

                Instantiate(obj);
            }
        ```

* 从内存中加载
    * 同步加载(AssetBundle.LoadFromMemory)
        ``` c#
            /// <summary>
            /// 从内存中同步加载
            /// </summary>
            void RealLoadFromMemory()
            {
                string cubeAbPath = "AssetBundles/wall/cubewall.unity";
                //读取字节流
                byte[] abBytes = File.ReadAllBytes(cubeAbPath);
                AssetBundle ab = AssetBundle.LoadFromMemory(abBytes);
                GameObject obj = ab.LoadAsset<GameObject>("cubewall");

                Instantiate(obj);
            }
        ```

    * 异步加载(AssetBundle.LoadFromMemoryAsync)
        ``` c#
            /// <summary>
            /// 从内存中异步加载
            /// </summary>
            /// <returns></returns>
            IEnumerator AsyncLoadFromMemory()
            {
                string cubeAbPath = "AssetBundles/wall/cubewall.unity";
                //读取字节流
                byte[] abBytes = File.ReadAllBytes(cubeAbPath);
                AssetBundleCreateRequest abRequest = AssetBundle.LoadFromMemoryAsync(abBytes);
                yield return abRequest;
                AssetBundle ab = abRequest.assetBundle;
                GameObject obj = ab.LoadAsset<GameObject>("cubewall");

                Instantiate(obj);
            }
        ```

* WWW.LoadFromCacheOrDownload 加载
    * 即可从网络加载，也可从本地加载。 **该方法逐渐被弃用**
        ``` c#
            /// <summary>
            /// 通过www加载
            /// </summary>
            /// <returns></returns>
            IEnumerator LoadFormWWW ()
            {
                // 缓存是否准备好
                while(!Caching.ready)
                {
                    yield return null;
                }

                //参数为：网址/路径，版本号
                WWW www = WWW.LoadFromCacheOrDownload(@"file://F:\Save\Unity_project\Unity_2018\Asset Bundle One\AssetBundles\wall\cubewall.unity", 1);
                yield return www;
                //检查报错
                if (!string.IsNullOrEmpty(www.error))
                {
                    Debug.Log(www.error);
                    yield break; 
                }
                AssetBundle ab = www.assetBundle;
                GameObject obj = ab.LoadAsset<GameObject>("cubewall");

                Instantiate(obj);
            }
        ```

* UnityWebRequest 加载
    * 只能从服务器下载，是用来替代www从服务器加载的
        ``` c#
            /// <summary>
            /// 通过 UnityWebRequest 加载
            /// </summary>
            /// <returns></returns>
            IEnumerator LoadFormWebRequest()
            {
                string url = @"file://F:\Save\Unity_project\Unity_2018\Asset Bundle One\AssetBundles\wall\cubewall.unity";
                UnityWebRequest request = UnityWebRequestAssetBundle.GetAssetBundle(url);
                yield return request.SendWebRequest();

                //方式一
                //AssetBundle ab = DownloadHandlerAssetBundle.GetContent(request);
                //方式二
                AssetBundle ab = (request.downloadHandler as DownloadHandlerAssetBundle).assetBundle;
                GameObject obj = ab.LoadAsset<GameObject>("cubewall");

                Instantiate(obj);
            }
        ```

* AB 加载方式：
    * AssetBundle.LoadFromFile 从本地加载;
    * AssetBundle.LoadFromMemory 从内存加载;
    * WWW.LoadFromCacheOrDownload 下载后放在缓存中备用(该方法逐渐被弃用);
    * UnityWebRequest 从服务器下载.

* 从AB中加载资源：
    * AssetBundle.LoadAsset(assetName)
    * AssetBundle.LoadAssetAsync() 异步加载，加载较大资源的时候
    * AssetBundle.LoadAllAssets() 加载AB包中所有的对象，不包含依赖的包
    * AssetBundle.LoadAllAssetsAsync() 异步加载全部资源
    * AssetBundle.LoadAssetWithSubAssets 加载资源及其子资源

* 依赖包加载
    * 使用有依赖的ab时，必须加载其所依赖的ab，否则显示是不正常的。

## 4、Asset Bundles 卸载

* 卸载有两个方面
    * 减少内存使用
    * 有可能导致丢失

* 卸载时机
    * AssetBundle.Unload(true) 卸载所有资源，即使有资源被使用着
        * 在关切切换、场景切换
        * 资源没被用的时候调用
    * AssetBundle.Unload(false) 卸载所有没用被使用的资源
	* Resources.UnloadUnusedAssets 卸载个别未使用的资源

## 5、Asset Bundle 分组策略、建议

* 策略
    * 逻辑实体分组
        * 一个UI界面或者所有UI界面一个包（这个界面里面的贴图和布局信息一个包）
        * 一个角色或者所有角色一个包（这个角色里面的模型和动画一个包）
        * 所有的场景所共享的部分一个包（包括贴图和模型）

    * 按照类型分组
        * 所有声音资源打成一个包，所有shader打成一个包，所有模型打成一个包，所有材质打成一个包

    * 按照使用分组
        * 把在某一时间内使用的所有资源打成一个包。可以按照关卡分，一个关卡所需要的所有资源包括角色、贴图、声音等打成一个包。也可以按照场景分，一个场景所需要的资源一个包

* 建议
    * 把经常更新的资源放在一个单独的包里面，跟不经常更新的包分离
    * 把需要同时加载的资源放在一个包里面
    * 可以把其他包共享的资源放在一个单独的包里面
    * 把一些需要同时加载的小资源打包成一个包
    * 如果对于一个同一个资源有两个版本，可以考虑通过后缀来区分  v1  v2  v3  unity3dv1 unity3dv2


