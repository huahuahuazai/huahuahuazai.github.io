---
layout: post
title: "unity 纹理属性及UI图片导入设置"
categories: unity
tags: Texture
author: FSH
---

* content
{:toc}


#### 一、纹理属性
1. Texture Type

- unity中常见纹理类型有以下8种：

&ensp;&ensp;![1.png](http://ww1.sinaimg.cn/large/006zwgbUly1ggrllvtlbcj30ce05w3yl.jpg)






- editor gui and legacy gui：编辑器和传统GUI；
- cursor：自定义光标；
- lightmap：光照贴图，将贴图编码成特定的格式，并对纹理数据进行后处理；
- single channel：单通道。
- Default ：默认最常用的导入类型，大部分导入参数均可访问

    ![2.png](http://ww1.sinaimg.cn/large/006zwgbUly1ggrmncyo8dj30ec09smxg.jpg)
    - Texture Shape：纹理形状，2D或Cube(立方体)；
    - sRGB(Color Texture)：指定纹理是否保存在Gamma空间；
    - Alpha Source：透明通道来源
        - None：强制无透明通道；
        - Input Texture Alpha：使用纹理自带透明通道；
        - From Gray Scale：使用纹理RGB通道均值来生成透明通道；
    - Alpha is Transparency：透明通道是透明的；
    - Non Power of 2：图片大小非2的次幂处理方式
        - None：纹理尺寸大小保持不变；
        - ToNearest：纹理缩放为最为接近的2的次幂的尺寸（257x511 -> 256x512）；
        - ToLarger：纹理缩放为尺寸最大值所接近的2的次幂尺寸（257x511 -> 512x512）；
        - ToSmaller：纹理缩放为尺寸最小值所接近的2的次幂尺寸（257x511 -> 256x256）；
    - Read/Write Enabled：纹理读写开关，非必要不开启，否则增加一倍内存；
    - Streaming Mip Maps：流式mip贴图，使用此选项可在贴图上使用纹理流；
    - Generate Mip Maps：生成Mip Maps，会增加33%的内存。一般用于模型纹理；UI、天空盒等纹理不需要开启；
        - Border Mip Maps：防止低阶的MipMap的色彩值溢出边界，一般用于光照Cookie；
        - Mip Map Filtering：过滤算法，Box和Kaiser；
        - Fadeout Mip Maps：根据层阶使Mip Map慢慢变灰，一般用于细节贴图（DetailMaps）；
    - Wrap Mode：循环（平铺）模式，当UV<0或>1时，如何采样
        - Repeat：在图块中重复纹理；
        - Clamp：拉伸纹理的边缘；
        - Mirror：在每个整数边界处创建纹理镜像重复图案；
        - Mirror Once：镜像一次纹理，然后将其夹到边缘像素；
        - Per-axis：选择此选项可单独控制Unity如何在U轴和V轴上处理纹理；
    - Filter Mode：过滤模式，当被3D变换拉伸时，纹理如何插值
        - Point(nofilter)：点（无过滤），马赛克；
        - Bilinear：双线性，；
        - Trilinear：三线性，在Bilinear的基础上对不同的Mip层阶进行模糊（插值）；
    - AnisoLevel：各项异性滤波（Anisotropic Filtering ）等级，增加纹理在大倾角视角的质量，对底板和地表纹理效果很好；
- Normal Map : 法线贴图

    ![3.png](http://ww1.sinaimg.cn/large/006zwgbUly1ggrnczdjrej30e908l0sy.jpg)
    - Create from Grayscale：将从灰度创建法线贴图的高度图
        - Bumpiness：控制颠簸量。低的凹凸值意味着高度图中鲜明的对比度也会转化为柔和的角度和凹凸；
        - Filtering：滤波算法，确定如何计算颠簸；
            - Smooth：平滑，标准前向差分算法；
            - Sharp：尖锐，Sobel滤波器；
- Sprite(2D and UI)：精灵，用于2D对象和UGUI

    ![4.png](http://ww1.sinaimg.cn/large/006zwgbUly1ggrniyned9j30e90akgm2.jpg)
    - Sprite Mode：精灵模式
        - Single：单图；
            - Mesh Type：网格类型（Polygon模式无此属性）
                - FullRect：矩形；
                - Tight：紧凑的，根据Alpha通道生成Mesh；
            - Pivot：轴心，精灵内部坐标的原点；
        - Multiple：多图；
            - Mesh Type：网格类型（Polygon模式无此属性）
                - FullRect：矩形；
                - Tight：紧凑的，根据Alpha通道生成Mesh；
        - Polygon：多边形，在SpriteEditor里使用多边形裁剪精灵；
        - PackingTag：图集名称，指定所属图集；
        - PixelsPer Unit：每单位像素数，在世界场景中，每单位距离有多少个像素；
        - ExtrudeEdge：拉伸边缘，确定在生成的“网格”中的“精灵”周围保留多少区域；
- cookie：场景光的cookie

    ![5.png](http://ww1.sinaimg.cn/large/006zwgbUly1ggrnz4hhlqj30e9075wen.jpg)
    - Light Type：光照类型
        - Spotlight：聚光灯，形状必须为2D；
        - Directional：平行光，形状必须为2D；
        - Point：点光源，形状必须为立方体；
- 压缩设置

    ![6.png](http://ww1.sinaimg.cn/large/006zwgbUly1ggro1jfe8hj30e903f3yg.jpg)
    - Max Size：最大尺寸；
    - Compression：压缩质量；
    - Format：压缩格式；
    - Use Crunch Compression：紧凑压缩；
        - Compressor Quality：压缩质量
    - Resize Algorithm：调整纹理大小算法，当纹理大小超过最大尺寸时，用于缩小纹理的算法
        - Mitchell：Mitchell算法调整纹理的大小，这是默认的调整大小算法；
        - Bilinear：双线性调整纹理的大小，对于小而锐利的细节的图像，会保留比Mitchell更多的细节

#### 二、UI图导入处理

1、导入时处理纹理属性的考虑
    
- 加快打包时速度；
- 防止特殊手动修改后的属性在打包时被修改；
- 可以预先发现不符合格式的资源，以免打包时才发现，浪费时间；

2、导入设置

&ensp;&ensp;&ensp;&ensp;导入时分为两大类处理，一是UI精灵图片，一是普通贴图；

&ensp;&ensp;&ensp;&ensp;对于普通贴图，导入到unity内时，unity会默认设置ToNerest，这样会自动保证Android平台下图片被4整除，IOS平台下图片是2的整除次幂，所以默认情况下图片都可以得到最好的压缩，不用自己额外处理;

&ensp;&ensp;&ensp;&ensp;对于UI贴图，由于需要将Texture设置成Sprite那么它就不会ToNearest了，默认情况下不能保证最优设置，需要自行处理

- UI图片压缩策略
    - Android下优先设置图片规则才用ETC+Crunched的格式，如果图片无法被4整除在设置ASTC格式，如果安卓手机不支持ASTC则会fall back到RGBA32
    - IOS下优先设置图片规则采用PVRTC4，如果图片不是2的整数次幂在设置ASTC格式，如果不支持fall back到RGBA32
- 详细配置
    - OnPreprocessTexture 导入前处理
        - 设置最大尺寸
            ```c#
                TextureImporterPlatformSettings setting = importer.GetDefaultPlatformTextureSettings();
                if (setting.maxTextureSize > 1024) Debug.LogWarningFormat("{0} 大小超过1024，已默认修改为1024，请自行检查，如需要请手动更改大小", assetPath);
                setting.maxTextureSize = DefineSpritePacker.kDefaultMaxSprite;
                importer.SetPlatformTextureSettings(setting);
            ```
        - 设置为精灵 importer.textureType = TextureImporterType.Sprite; 
        - 设置为单图 importer.spriteImportMode = SpriteImportMode.Single;
        - 设置为循环 importer.wrapMode = TextureWrapMode.Clamp;
        - 设置纹理拉伸时过滤模式 importer.filterMode = FilterMode.Bilinear; 
        - 设置每单位像素数 importer.spritePixelsPerUnit = 100; 
        - 根据图片所在文件夹设置图集名称 importer.spritePackingTag = "xxx"；
        - 关闭mip maps importer.mipmapEnabled = false;
        - 关闭读写 importer.isReadable = false;
        - 透明通道设置 importer.alphaIsTransparency = importer.DoesSourceTextureHaveAlpha();
    - OnPostprocessTexture 导入后处理
        - 处理ios模式压缩数据
            ```c#
                TextureImporterPlatformSettings isettings = importer.GetPlatformTextureSettings("iPhone");
                isettings.overridden = true;
                isettings.maxTextureSize = setting.maxTextureSize;
                bool isPowerOfTwo = IsPowerOfTwo(texture);
                TextureImporterFormat defaultAlpha = isPowerOfTwo ? TextureImporterFormat.PVRTC_RGBA4 : TextureImporterFormat.ASTC_RGBA_4x4;
                TextureImporterFormat defaultNotAlpha = isPowerOfTwo ? TextureImporterFormat.PVRTC_RGB4 : TextureImporterFormat.ASTC_RGB_6x6;
                isettings.format = importer.DoesSourceTextureHaveAlpha() ? defaultAlpha : defaultNotAlpha;
                importer.SetPlatformTextureSettings(isettings);
            ```
        - 处理安卓模式压缩数据
            ```c#
                TextureImporterPlatformSettings asettings = importer.GetPlatformTextureSettings("Android");
                asettings.overridden = true;
                asettings.allowsAlphaSplitting = false;
                asettings.maxTextureSize = setting.maxTextureSize;
                bool divisible4 = IsDivisibleOf4(texture);
                defaultAlpha = divisible4 ? TextureImporterFormat.ETC2_RGBA8Crunched : TextureImporterFormat.ASTC_RGBA_4x4;
                defaultNotAlpha = divisible4 ? TextureImporterFormat.ETC_RGB4Crunched : TextureImporterFormat.ASTC_RGB_6x6;
                asettings.format = importer.DoesSourceTextureHaveAlpha() ? defaultAlpha : defaultNotAlpha;
                importer.SetPlatformTextureSettings(asettings);
            ```
            
#### 三、完整代码

``` c#
using System.Reflection;
using UnityEditor;
using UnityEngine;

public class ImportTexture : AssetPostprocessor
{
    public override uint GetVersion() { return 1; }
    static bool isForceImport = false;
    static string uiPath = ResDefine.GetResRelaPath(ResType.resPicture);

    //资源变化时处理
    static void OnPostprocessAllAssets(string[] importedAssets, string[] deletedAssets, string[] movedAssets, string[] movedFromAssetPaths)
    {
        isForceImport = false;

        //移动资源的处理
        foreach (string str in movedAssets)
        {
            if (str.Contains(uiPath))
            {
                isForceImport = true;

                // 如果是移动到指定UI目录下，需要重新Import
                AssetDatabase.ImportAsset(str);
            }
        }
    }

    /// <summary>
    /// 图片导入前处理
    /// </summary>
    void OnPreprocessTexture()
    {
        if(assetPath.EndsWith(".dds"))
        {
            Debug.LogError("禁止使用dds格式图片，请检查：" + assetPath);
            return;
        }

        TextureImporter importer = assetImporter as TextureImporter;
        if (importer.userData != GetVersion().ToString() || isForceImport)
        {
            TextureImporterPlatformSettings setting = importer.GetDefaultPlatformTextureSettings();
            if (setting.maxTextureSize > 1024) Debug.LogWarningFormat("{0} 大小超过1024，已默认修改为1024，请自行检查，如需要请手动更改大小", assetPath);
            setting.maxTextureSize = DefineSpritePacker.kDefaultMaxSprite;
            importer.SetPlatformTextureSettings(setting);


            //此处只对UI图片进行处理，其他类型图片unity会默认设置ToNerest，默认情况下会得到最好的压缩
            if (assetPath.Contains(uiPath))
            {
                string spritrName = "";
                spritrName = assetPath.Replace("\\", "/");
                int _position = spritrName.LastIndexOf(@"/");
                spritrName = spritrName.Substring(0, _position);
                spritrName = spritrName.Replace("/", "_");
                spritrName.ToUpper();

                importer.textureType = TextureImporterType.Sprite;                              //精灵
                importer.spriteImportMode = SpriteImportMode.Single;                            //单图
                importer.wrapMode = TextureWrapMode.Clamp;                                      //循环 采样模式
                importer.filterMode = FilterMode.Bilinear;                                      //纹理拉伸时过滤模式
                importer.spritePixelsPerUnit = 100;                                             //每单位像素数
                importer.spritePackingTag = spritrName;                                         //图集名称
                importer.mipmapEnabled = false;                                                 //mip maps
                importer.isReadable = false;                                                    //可读写属性
                importer.alphaIsTransparency = importer.DoesSourceTextureHaveAlpha();           //透明通道是否透明
            }
        }
    }

    /// <summary>
    /// 图片导入后处理
    /// </summary>
    /// <param name="texture"></param>
    void OnPostprocessTexture(Texture2D texture)
    {
        TextureImporter importer = assetImporter as TextureImporter;

        if (importer.userData != GetVersion().ToString())
        {
            importer.userData = GetVersion().ToString();

            if(assetPath.Contains(uiPath))
            {
                TextureImporterPlatformSettings setting = importer.GetDefaultPlatformTextureSettings();

                TextureImporterPlatformSettings isettings = importer.GetPlatformTextureSettings("iPhone");
                isettings.overridden = true;
                isettings.maxTextureSize = setting.maxTextureSize;
                bool isPowerOfTwo = IsPowerOfTwo(texture);
                TextureImporterFormat defaultAlpha = isPowerOfTwo ? TextureImporterFormat.PVRTC_RGBA4 : TextureImporterFormat.ASTC_RGBA_4x4;
                TextureImporterFormat defaultNotAlpha = isPowerOfTwo ? TextureImporterFormat.PVRTC_RGB4 : TextureImporterFormat.ASTC_RGB_6x6;
                isettings.format = importer.DoesSourceTextureHaveAlpha() ? defaultAlpha : defaultNotAlpha;
                importer.SetPlatformTextureSettings(isettings);

                TextureImporterPlatformSettings asettings = importer.GetPlatformTextureSettings("Android");
                asettings.overridden = true;
                asettings.allowsAlphaSplitting = false;
                asettings.maxTextureSize = setting.maxTextureSize;
                bool divisible4 = IsDivisibleOf4(texture);
                defaultAlpha = divisible4 ? TextureImporterFormat.ETC2_RGBA8Crunched : TextureImporterFormat.ASTC_RGBA_4x4;
                defaultNotAlpha = divisible4 ? TextureImporterFormat.ETC_RGB4Crunched : TextureImporterFormat.ASTC_RGB_6x6;
                asettings.format = importer.DoesSourceTextureHaveAlpha() ? defaultAlpha : defaultNotAlpha;
                importer.SetPlatformTextureSettings(asettings);

                importer.SaveAndReimport();
                EditorUtility.SetDirty(importer);
            }
        }
    }

    /// <summary>
    /// 是否2的次幂
    /// </summary>
    /// <param name="importer"></param>
    /// <returns></returns>
    bool IsPowerOfTwo(TextureImporter importer)
    {
        (int width, int height) = GetTextureImporterSize(importer);
        return (width == height) && (width > 0) && ((width & (width - 1)) == 0);
    }

    /// <summary>
    /// 是否2的次幂
    /// </summary>
    /// <param name="texture"></param>
    /// <returns></returns>
    bool IsPowerOfTwo(Texture2D texture)
    {
        return (texture.width == texture.height) && (texture.width > 0) && ((texture.width & (texture.width - 1)) == 0);
    }

    /// <summary>
    /// 是否被4整除
    /// </summary>
    /// <param name="importer"></param>
    /// <returns></returns>
    bool IsDivisibleOf4(TextureImporter importer)
    {
        (int width, int height) = GetTextureImporterSize(importer);
        return (width % 4 == 0 && height % 4 == 0);
    }

    /// <summary>
    /// 是否被4整除
    /// </summary>
    /// <param name="texture"></param>
    /// <returns></returns>
    bool IsDivisibleOf4(Texture2D texture)
    {
        return (texture.width % 4 == 0 && texture.height % 4 == 0);
    }

    /// <summary>
    /// 获取图片的尺寸 - 获取的是资源原始尺寸，不是unity内显示的
    /// </summary>
    /// <param name="importer"></param>
    /// <returns></returns>
    (int, int) GetTextureImporterSize(TextureImporter importer)
    {
        if (importer != null)
        {
            object[] args = new object[2];
            MethodInfo mi = typeof(TextureImporter).GetMethod("GetWidthAndHeight", BindingFlags.NonPublic | BindingFlags.Instance);
            mi.Invoke(importer, args);
            return ((int)args[0], (int)args[1]);
        }
        return (0, 0);
    }
```