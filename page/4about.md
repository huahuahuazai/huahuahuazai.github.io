---
layout: page
title: About me
permalink: /about/
icon: heart
type: page
---

* content
{:toc}

## 关于我

> ![Snipaste_2020-03-21_18-31-38.png](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qage8g5j30f905ejsw.jpg)

* 语言

    * 熟练：C#，Lua
    * 了解：Python，C++，shaderLab

* 游戏引擎及开发工具

    * 熟练：Unity，Visual Studio，Visual Code，SubLime，SVN，GIT
    * 了解：Android Studio，LayaAir2，Jenkins

* Unity相关

    * 参与的正式项目：奇迹手游（MMO，未上线目前测试中），3D麻将（仅上线OPPO渠道），3D五子棋（版号申请中）
    * 一些个荣誉：2018年盛和年度优秀员工；2019年开启优秀新人
    * UI：NGUI，UGUI，图集处理，draw call优化，over draw优化，ui与模型及ui与特效层级管理
    * 热更框架：tolua
    * 资源：资源分类整理，ab打包策略及加载流程，脚本自动化打包
    * 编辑器：工具拓展（我做过的：模型处理工具，prefab检查工具，UI处理工具，图片设置工具，自动打包工具）
    * 动作方面：animator，animation都做过，由于时间紧且无需求并未深入研究

* 之后想做的事

    * shderLab深入提升
    * 接触和研究框架
    * 接触和研究网络通信

* 一些兴趣

    * Linux，撰写博客，学习视频剪辑，玩游戏（只喜欢主机游戏，不玩网游）


## 工作经历

* **2019.8.26-至今**
    * 公司：杭州开启网络有限公司
    * 职能：unity3d 前端
    * 项目简介：
        * 3D五子棋
            * 时间：2020.1.6 - 2020.2.26；
            * 目前状态：开发完成，版号申请中；
            * 人员构成：一产品，三开发（一个后端，两个前端），一测试，我和另一个u3d程序负责开发，我主要负责游戏逻辑层处理，另一个负责底层；
            * 简介：unity版本2018.4.10，NGUI系统，tolua热更框架，Animator动作系统，unity + Android Studio + Jenkins 自动出包。
            * 我的主要工作

                ① UI界面搭建及业务逻辑；

                ② 处理棋子及场景材质；

                ③ 与美术对接，规范资源格式，把控模型及场景效果；

                ④ 模型及场景动画系统；

                ⑤ 人物换装系统；

                ⑥ UI界面优化：Draw Call , Over Draw, 界面互斥处理，UI界面与模型与特效之间层级管理；

                ⑦ 资源：模型，图片，音频，动作资源导入处理及优化工具；

                ⑧ 打包：项目打包是unity导出安卓包到Android Studio内进行编译，导出我做了自动化脚本，AS内编译为公司统一流程。
            * 一些效果图

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1ql4egrwj31o00u0jv3.jpg)

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qlkcse5j31o00u077r.jpg)

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qluuhhkj31o00u00w5.jpg)

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qm493fxj31o00u0n0p.jpg)

        * 3D麻将
            * 时间：2019.8.26 – 2019.12.30
            * 目前状态：开发完成，已上线（3D版仅OPPO渠道），可在OPPO商店下载游玩。
            * 人员构成：一产品，四开发（一个后端，两个前端，一个SDK），一测试，U3D程序同五子棋项目
            * 说明：这个项目是这个公司的第一个3D项目，后续3D项目用的很多东西均是在这个项目中进行开发和架构的，我的很多工作也主要体现在这个项目中，与之后的项目（上面五子棋介绍的工作内容）中的工作项目基本雷同，除了一些针对游戏的细节处理，就不再做说明了
            * 一些效果图

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qmc3wu1j31o00u0q6l.jpg)

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qmmmx1bj31o00u0tc3.jpg)

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qmuxuraj31o00u0djq.jpg)

                ![](http://ww1.sinaimg.cn/large/006zwgbUly1gd1qn2v35hj31o00u0djv.jpg)

* **2017.9-2019.8.20**
    * 公司：杭州盛和网络科技有限公司
    * 职能：unity3d 前端
    * 项目简介

        * 奇迹类大型多人在线 MMO 3D 手游项目，基于unity5.6.4的可以小包发布的一个完整的项目（目前已测试3次，第四次测试准备中）；
        * 使用SVN进行版本控制；
        * 使用tolua热更；
        * 使用jenkins自动出包。

    * 我的具体工作内容

        &emsp;&emsp;根据策划需求，实现相关系统；处理模型，特效资源；提供相关自动化处理工具；UI方面的优化；一些第三方插件的导入与应用；部分游戏核心逻辑的实现，下面详细说明。

        * 基础UI系统

            角色面板(属性，加点，改名)，果实，每日签到，活动预告，排行榜，经验加成，魔法书，红包，图鉴，好友赠送，跨服天梯，头衔。
        * 充值相关系统

            充值，VIP，月卡，投资，充值活动，开服活动。
        * 交互系统

            采集，NPC好感度，BOSS宝箱，BOSS刷新，Boss伤害列表，智能NPC。
        * 综合系统
            红点系统，新手引导，UI动效，系统开启，主界面布局,通用底板。

            **还有一些其他小的系统就不记录在这里了，下面简单贴几张图**

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmi31xvtj30pg0eh0vm.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmigiuf8j30o40dz75c.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmipa0inj30oa0djabo.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmj56y2nj30np0d575n.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmjfk4vcj30nt0cn762.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmjnd9byj30nq0cuwfu.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmjwcvs9j30nr0cz0u6.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmk4zhb4j30pg0ezabc.jpg)

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmkc7bpyj30af07oweu.jpg)
            
            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lmkiw6x0j308e06kwep.jpg)

        * UI优化

            主要优化项目里的UI：drowcall,overcall，图集；ui与特效模型间的层级关系。
            * drowcall,overcall可以看我的[Unity 中的 DrawCall](https://fanshaohua.top/2019/07/22/DrowCall-for-unity/)
            * 图集的管理可以看我的[Unity 图集的理解与使用](https://fanshaohua.top/2019/07/22/Unity-sprite-packer/)
            * ui与特效，模型间的层级可以看我的[UI与特效模型间的层级理解](https://fanshaohua.top/2019/07/28/Unity-ui-level/)

        * 使用的第三方工具
            * TextMesh Pro 字体组件；
            * SimpleLod  减面工具；
            * UberLogger log控制台，手机可用（非SDK包开启）；
            * DoTween 动效；
            * AseetBundleView 查看asset bundle（unity内部看）；
            * UnityStudio 查看asset bundle（解包看）；
            * LitJson。
        * 自己写的一些辅助工具
            * 模型处理工具
                * 添加需要的节点：root,middle,head,lod,shadow;
                * 添加生成box;
                * 添加animation,绑定动画；
                * 修改layer；
                * 检查材质，shader。

                ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5ln9w4ijej30b207zq33.jpg)

                ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lnabgvv8j30ho0faaay.jpg)

            * 预制检查工具
                * 检查预制材质（剔除使用morenshader）；
                * 检查使用了特效的预制（预制经常改，5.6存在预制嵌套的问题）
                * 删除空的animator;
                * 取消射线检测。

                ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lnbb4vw3j30bi08qt8t.jpg)

            * UI处理工具（主要服务于drowcall的优化）
                * 设置UI所有子节点Z为零；
                * 检查UI坐标Z大于0的节点；
                * 检查UI使用了canvas的节点；
                * 显示UI锁使用到的图集。

                ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lnguhg9jj30b408ft8r.jpg)

            * 图片检查工具
                * 检查并替换UI中的白图，避免打开界面是闪一下；
                * 批量压缩还原图片，是要用于缩减首包体积；
                * 批量检查设置图片图集；
                * 批量检查处理大于1024的图片，并压缩为最大1024。

                ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lnnjrhjjj30ac0433yf.jpg)

            * 动作处理工具
                * 批量提取动作；
                * 批量附加动作；
                * 按规定处理动作；

                ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lnph2nu5j30a30163ya.jpg)

                * 检查没有被使用的图片

                ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5lnr14eohj307c01ljr7.jpg)

        * 附一个做引导时的流程图（具体实现要根据具体项目，这里只介绍下流程）

            ![](http://ww1.sinaimg.cn/large/005OSXbNly1g5mbr6hletj30kg0oidhb.jpg)

        * 红点概要
            * 红点的话最好做在客户端，与所使用的框架关系较大，最好每个系统都有管理类，在管理类中处理数据，判定红点统一进行实践分发；但是也有一部分数据客户端提前是不知道，需要后端进行处理与分发；
            * 表现方面，最好实现个基类，每个实现类继承和绑定红点对象，注册方法来进行显示；
            * 总的来说，这个系统是个比较麻烦且不能自动化配置的系统，因为每个界面做的都不一样，但是只要基础写好，只需要在做界面时绑定信息，添加红点ID即可。

* **2017.4-2017.8 进入游戏行业**
    * 公司：郑州游戏啦网络有限公司
    * 职能：根据策划的需求，对美术给的资源进行拼接，做成合适的游戏界面。
    * 总结

        &emsp;&emsp;在这里拼了一段时间的UI之后，我就开始不满足于拼UI了啊（我想写逻辑啊），但是在这里是两块是分开的，有大把的程序员，我并没有机会，但是我不嫩够满足与现状啊，我就开始想办法写逻辑。

        &emsp;&emsp;此时拼UI对我来说只是小菜一碟了，有了空闲的时间，但问题是我并没有代码的权限，于是我就买了一份网上的课程自己学习，期间跟着做了一些小游戏案列，对Unity的开发有了一个系统额的完整的认知后我就更不满足于拼UI了，然后我多次尝试沟通去写逻辑，但一直没有成功，在另一个契机下，我来到了杭州，进入到下一家游戏公司，真正意义上开始了我的程序员之路。
    * 期间自学做过的案例
        * 打砖块

            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g5j8l58l3hj30ey09naa2.jpg)

            * 学习使用了Rigidbody（钢体）组件；
            * unity 的基本操作与使用。
        * 见缝插针

            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g5j8n0bi43j30at0bhdfp.jpg)

            * 学习了unity的依稀而基本组件transform的相关API与使用；
            * UGUI的理解与使用。
        * 坦克大战

            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g5j8o5uh6hj30h3090js4.jpg)

            * 学习使用Collision(碰撞器)；
            * 学习使用Trigger(触发器)；
            * 复习使用Rigidbody(钢体)；
            * 复习使用UGUI；
            * 针动画的学习与使用；
            * 简单打包的学习与使用；
            * 学习2D游戏的制作。
        * UGUI背包系统

            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g5j8rekc6wj30fa0b5mxd.jpg)

            * 综合学习UGUI系统；
            * UGUI相关的事件(点击，拖拽等)；
            * layout element等自动排列组件；
            * UI的层级遮挡与mask处理；
            * UI的自适应处理(锚点与中心点)；
            * 接触学习简单的UI系统；
            * 数据与表现的结合与操作。
        * 武士
            
            ![](http://ww1.sinaimg.cn/large/006zwgbUly1g5j9gxeh9ej30j30dwq4b.jpg)

            * CharacterController(角色控制器)的学习；
            * animation动画系统的学习；
            * PlayMaker插件的学习与使用；
            * 触发器控制创建敌人的学习；
            * AudioSource(声音)的使用学习；
            * MVC框架的接触与学习；
            * Dotween的学习与使用；
            * 对象池的使用；
            * Particle system(粒子特效的学习与使用)。

    &emsp;&emsp;**这段时间，总的来说就是克隆式的学习，并没有自己深入的研究和处理一些东西，但也为不是科班的自己积累了一定的基础经验，为之后生活和工作提供了很多帮助。**


## Comments

{% include comments.html %}
