---
layout: post
title:  "Unity 图集的理解与使用"
categories: unity
tags: spritepacker
author: FSH
---

* content
{:toc}

# 理解图集

* 所谓图集就是将很多零碎的2d小图整合成一张大图，做成大图后我们不仅会想，为什么这么做呢？这样做有什么好处呢？
* 图集的优点：
    * 提升效率

        图片尺寸为2的次幂时，GPU处理起来会快很多，小图自己是做不到每张图都是2的次幂的，但打成一张大图就可以（浪费一点也无所谓）；
    * UI的合批处理，减少drawcall

        打成图集后，CPU在传送资源信息给GPU时，只需要传一张大图就可以了，因为GPU可以在这张图中的不同区域进行采样，然后拼出对应的界面。注意，这就是为什么一个UI界面需要用同一个图集的原因，是Batch的关键，因为一个Drawcall就把所有原材料传过去了，GPU你画去吧。
    * 避免资源浪费

        我们用来做sprite 的图片，通常会留有很多空白的地方，我们在画完了sprite之后，这些地方很可能就没有什么作用了，打成图集后把图片上的空间尽量利用得充实一点，就可以在一定程度上避免这些浪费。





        
    **但也不可盲目的都打成一个图集，太大了并不好，在后面的注意事项里会详细说明。**

# 打包图集

* 在Editor模式下，默认是不打图集的（此模式下使用图集比较慢），此时想要打成图集，有两种方法：
    * Edit >> Project Seting >>Editor>>Sprite Packer的Mode需要设置为Always Enabled 即可开启自动打图集
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58q1kbw91j30e40jsq3h.jpg)
    * 手动利用系统自带的打包工具SpritePacker（也可以使用第三方的工具TexturePacker进行图集制作）
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58qhk83juj30ae0i3aah.jpg)  ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58qkgtaibj306i0cawed.jpg)
* SpritePacker打图集时是根据图片的 packing tag 来工作的，它把packing tag相同的sprite打到一个图集里，packing tag如果为空的话，则该图片不会被打到图集里，需要注意的是Resources中的图片不会被打包到图集。
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58qne1z9aj30dt093t8y.jpg)
* 举个例子
    
    * 三个图的设置为：img1 >> packing tag(img1);img2 >> packing tag(img2);img3 >> 空；此时打了两个图集，分别为img1,img2。img3未达成图集img1,img2为别位于对应的图集里：
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58r989u7vj30bp0hwq54.jpg)
    *  三个图的设置为：img1 >> packing tag(img1);img2 >> packing tag(img2);img3 >> packing tag(img3)；此时分别打了三个图集：
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58rd9jrzlj30bq0hqdht.jpg)
    * 三个图的设置为:img1 >> packing tag(img);img2 >> packing tag(img);img3 >> packing tag(img),此时只打了一个图集，包含此三张图：
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58rfg2e8aj30bk0htq58.jpg)

# 使用图集的注意事项

* 能够打图集的要求
    * 图片需要设置为精灵模式；
    * 需要指定packing tag，否则不会打进图集；
    * Resources中的图片不会被打包到图集；
* 图集分类
    * 设计UI时要考虑重用性，如一些边框、按钮等，这些作为共享资源，放在1~3张大图集中，称为重用图集;
    * 其它非重用UI按照功能模块进行划分，每个模块使用1~2张图集，为功能图集;
    * 对于一些UI，如果同时用到功能图集与重用图集，但是其功能图集剩下的“空位”较多，则可以考虑将用到的重用图集中的元素单独拎出来，合入功能图集中，从而做到让UI只依赖于功能图集。也就是通过一定的冗余，来达到性能的提升;

**注意控制图集的大小，不要让图集太大，一个超级大图集的DrawCall消耗或许顶的上十几个小图集的消耗**

# 辅助工具

* 图片导入时自动设置模式，根据文件夹路径设置packing tag
    ``` c#
        using UnityEngine;
        using UnityEditor;

        public class AutoSpritePacker : AssetPostprocessor
        {
            /// <summary>
            /// 标识是否初次导入
            /// </summary>
            private string m_Version = "1";
            /// <summary>
            /// 最大尺寸，一般为1024，根据自己项目需要设置
            /// </summary>
            private int maxSize = 1024;

            /// <summary>
            /// 需要处理的图片根路径（并不是所有的图片都需要这样处理）
            /// 这里是定义出来，项目里一般会有这些资源路径的管理分类，直接获取即可
            /// </summary>
            private string tarPath = "Assets/Picture";

            /// <summary>
            /// 得到导入图片的文件夹路径，作为其 packing tag
            /// </summary>
            /// <param name="path"></param>
            /// <returns></returns>
            private string DisposePath(string path)
            {
                string tempPath = "";
                tempPath = path.Replace("\\", "/");
                int lastIndex = tempPath.LastIndexOf(@"/");
                tempPath = tempPath.Substring(0, lastIndex);
                return tempPath;
            }

            /// <summary>
            /// 图片导入时自动处理
            /// </summary>
            /// <param name="texture"></param>
            void OnPostprocessTexture(Texture2D texture)
            {
                //判断导入的图片是否在目标路径下
                if (assetPath.Contains(tarPath))
                {
                    string packingTag = DisposePath(assetPath);
                    TextureImporter textureImporter = assetImporter as TextureImporter;

                    //新导入的
                    if (textureImporter.userData != m_Version)
                    {
                        //设置为精灵模式
                        textureImporter.textureType = TextureImporterType.Sprite;
                        //设置默认格式
                        textureImporter.spriteImportMode = UnityEditor.SpriteImportMode.Single;
                        //设置 packing tag
                        textureImporter.spritePackingTag = packingTag;
                        //默认取消 mipmap（太占内存，需要的话在游戏里针对性处理）
                        textureImporter.mipmapEnabled = false;
                        //标记导入过
                        textureImporter.userData = m_Version;
                        //根据图片是否有透明通道设置alphaIsTransparency
                        textureImporter.alphaIsTransparency = textureImporter.DoesSourceTextureHaveAlpha();
                        //设置特定的目标平台设置
                        TextureImporterPlatformSettings setting = textureImporter.GetDefaultPlatformTextureSettings();
                        //设置最大尺寸
                        setting.maxTextureSize = maxSize;
                        textureImporter.SetPlatformTextureSettings(setting);
                        //保存
                        textureImporter.SaveAndReimport();
                    }
                    //已处理过的（有些是不需要打图集的）
                    else
                    {
                        textureImporter.spritePackingTag = textureImporter.spritePackingTag != string.Empty ? packingTag : string.Empty;
                    }
                    EditorUtility.SetDirty(textureImporter);
                }
            }
        }
    ```

* 打图集时的一些自定义设置

    这个主要没有自己写，参考的雨松大大的博文，很完善，这里就不多做说明了，[去这里看吧](https://www.xuanyusong.com/archives/3315)

# 补充

Unity2017之后开始使用Sprite Atlas（精灵图集），我也学习了下，大家可以看下我的这篇文章[Unity SpriteAtlas的学习](https://huahuahuazai.github.io/2019/07/23/Unity-sprite-atlas/)