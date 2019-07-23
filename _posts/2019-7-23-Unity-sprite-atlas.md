---
layout: post
title:  "Unity SpriteAtlas的学习"
categories: unity
tags: atlas
author: FSH
---

* content
{:toc}

# 简介

Unity2017之后开始使用Sprite Atlas（精灵图集），Sprite Atlas用于替换现有的Sprite Packer，让制作图集（Atlas）的过程更加便利和高效。针对现有的图集打包系统Sprite Packer在性能和易用性上的不足，进行了全面改善。除此之外，相比Sprite Packer，Sprite Atlas将对精灵更多的控制权交还给用户。由用户来掌控图集的打包过程以及加载时机，更加利于对系统性能的控制。






# 属性说明

Sprite Atlas是一种资源，可以像其它资源一样在Unity中创建，例如预制件、场景等。可以在检视窗口中设定要打包的精灵及其参数，例如图集的打包方式、输出贴图的压缩格式等，属性一览：
![](http://ww1.sinaimg.cn/large/006zwgbUly1g59tyh92wbj30dt0eqq3b.jpg)

* Type有Master,Variant两种，所谓Variant，就是指原有图集的一个变种。它会复制原有图集的贴图，并根据一个比例系数(scale)来调整复制贴图的大小。这样的Variant通常用于为高分辨率和低分辨率的屏幕准备不同的图集；
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g59ua5uo68j30rs0f6dh3.jpg)

    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g59uau3ilfj30rr0j8dh3.jpg)
* Include in Build：仅作用于编辑器中，如果勾选就在点击运行时自动加载纹理集，否则不自动加载，并触发SpriteAtlasManager.atlasRequested 事件，需要在回调里自己手动填充；
* Tight Packing：可以更大程度压缩镂空的图片，但是镂空的部分，会放入别的图片，很有可能造成他们意外地出现在你的UI中；
* Allow Rotation：打包时是否支持精灵旋转；
* Filter Mode：贴图的采样模式；
* Objects for Packing：需要打到这个图集的图片拖到这里。

# 使用流程

* 设置 Editor Settings 中 Sprite Packer 的 Mode 为 Always Enabled
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g59uu23ppzj30dk09ldg5.jpg)
* 通过 Project 面板的右键创建 Sprite Atlas
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g59uv82exkj30fu0dj0t6.jpg)
* 选中创建的 Sprite Atlas, 并在 Inspector 面板的 Objects for Packing部分添加需要打到图集里的sprite
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g59uwymio4j30aa0e6jro.jpg)
* 点击 Pack Preview 按钮预览合并的纹理集
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g59uy82rp5j30ae03qglh.jpg)

* 注意事项：
    * 图片需要设置为Sprite(2D and UI), 否则不能真正包含到 Sprite Atlas 里；
    * 不能将原始纹理集从 Unity 的 Assets 目录中移除，否则将不能正常显示。 即使是被打包到了Asset bundle里也不能删掉原始的纹理集；

# 加载图集

* 运行时访问图集
    ``` c#
        using UnityEditor;
        using UnityEngine;
        using UnityEngine.U2D;
        using UnityEngine.UI;

        public class LoadSprite : MonoBehaviour
        {
            void Start()
            {
                SpriteAtlas atlas = AssetDatabase.LoadAssetAtPath<SpriteAtlas>("Assets/Atlas/master.spriteatlas");
                Sprite sprite = atlas.GetSprite("img1");

                if (sprite != null)
                {
                    GetComponent<Image>().sprite = sprite;
                }
            }
        }
    ```
* 从AssetBundle加载使用图集

    此处摘录自[Unity2017的新spriteAtlas](www.litefeel.com)
    ``` c# 
        using UnityEngine;
        using UnityEngine.U2D;

        public class SpriteAtlasManager : MonoBehaviour {
            void Awake () {
                // 注册事件
                SpriteAtlasManager.atlasRequested += OnAtlasRequested;
            }

            private void OnAtlasRequested(string tag, Action<SpriteAtlas> action) {
                // 加载纹理集
                // unity2017.1版本：当收到回调后必须立刻用纹理集填充
                // SpriteAtlas atlas = LoadAtlas(tag);
                // action(atlas);
                // 2018.2.1f1版本：可以异步加载纹理集，只需向action回调填充纹理集就可以了
                // 当纹理集没有被填充前，Image等组件将显示为默认的白色纹理
                // 一旦纹理填充后，Image等组件将自动显示为正确的纹理
                StartCoroutine(DoLoadAsset(action, tag));
            }

            private IEnumerator DoLoadAsset(Action<SpriteAtlas> action, string tag)
            {
                yield return new WaitForSeconds(3);
                var ab = AssetBundle.LoadFromFileAsync(getpath(tag));
                yield return ab;

                print("DoloadAsset frame:" + Time.frameCount);
                var sa = ab.assetBundle.LoadAsset<SpriteAtlas>(tag);
                print("sa: " + sa);
                action(sa);
            }
        }
    ```