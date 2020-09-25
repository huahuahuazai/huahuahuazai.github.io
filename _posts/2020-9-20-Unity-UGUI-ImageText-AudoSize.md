---
layout: post
title: "unity ugui 图片 文本 大小完美自动扩展"
categories: unity ugui
tags: Plugins
author: FSH
---

* content
{:toc}


&ensp;&ensp;&ensp;&ensp;unity开发中总是会用到文本框大小自动扩展，图片和文本自动扩展，之前一直没有找到很好的解决方案，以至于用了很久自己计算的笨方法，一点都不方便；最近下定决心搞定他，本来想自己封装个较完善的方法，但是研究了UGUI后发现，unity已经帮我们做好了，只是一直不会用，汗颜。。。

&ensp;&ensp;&ensp;&ensp;下面我就来把我的理解说下，有不足的地方欢迎大家指出，一起学习进步。






#### 一、自动扩展文本的大小

自动扩展文本大小比较简单，使用Content Size Fitter组件即可，

>![1.png](http://ww1.sinaimg.cn/large/006zwgbUly1gj2zkve3erj30d0046t90.jpg)

- Content Size Fitter属性
    - Horizontal Fit:控制横向；
    - Vectical Fit：纵向控制；
        - Unconstrained：不进行控制；
        - Min Size：限制为内容最小值；
        - Preferred Size：自动调整内容的大小；

#### 二、图文大小自动扩展

&ensp;&ensp;&ensp;&ensp;文本大小的自动扩展较简单，但是游戏内文本总是有背景的，在文本自动变化的同时，背景图片的大小也应该跟随文本一起变化；

&ensp;&ensp;&ensp;&ensp;此时不能单独对文本使用Content Size Fitter了，而是需要把文本放在图片节点下，对节点使用Content Size Fitter，但是单Content Size Fitter并不会有作用，还需要配合Vertical(Horizontal) Layout Group组件来使用，具体如下：

- 创建image背景图；
- 添加Vertical(Horizontal) Layout Group组件；
- 勾选Child Controls Size(子物体大小控制);
    > ![2.png](http://ww1.sinaimg.cn/large/006zwgbUly1gj301m5tsyj30i703zglt.jpg)
- 添加Content Size Fitter组件，根据需要选择横纵向控制
    > ![3.png](http://ww1.sinaimg.cn/large/006zwgbUly1gj303msxruj30cv04w3ym.jpg)
- 在背景节点下添加文本
    >![4.png](http://ww1.sinaimg.cn/large/006zwgbUly1gj304cxlzij306n01fwea.jpg)
- 根据效果调整Layout Group的边距控制
    >![5.png](http://ww1.sinaimg.cn/large/006zwgbUly1gj306skztgj30he04w0t1.jpg)
- 此时文本和图片即可同步自动扩展大小了
    >![imgtextaudosize.gif](http://ww1.sinaimg.cn/large/006zwgbUly1gj30ijxwnhg30r60aywm8.gif)

**注意：Child Controls Size需要放到父节点，而不是文本上，配合layout group对子物体size的控制使用，而且这个组件用一个就好了！！**

#### 三、 我自己用到的一个效果

>![imgtextaudosize1.gif](http://ww1.sinaimg.cn/large/006zwgbUly1gj30pyz0ixg31110ie4qp.gif)

&ensp;&ensp;&ensp;&ensp;以前我做这样的效果每次都要写一部分代码，很不方便，现在用这种方式就简单多了，创建item，赋值文本就搞定了，完全通过上面的方法搞定，欢迎讨论。