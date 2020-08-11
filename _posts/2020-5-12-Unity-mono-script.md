---
layout: post
title: "unity 各平台C++库的调用"
categories: unity
tags: c++
author: FSH
---

* content
{:toc}


#### 一、为什么要修改mono脚本创建模板

- unity创建脚本时默认添加的函数很多时候我们并不需要,每次创建出来再删除很繁琐；
    > ![1.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghmvvg1hs1j309807oaa6.jpg)




    

- 默认创建的脚本没有题头说明，后续自己加又不方便；修改后可自行添加题头说明；
    > ![2.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghmvy55ovaj30az038mx1.jpg)

#### 二、模板修改步骤

1、找到unity默认模板位置根据自己需要进行编辑或者替换

- 默认模板位置：Unity安装目录\Editor\Data\Resources\ScriptTemplates
    > ![3.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghmw203nywj30cm06ttab.jpg)

- 其中 81-C# Script-NewBehaviourScript.cs.txt为默认的mono脚本模板，修改或替换为自己需要的内容
    > ![4.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghmw417zc5j30ak06r0sq.jpg)

    > #SCRIPTNAME#会默认解析为脚本名称
    
2、模板解析

- Editor内创建解析脚本xxx.cs(此处实例创建为NewBehaviourTemplates.cs)

    ```c#
        using System;
        using System.IO;
        
        public class NewBehaviourTemplates : UnityEditor.AssetModificationProcessor
        {
            private static void OnWillCreateAsset(string path)
            {
                path = path.Replace(".meta", "");
                if (path.EndsWith(".cs"))
                {
                    string str = File.ReadAllText(path);
                    str = str.Replace("#CreateAuthor#", Environment.UserName).Replace(
                                      "#CreateTime#", string.Concat(DateTime.Now.Year, "/", DateTime.Now.Month, "/",
                                        DateTime.Now.Day, " ", DateTime.Now.Hour, ":", DateTime.Now.Minute, ":", DateTime.Now.Second));
                    File.WriteAllText(path, str);
                }
            }
        }
    ```
    
- 然后创建脚本就会自动设置默认格式 
    > ![5.png](http://ww1.sinaimg.cn/large/006zwgbUly1ghmwcnf7ikj30ag07at8t.jpg)
