---
layout: post
title:  "Lua 元表理解"
categories: lua
tags: metatable
author: FSH
---

* content
{:toc}

# Lua元表

lua元表(Metatable)是lua提供给我们改变table行为的方法，每个行为关联了对应的元方法。
* 有两个很重要的函数来处理元表：
    * setmetatable(table,metatable): 对指定 table 设置元表(metatable)，如果元表(metatable)中存在 __metatable 键值，setmetatable 会失败；
    * getmetatable(table): 返回对象的元表(metatable)。






* 设置元表

    ``` lua
        mytable = {}                          -- 普通表 
        mymetatable = {}                      -- 元表
        setmetatable(mytable,mymetatable)     -- 把mymetatable设为mytable的元表 
        --也可写为一行
        mytable = setmetatable({},{})
        --返回对象的元表
        getmetatable(mytable)                 -- 返回mymetatable
    ```

# Lua元方法

函数  | 描述
------------- | -------------
__add  |  运算符 +
__sub  |  运算符 -
__mul  |  运算符 *
__ div  |  运算符 /
__mod  |  运算符 %
__unm  |  运算符 -（取反）
__concat  |  运算符 ..
__eq  |  运算符 ==
__lt  |  运算符 <
__le  |  运算符 <=
__call  |  当函数调用
__tostring  |  转化为字符串
__index  |  调用一个索引
__newindex  |  给一个索引赋值

* \_\_add

    使用元表定义Lua如何计算两个table的相加操作a+b。

    ``` lua
        local t1 = {1,2,3}
        local t2 = {4,5,6}
        setmetatable(t1,{__add = function(t1,t2)
            local temp = {}
            for k,v in pairs(t1) do
                table.insert(temp,v)
            end
            for k,v in pairs(t2) do
                table.insert(temp,v)
            end
            return temp
        end
        })
        local t3 = t1 + t2
        for k,v in pairs(t3) do
            print(v)
        end
        -- 输出为：1，2，3，4，5，6
    ```
**当Lua试图对两个表进行相加时，先检查两者之一是否有元表，之后检查是否有一个叫"__add"的字段，若找到，则调用对应的值。"__add"等即时字段，其对应的值（往往是一个函数或是table）就是"元方法"。**

    **sub, mul, div, mod, unm, concat, eq, lt, le与add雷同，这里就不做说明了**

* \_\_index

    \_\_index是 metatable 最常用的键，用来对表访问，\_\_index可以是一个函数也可是一个table。

    * \_\_index 为 table:

        当你通过键来访问 table 的时候，如果这个键没有值，那么Lua就会寻找该table的metatable（假定有metatable）中的\_\_index 键。如果\_\_index包含一个表，Lua会在表中查找相应的键。

        ``` lua
            local tarkey = {
                name = "__index为table",
            }
            local tempT = {}
            setmetatable(tempT,{__index = tarkey})
            print(tempT.name)
            -- 输出为 __index为table
        ```

    * \_\_index 为 函数:

        如果\_\_index包含一个函数的话，Lua就会调用那个函数，table和键会作为参数传递给函数。\_\_index 元方法查看表中元素是否存在，如果不存在，返回结果为 nil；如果存在则由 \_\_index 返回结果。

        ``` lua
            local mt = {
                name = "__index 为函数",
            }
            
            local tempT = {}
            print(tempT.name)
            -- 输出为 nil
            setmetatable(tempT,{__index = function (tab,key)
                if mt[key] then 
                    return mt[key]
                else
                    return "mt 中不存在 " .. key
                end
            end})
            print(tempT.name)
            -- 输出为 __index 为函数
            print(tempT.value)
            -- 输出为 mt 中不存在 value
        ```

    * 总结

        出处[菜鸟教程lua元表](https://www.runoob.com/lua/lua-metatables.html)
        * Lua 查找一个表元素时的规则，其实就是如下 3 个步骤:
            * 1、在表中查找，如果找到，返回该元素，找不到则继续；
            * 2、判断该表是否有元表，如果没有元表，返回 nil，有元表则继续；
            * 3、判断元表有没有 \_\_index 方法，如果 \_\_index 方法为 nil，则返回 nil；如果 \_\_index 方法是一个表，则重复 1、2、3；如果 \_\_index 方法是一个函数，则返回该函数的返回值。

* \_\_newindex

    \_\_newindex 元方法用来对表更新，当为table中一个不存在的key时，会去调用元表中的\_\_newindex元方法,而不进行赋值；如果为table中一个存在的key时，则会进行赋值，而不调用元方法 \_\_newindex。\_\_newindex可以是一个函数也可是一个table。

    * \_\_newindex 为table

        ``` lua 
            local mt = {}
            local tempT = {value = "当前值"}
            print(tempT.value)
            -- 输出为 当前值

            setmetatable(tempT,{__newindex = mt})
            tempT.newvalue = "新值1"
            print(tempT.newvalue,mt.newvalue)
            -- 输出为 nil , 新值1

            tempT.value = "新值2"
            print(tempT.value,mt.value)
            --输出为 新值2 , nil
        ```

    * \_\_newindex 为函数
    
        ``` lua
            local tempT = {
                name = "原始"
            }
            print(tempT.name)
            -- 输出为 原始

            setmetatable(tempT,{__newindex = function (t,key,value)
                rawset(t,key,"new " .. value)
            end})
            tempT.name = "new 原始"
            tempT.age = 10
            print(tempT.name,tempT.age)
            -- 输出为 new 原始 , new 10
        ```
        
    * rawget 和 rawset
        * **rawget可以让你直接获取到表中索引的实际值，而不通过元表的\_\_index元方法**
        * **rawset可以让你直接为表中索引的赋值，而不通过元表的\_\_newindex元方法**

* \_\_call

    \_\_call可以让table当做一个函数来使用

    ``` lua 
        local tempT = {1,2,3}
        -- tempT()
        -- 报错 attempt to call local 'tempT' (a table value) 
        -- 正常情况下一个table 不能这样使用

        setmetatable(tempT,{__call = function (t)
            for k,v in pairs(t) do
                print(v)
            end
        end})
        tempT()
        -- 输出 1，2，3
    ```

* \_\_tostring

    \_\_tostring用于修改表的输出行为

    ``` lua 
        local tempT = {1,2,3}
        print(tempT)
        -- 输出 table: 0x2565120

        setmetatable(tempT,{__tostring = function (t)
            local s = ""
            for k,v in pairs(t) do
                s = s .. v .. ","
            end
            return s
        end})
        print(tempT)
        -- 输出为 1，2，3，
    ```

# 结语

通过元表我们可以很好的简化我们的代码功能，所以了解 Lua 的元表，可以让我们写出更加简单优秀的 Lua 代码 ，可以在lua中实现面向对象编程。