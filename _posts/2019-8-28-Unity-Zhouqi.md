---
layout: post
title:  "Unity生命周期"
categories: unity
tags: Editor
author: FSH
---

* content
{:toc}

#### 简介
&emsp;&emsp;生命周期也是消息机制，当满足条件时，unity引擎自动调用的函数

#### 流程
&emsp;&emsp;Awake() -> OnEnable() -> Start() -> FixedUpdate() -> Update() -> ateUpdate() -> OnGui() -> OnDisable() -> OnDistroy()






#### 详解

- 初始阶段

    > **Reset()**：当拖动脚本到组件栏的时候，还有物体被reset的时候会调用
    
    > **Awake()**：脚本挂载在物体上，物体载入时立即调用一次且只执行一次，常用于游戏开始前进行初始化（无论当前组件是否启用）
    
    > **OnEnable()**: 每当脚本对象启用时调用（可重复调用）
    
    > **Start()**：脚本挂载在物体上，物体载入且 脚本对象启用时被调1次。
    常用于数据或游戏逻辑初始化，执行时机晚于Awake
    
- 物理阶段

    > **FixedUpdate()**:脚本启用后，固定时间被调用，适用于对游戏对象做物理操作，跟物理相关的(设置更新频率：Edit -> Project Setting -> Time -> Fixed Timestep值，默认为0.02s)
    
    **特性：不受渲染帧的影响，渲染时间固定，受时间缩放Time.timeScale的影响**
    
    > **OnCollisionXXX碰撞**：挂载脚本的对象必须有**Rigidbody和Collider**，且和另一个Collider物体发生接触;
        
        OnCollisionEnter：开始碰撞 -> OnCollisionStay：碰撞中 ->  OnCollisionExit：碰撞结束
        
    
    > **OnTriggerXXX触发**：挂载脚本的对象必须有**Collider**，且和另一个Collider物体发生接触;
    
         OnTriggerEnter：开始触发 -> OnTriggerStay：触发中 ->  OnTriggerExit：触发结束
         
- 输入事件

    > **OnMouseEnter()**：鼠标移入到当前 Collider 时调用;

    > **OnMouseOver()**：鼠标经过当前 Collider 时调用;
    
    > **OnMouseExit()**：鼠标离开当前 Collider 时调用；
    
    > **OnMouseDown()**：鼠标按下当前 Collider 时调用；
    
    > **OnMouseUp()**：鼠标在当前 Collider 上抬起时调用。
    
- 游戏更新

    > **Update()**：每帧（约0.02s）更新，**特性：受渲染帧影响，机器性能越高，渲染帧间隔越短，时间不固定**；

    > **LateUpdate()**：在 Update 函数被调用后执行，适用于跟随逻辑。

- 结束阶段

    > **OnDisable()**：对象变为不可用或附属游戏对象非激活状态时此函数被调用;
    
    > **OnDestroy()**：当脚本销毁或附属的游戏对象被销毁时被调用；
    
    > **OnApplicationQuit()**：应用程序退出时被调用。

- 流程图
    
    > ![20200219103309215.png](http://ww1.sinaimg.cn/large/006zwgbUly1gdixyry7p6j30iw0r0tgk.jpg)