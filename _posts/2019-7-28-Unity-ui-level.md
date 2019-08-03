---
layout: post
title:  "UI与特效模型间的层级理解"
categories: unity
tags: UI
author: FSH
---

* content
{:toc}

# unity的层级分类

* Camera

    Camera的depath值大的渲染在上面。

* Sorting Layer

    &emsp;&emsp;相同Camera的情况下，可以设置Sorting Layer来控制渲染顺序，可以在Edit->ProjectSettings->Tags&Layers中设置，设置过Sorting Layer后，其渲染是从下往上依次渲染的，也就是说Tags&Layers中，越下面的Sorting Layer越早渲染。
    ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5m9vckylrj30ao0att8u.jpg)






* sortingOrder

    &emsp;&emsp;在Camera和Sorting Layer都相同的情况下可以修改其sortingOrder来控制渲染顺序，sortingOrder是int值，值大的渲染在上面。

# 详细使用

* 不同相机的就不说了，主要设置depath，主要说下在同一相机下的层级处理，如UI界面之间的层级；在项目中UI与特效与模型之间的层级关系往往很让人头疼。

* 针对UI于模型之间的层级关系，一个终级的方法就是把模型用相机单独渲染成一张图片，然后在UI中使用这张图片，这样就能很完美的解决UI与模型之间的层级关系。

    如下图模型都是渲染成图片的，就可以当UI一样使用，一劳永逸。

    ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5mac20yl0j30li0bugn6.jpg)

* 针对UI不同Canvas于特效之间的层级关系，可以规划不同canvas的Sorting Layer，然后给特效绑定一个脚本，放在哪个canvas下就动态修改特效Render的Sorting Layer，这样就可以比较好的解决不同UI Canvas下的层级关系。
    * 附一份动态修改特效Sorting Layer的代码(使用批量工具添加到UI特效上即可)

        ``` c#
                private Renderer[] allRenderers;
                void Start ()
                {
                    allRenderers = GetComponentsInChildren<Renderer>(true);
                    //特效有Renderer就设置layer,因为Sorting Layer是Renderer的sortingLayerName属性。
                    if (allRenderers.Length > 0) ChangeLayer();
                }

                //特效父节点改变时重新计算layer
                void OnTransformParentChanged()
                {
                    ChangeLayer();
                }

                void ChangeLayer()
                {
                    //特效不存在时不执行
                    if (gameObject == null) return;
                    //这里要根据情况修改，测试项目里分了两个大类Canvas，每个下面又根据层级细分了一些canvas（pop,notice,base之类的）所以这里我总是要取到第二层的canvas属性对特效进行设置。
                    Canvas [] parentsCanvas = GetComponentsInParent<Canvas>(true);
                    if (allRenderers != null && parentsCanvas.Length > 0 && allRenderers.Length > 0)
                    {
                        int index = parentsCanvas.Length - 2 >= 0 ? parentsCanvas.Length - 2 : 0;
                        //处理特效下所有sortingLayerName
                        foreach (Renderer rend in allRenderers)
                        {
                            if (rend != null && rend.sortingLayerName != parentsCanvas[index].sortingLayerName)
                            {
                                rend.sortingLayerName = parentsCanvas[index].sortingLayerName;
                            }
                        }
                    }
                }
        ```

* 同一UI Canvas下的UI与特效的层级关系，就需要根据情况具体分析和调整sortingOrder；也可以从UI框架角度有效的解决，比如可以代码层面设置UI的级别处理UI的互斥关系，从而达到界面不会叠加很多显示；又或者需要根据情况合适的设置UI所属的Canvas。
