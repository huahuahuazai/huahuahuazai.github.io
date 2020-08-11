---
layout: post
title:  "Unity-ShaderLab基础知识"
categories: unity
tags: ShderLab
author: FSH
---

* content
{:toc}

#### 一、shader概述

- 相关术语说明
    - GLSL：基于 OpenGL 的 OpenGL Shading Language;
    - HLSL：基于 DirectX 的 High Level Shading Language;
    - CG：NVIDIA 公司的 C for Graphic；
    - OpenGl：图形编程接口，依赖硬件，不依赖系统，可以跨平台，但取决于硬件开发商是否给与支持；
    - DX：图形编程接口，不依赖硬件，但其基本运行与微软自家的平台Windows,Xboxp平台；
    - CG：由NVIDIA公司和微软公司相互协作在标准硬件光照语言的语法和语义上达成了一致开发出来的，是GLSL,HLSL上层的语言，可根据需要编译为GLSL或者HLSL，真正意义上的跨平台。




    

- 什么是shader?
    - shader 是运行在GPU流水线上一些高度可编程阶段的程序;
    - shader 是一些特定类型的着色器，如顶点，片元着色器;
    - shader可以控制流水线中的渲染细节，例如用顶点着色器来进行顶点变换以及传递数据，用片元着色器来进行逐 像素渲染。
    
- 渲染流水线

    ![1.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt3fn1536j30mz09379a.jpg)
    
    - 应用阶段：由CPU负责，主要工作如下：
        - 准备场景数据
        
            ![4.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt3ngng58j30m207eq5d.jpg)
            
            - 将渲染所需数据从硬盘加载到内存中，网格纹理等数据加载到显存中;
        
        - 不可见剔除：把看不到的对象剔除
        - 设置渲染状态
        
            ![5.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt3pi3vbfj30d7080mzj.jpg)
            
            - 这些状态定义了场景中的网格是怎么被渲染的。例如，使用哪个顶点着色器， 哪个片元着色器，光源属性，材质等
            
        - 调用DrawCall
        
            ![6.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt3sznfrej30b206kgmg.jpg)
            
            - Draw Call是由CPU发起，GPU接收的一个命令，该命令指向一个需要被渲染的图元列表，不会包含任何的材质信息
            
    - GPU流水线
    
        &emsp;&emsp;**几何阶段和光栅化阶段**处于GPU流水线中，开发者无法拥有绝对的控制权，其实现的载体是Gpu。Gpu通过实现流水线化，大大加快了渲染速度。虽然我们无法完全控制这两个阶段的实现细节，但是Gpu向开发者开放了很多控制权
        
        ![7.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt3w37lu3j30uq07hgm8.jpg)
        
        - 顶点数据：应用阶段载入到显存中，并**由DrawCall指定**
        - 绿色流水线阶段**完全可编程控制**；
        - 黄色流水线阶段**只可配置不可编程控制**；
        - 蓝色流水线阶段**由GPU固定实现**；
        - 其中曲面细分着色器和几何着色器是可选的。
        
    - 几何阶段
        - 顶点着色器
        
            &emsp;&emsp;顶点着色器是GPU流水线第一部分，其输入来自CPU；其主要工作：**坐标变换和逐顶点光照**，此外还可以输出后续阶段所需要的数据
        
            &emsp;&emsp;每个顶点着色器都必须要完成把**顶点坐标从模型空间转换到齐次裁剪空间**
        
            - 1、模型变换：模型空间 ——>> 世界空间；
            - 2、观察变换：世界空间 ——>> 观察空间；
            - 3、投影变换：观察空间 ——>> 裁剪空间；
            
            ![8.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt4l1bolwj30cj04rwf0.jpg)
        
        - 裁剪
        
            根据图元与摄像机的关系进行裁切；有如下三种关系:
            
            - 1、完全在视野内，该图元继续传递给下一阶段；
            - 2、部分在视野内，该图元在视野外的部分将被裁剪掉；
            - 3、完全在视野外，该图元完全舍弃；
            
             ![9.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt4pfh19yj30mj05l0tj.jpg)
            
        - 屏幕映射
        
            &emsp;&emsp;屏幕映射的任务是将裁剪后的**齐次坐标（NDC）转换到屏幕坐标**系，屏幕坐标系是一个二维坐标系，最终结果受显示画面的分辨率影响。
            
            - 屏幕映射：裁剪空间 ——>> 屏幕空间（二维）
            
            ![10.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt4vbpkabj30hp05it9c.jpg)
            
            &emsp;&emsp;将齐次坐标下 -1，1的坐标范围转换到（x1,y1）,(x2,y2)。可以看到这个过程实际上就是一个缩放的过程。在这个处理种z轴不做处理。屏幕坐标系和z轴构成了窗口坐标系。这些值会被传到光栅化阶段
            
    - 光栅化阶段
    
        - 三角形设置
            - 三角形设置是光栅化的**第一个流水线阶段**，该阶段会根据上一阶段的顶点坐标**计算出三角形网格**
            
            ![11.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt511zchpj306407g74p.jpg)
            
        - 三角形遍历
            - 该阶段也被称为**扫描变换**，会检查每个像素是否被一个三角形网格覆盖，若覆盖则**生成一个片元**
            
            ![12.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt52gbb8jj305g06kwet.jpg)
            
        - 片元着色器
            - 片元着色器的输入是上一个阶段对顶点信息插值得到的结果，具体来说是根据那些从顶点着色器中输出的数据插值得到的。而其输出是一个或者多个颜色值
            - 这一阶段可以完成很多重要的渲染技术，其中最重要的技术之一就是纹理采样
            
            ![13.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt55lqpbqj30g208dq3l.jpg)
            
        - 逐片元操作
        
            逐片元操作是高度可配置的，该阶段主要任务：
            - 1、决定每个片元的可见性，涉及很多测试工作，例如深度测试，模板测试；
            - 2、如果一个片元通过了所有测试后，就需要把这个片元的颜色值和已经储存在颜色缓冲区的色彩进行合并，或者说混合。
            
            ![14.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt5ert09aj30ny01l0sq.jpg)
            
            - 模板测试
            
                ![15.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt5g9wbtfj30i104j3z3.jpg)
                
                - 如果开启了模板测试，GPU会首先读取模板缓冲区该片元位置的模板值，然后将该值和读取的参考值进行比较，比较函数可由开发者指定（小于，大于，等于），如果未通过测试，该片元将被舍弃，无论是否通过测试，开发者都可以根据模板测试结果和后面的深度测试结果来修改模板缓冲区的值
                
            - 深度测试
            
                ![16.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt5h2i3zij30i1050dgi.jpg)
                
                - 如果开启了深度测试，GPU会把该片元的深度值和已经存在于深度缓冲区的深度值进行比较，比较函数可由开发者指定（小于，大于，等于），如果未通过测试，该片元将被舍弃，其就没有权利修改深度缓冲区的值；如果通过了测试（或者未开启深度测试），开发者还是可以指定（根据是否开启深度写入）是否用这个片元的深度值来覆盖深度缓冲区原有的深度值
                
            - 混合
            
                ![17.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt5hfbxzij30i105sdgf.jpg)
                
                - 每个像素的颜色信息被保存在一个名为颜色缓冲区的地方，当我们执行本次渲染时，颜色缓冲区往往已经有了上次渲染之后的结果，需要通过混合处理来决定是否使用本次渲染得到的颜色覆盖掉之前的结果，或者其他一些处理；如果没有开启混合，则会直接使用本次渲染得到的颜色来更新颜色缓冲区的值；如果开启了混合，则根据混合结果来更新颜色缓冲区的值。
                - 对于不透明的物体，可以直接关闭混合；对于半透明的物体，则需要使用混合来时这个物体看起来是透明的
                
            &ensp;&ensp;&ensp;&ensp;以上顺序不是固定的，对于大多数GPU来说，会尽可能在片元着色器之前进行测试，以避免做无用功（计算了片元信息后而没有通过测试舍弃该片元的情况），浪费性能；
            
    - 补充
    
        ![18.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt6y8zr8mj30bo0ca0u8.jpg)
        
        ![19.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt6yhy77vj30d70b8wg0.jpg)
        
        ![40.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdtf69y358j30jx0cfq82.jpg)


#### 二、相关数学知识

&emsp;&emsp;较依赖数学知识，此处指做简单说明，不做详细分析，具体可自行学习，推荐书籍《3D数学基础：图形与游戏开发》,需要的知识如下：

- 坐标系：二维，三维坐标；
- 点和矢量：概念及运算；
- 矩阵：定义，行列矩阵，运算，特殊矩阵，矩阵的几核意义（变换）;
- 坐标空间：模型空间，世界空间，观察空间，裁剪空间，屏幕空间及其变换过程；
- 法线相关

#### 三、untiy - shader

- 说明
    - unity-shader并不是真正的shader，unity-shader是用ShaderLab语言来编写的，S**haderLab语言是unity封装的，其是GLSL,HLSL,CG的上层语言**，其内部真正的着色器代码还是**从属于CG语言**的，着色器代码嵌套在**CGPROGRAM和ENDCG**之间
    
    ![20.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7ap46faj30h507ymy9.jpg)
    
- 结构

    ![21.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7c1s4mkj308d09kwf3.jpg)
    
    - Properties-属性块
        - 定义的属性可在材质球面板看到并进行赋值；
        
        ![22.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7e23x6rj30jg07gtas.jpg)
        
        ![23.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7er84a1j30jk05ytbd.jpg)
        
    - RenderSetup-渲染模式
        - 配置渲染状态相关，例如：深度测试等等；
        
        ![24.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7gz1lhvj30hv067jrd.jpg)
        
        ![25.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7h895m3j30j8041q4j.jpg)
        
    - SubShader-tags设置
        - 配置shder的一些渲染属性
        
        ![26.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7iuo6kmj30bs06cgmt.jpg)
        
        ![27.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7j5vjrzj30eq092gps.jpg)
        
    - Pass-tags设置
        - 设置pass通道的一些渲染属性，大多数与SubShader-tags设置相同，有一些pass独有的设置，当**SubShader-tags与Pass-tags同时存在一个相同设置时，有限使用SubShader-tags中的设置**
        
        ![29.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7mfv56aj30i903tq39.jpg)
        
        ![30.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7mp9h9vj30i902wmxe.jpg)
        
- 语义简介
    - 顶点着色器输入语义

        ![41.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdupxz44xgj30in056jts.jpg)
        
    - 顶点着色器输出语义
    
        ![42.png](http://ww1.sinaimg.cn/large/006zwgbUly1gduq1umrkpj30it04emzb.jpg)
        
    - 片元着色器输出语义
    
        ![43.png](http://ww1.sinaimg.cn/large/006zwgbUly1gduq3qbxr4j30iu01w74v.jpg)
        
- 一些简单公式
    - 漫反射公式
        - 兰伯特定律（Lambert's law）反射光线的强度与表面法线和光源方向间的夹角	的余弦值成正比
        
            ![31.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7pabqbxj30cb017glf.jpg)
            - clight：光源颜色
            - mdiffuse ： 材质漫反射颜色
    		- n：表面法线
    		- l：光源方向单位矢量
    		
    	- 半兰伯特光照模型：基本复核兰伯特定律，做了稍微修改
    	
    	    ![32.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7qoqve8j30cw01agli.jpg)
    	    
    	    - 未限制n与l点积不为负，而是进行一个a倍的缩放并加上一个β的偏移，把n与l点积从[-1,1]映射到[0,1]
    	    
    - 高光
        - 普通模型
        
            ![34.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7sm2fktj30i501kdft.jpg)
            - clight ：光源颜色
            - mspecular ： 高光反射系数
        	- v：视角方向
        	- r：反射方向 - CG内置函数 reflect(i,n) i :光照方向 n：法线
            - mgloss 次方
        
        - Blinn-PHong光照模型：不使用反射方向，使用一个新的矢量H与法线点乘
            
            ![35.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7vgsofaj30js02274d.jpg)
            
            - 计算H：归一化（光照方向与视角方向的和）；v:视角方向；i:光照方向
            
            ![36.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdt7vn0zxdj305h02t745.jpg)