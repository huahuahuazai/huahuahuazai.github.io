---
layout: post
title:  "C#命名规范学习"
categories: C#
tags: norm
author: FSH
---

* content
{:toc}

# 命名规则

* Pascal 规则（帕斯卡命名）

    每个单词开头的首字母大写（如：MyName）
* Camel 规则（驼峰命名）

    除第一个单词外的其余单词首字母大写（如：myName）
* Upper 规则（大写）
    仅用于一两个字符长的常量的缩写命名，超过三个字符长度应该应用Pascal规则（如：const A=...,const AB=...）






# 详细规则

* 类命名
    * 类命名应该为名词及名词短语，尽可能使用完整的词；
    * 使用Pascal规则；
    * 在适当的地方，使用复合单词命名派生的类，派生类名称的第二部分应为基类的名称

        如：MyName对于从名为Name的类派生的类是合适的名称。
* 接口命名
    * 接口名称应该为名词及名词短语或者描述其行为的形容词，尽可能使用完整的词；
    * 使用Pascal规则。
* 枚举命名
    * Enum的类型和值名称均使用Pascal规则；
    * 少用缩写；
    * 不要在Enum类型名称上使用后缀。
* 变量命名
    * 使用Camel规则；
    * 在简单的循环语句中计数器变量使用i,j,k,v。
* 方法命名
    * 使用Pascal规则；
    * 采用一致的动词/宾语或宾语/动词顺序；

        如：动词在前-->>WriteData;动词在后-->>DataWrite;推荐前者。
    * 不要在方法名中重复类的名称。
* 属性命名
    * 使用Pascal规则；
    * 名称应为名词及名词短语；
    * 对于bool类型的属性或者变量应使用Is(is)前缀。
* 集合命名
    * 使用Pascal规则；
    * 名称应为名词及名词短语；
    * 代表集合的类后面追加Collection,代表集合的变量后面追加List。
* 事件命名
    * 使用Pascal规则；
    * event handlers命名使用 EventHandler 后缀；
    * 两个参数分别使用 sender 及 e；
    * 事件参数使用EventArgs 后缀；
    * 事件命名使用语法时态反映其激发的状态，例如 Changed，Changing。
* 其他常用编码规则
    * 代码的缩进，使用Tab,不要用Space;
    * 局部变量的名称要有意义；
    * 所有的成员变量声明在类的顶端，用换行把它与方法隔开；
    * 把想死的内容放在一起，比如数据成员，属性，方法，事件等，并适当使用#region…#endregion。

# 命名规范表格

* 类相关

标识符 | 大小写 | 示例
------------- | ------------- | -------------
类/结构 | Pascal | AppDomain
枚举类型 | Pascal | ErrorLevel
枚举值 | Pascal | FatalError
事件 | Pascal | ValueChange
异常类 | Pascal | WebException 注意 总是以 Exception 后缀结尾
只读的静态字段 | Pascal | RedValue
接口 | Pascal | IDisposable 注意 总是以 I 前缀开始
集合 | Pascal | CustomerCollection / CustomerList
方法 | Pascal | ToString
命名空间 | Pascal | System.Drawing
参数 | Camel | typeName
属性 | Pascal | BackColor
受保护的实例字段 | Camel | redValue 注意 很少使用,属性优于使用受保护的实例字段
公共实例字段 | Pascal | RedValue 注意 很少使用,属性优于使用公共实例字段

* 前缀

类型 | 前缀 | 示例
------------- | ------------- | -------------
Array | arr | arrShoppingList
Boolean | bln | blnIsPostBack
Byte | byt | bytPixelValue
Char | chr | chrDelimiter
DateTime | dtm | dtmStartDate
Decimal | dec | decAverageHeight
Double | dbl | dblSizeofUniverse
Integer | int | intRowCounter
Long | lng | lngBillGatesIncome
Object | obj | objReturnValue
Short | shr | shrAverage
Single | sng | sngMaximum
String | str | strFirstName

单纯记录，方便需要时查找，[参考的博客](https://www.cnblogs.com/qq841231249/p/9267316.html)