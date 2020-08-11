---
layout: post
title: "unity C# 单例模式总结"
categories: unity C#
tags: script
author: FSH
---

* content
{:toc}


#### 一、什么是单例模式

&ensp;&ensp;&ensp;&ensp;单例模式是最简单的设计模式之一，这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建，并提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

- 注意：
    - 单例模式只有一个实例；
    - 单例类必须自己创建自己的唯一实例；
    - 单例类必须给所有其他对象提供这一实例；
    - 单例模式在多线程中需要小心使用，否则可能创建多个实例，违反单例唯一实例的原则；

- 应用场景：
    - 需要频繁实例化然后销毁的对象；
    - 创建时耗时过多但又经常用到的对象；
    - 一些管理类对象；






#### 二、单例之懒汉式与饿汉式

1、饿汉式

&ensp;&ensp;&ensp;&ensp;饿汉式单例是在类加载时就生成静态实例

```c#
    public class HungrySingLeton
    {
        private static HungrySingLeton instance;
        
        private HungrySingLeton()
        {
            instance = new HungrySingLeton();
        }
        
        public static HungrySingLeton Instace
        {
            get{return instance;}
        }
    }
```


- 优点：
    - 简单方便
    - 线程安全；
    - 在类加载时就创建一个实例对象，调用时反应速度快；

- 缺点:
    - 单例类实例是在类加载时创建的，会降低启动速度；
    - 不管程序中是否使用该单例对象，都会创建该单例对象；

- 应用场景：单例对象功能简单，占用内存小，需要频繁使用；

2、懒汉式 - 普通懒汉式
    
&ensp;&ensp;&ensp;&ensp;懒汉式单例是第一次调用时创建实例

```c#
    public class LazySingleton
    {
        private static LazySingleton _instance;
        
        public static LazySingleton Instace
        {
            get
            {
                if (_instance == null)
                {
                    _instance = new LazySingleton();
                }
                return _instance;
            }
        }
    }
```

- 优点：
    - 在需要时创建，利用率高；
    - 提高启动速度；

- 缺点:
    - 多线程不安全，可能会创建多个实例；

- 应用场景：单例对象功能复杂，内存占用大，需要快速启动；

3、懒汉式 - 加锁懒汉式

&ensp;&ensp;&ensp;&ensp;解决普通懒汉式线程不安全的问题

```c#
    public class LazySingleton
    {
        private static LazySingleton _instace;
        private static readonly object lockObj = new object();
        
        public static LazySingleton Instance
        {
            get
            {
                lock(lockObj)
                {
                    if (_instace == null)
                    {
                        _instace = new LazySingleton();
                    }
                }
                return _instace;
            }
        }
    }
```

4、懒汉式 - 加锁双重判断懒汉式

&ensp;&ensp;&ensp;&ensp;加锁的懒汉式虽然解决了线程安全的问题，但并不是每次都需要加锁，调用时如果对象已经存在直接返回即可，不需要再加锁。

```c#
    public class LazySingleton
    {
        private static LazySingleton _instace;
        private static readonly object lockObj = new object();
        
        public static LazySingleton Instance
        {
            get
            {
                if (_instace == null)
                {
                    lock(lockObj)
                    {
                        if (_instace == null)
                        {
                            _instace = new LazySingleton();
                        }
                    }
                }
                return _instace;
            }
        }
    }
```

#### 三、以泛型方式实现单例基类

1、不继承mono的单例泛型基类

```c#
    public class Singleton<T> where T : class, new()
    {
        private static T instance;
        private static readonly object lockObj = new object();

        public static T Instance
        {
            get
            {
                if (instance == null)
                {
                    lock(lockObj)
                    {
                        if (instance == null) instance = new T();
                    }
                }
                return instance;
            }
        }
    }
```

2、继承mono的单例类

```c#
    using UnityEngine;

    public class MonoSingleton<T> : MonoBehaviour where T : MonoSingleton<T>
    {
        private static T instance;
        public static T Instance
        {
            get{return instance;}
        }
        
        protected virtual void Awake()
        {
            if (instance == null)
            {
                instance = (T)this;
            }
            else
            {
                Debug.LogError("Get a second instance of this class：" + this.GetType());
            }
        }
    }
```