---
layout: post
title:  "Unity 中的 DrawCall"
categories: unity
tags: drawcall
author: FSH
---

* content
{:toc}

# 摘要

* 文档分为5个部分：
    * drawcall的理解；
    * unity中的DrawCall合并；
    * 一些配置和工具；
    * 针对UI的合批分析；
    * UI拼接建议。






# DrawCall的理解

## 什么是drowcall？

drawcall是openGL的描绘次数,一个简单的openGL的绘图次序是：

`设置颜色→绘图方式→顶点座标→绘制→结束`

每帧都会重复以上的步骤,这就是一次draw call;
如果有两个model，那么需要(两次draw calls):

`设置颜色→绘图方式→顶点座标A→绘制→结束`

`设置颜色→绘图方式→顶点座标B→绘制→结束`

也就是说在openGl绘制前，如果色彩通道(color filter)，绘图方式(shader)，顶点（model）不同的情况下draw calls就会增加;
对openGl来说绘制参数（状态值）的变更要比绘制大量的顶点更耗费cpu。所谓高速绘图就是，在尽量不改变openGl状态值的情况下，用一次draw call完成所有绘制;
比如上面的例子： 

`设置颜色→绘图方式→顶点座标A＋顶点座标B→绘制→结束`

就要更加有效率;

## unity中的drowcall

unity（或者说基本所有图形引擎）生成一帧画面的处理过程大致可以这样简化描述：
引擎首先经过简单的可见性测试，确定摄像机可以看到的物体，然后把这些物体的顶点（包括本地位置、法线、UV等），索引（顶点如何组成三角形），变换（就是物体的位置、旋转、缩放、以及摄像机位置等），相关光源，纹理，渲染方式（由材质/Shader决定）等数据准备好，然后通知图形API或者就简单地看作是通知GPU开始绘制，GPU基于这些数据，经过一系列运算，在屏幕上画出成千上万的三角形，最终构成一幅图像。

在Unity中，每次引擎准备数据并通知GPU的过程称为一次Draw Call。这一过程是逐个物体进行的，对于每个物体，不只GPU的渲染，引擎重新设置材质/Shader也是一项非常耗时的操作。因此每帧的Draw Call次数是一项非常重要的性能指标，对于iOS来说应尽量控制在20次以内，这个值可以在编辑器的Statistic窗口看到。

[详见这篇文章](https://blog.csdn.net/sakyaer/article/details/44459881)

# unity中的DrawCall合并

* Unity内置了Draw Call Batching技术，主要目标就是在一次Draw Call中批量处理多个物体，只要物体的变换和材质相同，GPU就可以按完全相同的方式进行处理，即可以把它们放在一个Draw Call中。

* Unity中的Batching分为Dynamic Batching（动态批处理）和Static Batching（静态批处理）两种方式：
    * Dynamic Batching（动态批处理）

        如果动态物体共用着相同的材质，那么Unity会自动对这些物体进行批处理。动态批处理操作是自动完成的，并不需要你进行额外的操作

        **动态批处理注意事项：**

        1、批处理动态物体需要在每个顶点上进行一定的开销，所以动态批处理仅支持小于900顶点的网格物体。

        2、如果你的着色器使用顶点位置，法线和UV值三种属性，那么你只能批处理300顶点以下的物体；

        3、如果你的着色器需要使用顶点位置，法线，UV0，UV1和切向量，那你只能批处理180顶点以下的物体。

        4、使用不同材质的实例化物体（instance）将会导致批处理失败。

        5、拥有lightmap的物体含有额外（隐藏）的材质属性，比如：lightmap的偏移和缩放系数等。所以，拥有lightmap的物体将不会进行批处理（除非他们指向lightmap的同一部分）。

        6、多通道的shader会妨碍批处理操作。比如，几乎unity中所有的着色器在前向渲染中都支持多个光源，并为它们有效地开辟多个通道。
    * Static Batching（静态批处理）

        相对而言，静态批处理操作允许引擎对任意大小的几何物体进行批处理操作来降低绘制调用（只要这些物体不移动，并且拥有相同的材质）。因此，静态批处理比动态批处理更加有效，为了更好地使用静态批处理，需要明确指出哪些物体是静止的，并且在游戏中永远不会移动、旋转和缩放。

        需要在检测器（Inspector）中将Static复选框打勾:
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58ddp47ftj30fv05iglt.jpg)

        **注意：使用静态批处理操作需要额外的内存开销来储存合并后的几何数据。在静态批处理之前，如果一些物体共用了同样的几何数据，那么引擎会在编辑以及运行状态对每个物体创建一个几何数据的备份。这并不总是一个好的想法，因为有时候，你将不得不牺牲一点渲染性能来防止一些物体的静态批处理，从而保持较少的内存开销。**

    **说明：本文档主要为了优化UI界面drawcall,游戏中UI基本都是动态的，则主要针对unity的动态合批进行处理。**

    [详见: Unity3D性能优化之Draw Call Batching](https://www.cnblogs.com/lifesteven/p/3664750.html)

    [详见: Unity全面优化](https://www.jianshu.com/p/3b285110069f) 
    
# 一些配置和工具

* 打开图集模式：sprite packer 选择 always enabled
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58dmgqqjjj31fd0ktq8y.jpg)

* Drawcall数量查看：game窗口state运行时可以查看当前所有Drawcall数量
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58dnca4saj30bq06zt9n.jpg)

* 查看渲染顺序
    运行unity，Windows -> Frame Debugger。看到的一些image的_MainTex的信息为图集形式，而不是单张资源的形式。
    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58dp3qk63j30w80p9tgd.jpg)

* UI预制检查工具
    * 这里的工具是针对自己项目自制的，这里简要说下作用，之后的专门记录工具的文章里会详细说明。
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58dz2oncdj30cs0a53zz.jpg)
    * 该工具比较简单，主要有四个功能：
        * set z as zero:设置目标所有子节点为零（UI中Z坐标不为零的不能合批）；
        * find child z more zero:检查目标Z坐标大于零的子节点（有时为了达到一些UI效果，主要是遮挡关系，会需要修改Z值）,用于针对性调整Z值；
        * find child canvas:检查目标带有canvas组件的子节点（有时为了达到一些UI效果，会需要额外添加canvas），用于针对性调整canvas；
        * 显示目标图集名称：会列出UI对象所使用资源的图集，用于检查同一UI的图集使用情况
            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58ewtmiydj30560893ye.jpg)    ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58ex5p0pdj308g07vt8r.jpg)

# 针对UI的合批分析

* 材质
    * 图片需要同一图集的才可以Batching
        例如：下面俩图，使用同一图集，一个批次渲出，使用不同图集则两个批次。
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58hw3gp9xj30m208dt97.jpg)

    * 文字需要使用同一字体，并且材质球相同才可Batching
        例如：下面两个文本，分别使用不同字体；同一字体，相同材质；相同字体，不同材质，批次分别为：2次，1次；2次
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58hxrm2zvj30rx05g74n.jpg)

* 重叠
    * 当GPU在绘画的时候，在一块区域有多个一样或者不一样的控件。 如text和图片，图片和图片，凡是**物理上的叠加**，那么我们就称呼这个为叠加，我们可以通过Scene窗口调为overdraw就能看到UI的叠加情况，叠加越多的地方越红(overdraw越高，越耗电)。
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58i14vftjj30rs07xaag.jpg)

    * 叠加对合批影响
        * 图片和图片叠加时，若图集相同则不影响合批，否则会打断合批，例如：下面俩图分别使用相同图集和不同图集进行叠加，批次分别为1次，2次
            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58i3i2qrgj30pj05f3yw.jpg)

        * 文字和文字叠加，与图片叠加相同，例如：下面俩文本分别使用不同字体；相同字体，相同材质；相同字体，不同材质，批次分别为2次，1次，2次
            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58i53xinzj30q704paae.jpg)

    * 图片和文字，必定不可合批，无论叠加与否，例如：下面文字和图片，无论是否叠加，批次均为2
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58i6ocquwj30pt0550t4.jpg)

    * 两个本来可以合批的图片，如果中间叠加了字体，则合批会被打断，字体亦然。例如：下面本来可以合批的两个图片，若其中一张图片叠加了文字，则合批被打断
        ![](http://ww1.sinaimg.cn/large/006zwgbUly1g58i7pg9foj30pv06g0t5.jpg)

* 层级
    * ui控件在hierachy层级相同的则可以合批（同一图集，且没有被别的图集控件或者文字，图片叠加）；这里所谓层级包括:
        * Z轴的值是否为零，只有零的才可以合批;
        * 是否同一canvas,不同canvas不能合批;
        * mask必定会打断mask内部控件与其外部控件的合批。
    * 这个就不举例子了，具体的可以看下下面的补充（主要是严格这样做的话，会导致代码写的很丑陋，看自己取舍）

* 补充

    [摘自网络](http://www.jianshu.com/p/061e67308e5f)，本文末尾特殊处理方式用的就是这个理论

    如果有一个UI元素，它所占的屏幕范围内（通常是矩形），如果没有任何UI在它的底下，那么它的层级号就是0（最底下）；如果有一个UI在其底下且该UI可以和它Batch，那它的层级号与底下的UI层级一样；如果有一个UI在其底下但是无法与它Batch，那它的层级号为底下的UI的层级+1；如果有多个UI都在其下面，那么按前两种方式遍历计算所有的层级号，其中最大的那个作为自己的层级号。

    有了层级号之后，就要合并批次了，此时，Unity会将每一层的所有元素进行一个排序（按照材质、纹理等信息），合并掉可以Batch的元素成为一个批次，目前已知的排序规则是，Text组件会排在Image组件之前渲染，而同一类组件的情况下排序规则未知（好像并没什么规则）。经过以上排序，就可以得到一个有序的批次序列了。这时，Unity会再做一个优化，即如果相邻间的两个批次正好可以Batch的话就会进行Batch。

# UI拼接建议

* 图片资源导入时，应把该UI所需图片放置到同一文件夹，以打成一个图集，复用的图片应视情况统一打成一个图集，但要注意一个图集不易过大，注意你的图片不能放在Resources文件夹下面，Resources文件夹下的资源将不会被打入图集；
* 拼接时，尽量图文分开，文本在Hierachy视图里尽量都放在最下方（因为文本一般都是显示在其他UI上方的），不同字体的文本分开放不要交叉，自上而下同一图集的或使用同一资源的放在一起，注意不同资源的控件之间不要重叠，这个不必强求，根据实际情况分析调整即可；
* 很多时候复制过来的控件坐标会有变化，如无特殊需求，切记Z值设为零。（我们项目中目前有很多界面为了处理遮挡关系，调整了Z值），不为零的所有都不能合批的；
* 使用canvas，mask的部分不要重叠在原本可以合并的控件之间，否则会打断合批。
[详细说明](https://blog.csdn.net/weixin_39562322/article/details/87877596)

# 特殊处理方式

由于图集和图集一些堆叠关系，会破坏Hierachy里面由上至下的同图集合批的规则，但是，在一些Image和Text的下面垫上一些透明的控件，可以达到不破坏这种规则的目的，从而可以减少DrawCall。

例如：图中Image3和Image虽然是同一图集，但是没有合并，因为Image有堆叠，是放在另一个图集的图片btn控件上，btn是放在底板上的，而Image3是直接放在底板上的；此时如果在Image下垫一张图片，二者就可以合批了，但是垫的这张图片也会多一个批次，所以总的批次并没有减少，只有垫的图片和btn图集相同，才会减少drawcall，但是这样是会破坏效果的，所以这种方式需要综合考虑分析使用。

![](http://ww1.sinaimg.cn/large/006zwgbUly1g58im4xqxoj30rb04rq3i.jpg)
![](http://ww1.sinaimg.cn/large/006zwgbUly1g58in97e8oj30s309o0th.jpg)
![](http://ww1.sinaimg.cn/large/006zwgbUly1g58injfgntj30s609pq3l.jpg)