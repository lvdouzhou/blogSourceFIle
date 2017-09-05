---
title: UML 类图学习笔记
date: 2017-9-5 17:18:29
categories: “UML”
tags:
    - UML
description: "UML"
---

平时看代码时，特别是复杂的 app 类和类之间的关系错综复杂，想要理清楚他们之间的关系，往往是非常烧脑的。不过通过
使用 UML 图就可以达到事半功倍的效果。

## UML 图
在 UML 图中，使用一个矩形加两根横线表示一个类。如下图所示

![图1: UML 图](https://raw.githubusercontent.com/lvdouzhou/blopPic/master/UML1.jpg)

- 第一行： 类名  
- 第二行:  属性
- 第三行： 方法
- \+ : public
- \- : private
- \# : protected


## 类的属性表示
**格式：可见性  名称 ：类型 [ = 缺省值]**

```
java : public int startIndex = -1

UML  : + startIndex :int  =-1  
```

## 类的方法表示

**格式：可见性  名称(参数列表) [ ： 返回类型]**

```
栗子 无参数：
    java : public void action ()
    UML  : + action()  :void
    
栗子 有参数：
    java : public void action1 (String params)
    UML  : + action1(String params)  :void
    
```

<!-- more -->
## 类之间的关系

### 关联关系

1. 单向关联

单向关联用一个带箭头的直线表示，表示每个教室都有学生 

![图2 ：单向关联](https://raw.githubusercontent.com/lvdouzhou/blopPic/master/UML10.jpg)


2. 双向关联 

双向关联就是双方各自持有对方类型的成员变量。在UML类图中，双向关联用一个不带箭头的直线表示。

![图3 ：双向关联](https://raw.githubusercontent.com/lvdouzhou/blopPic/d24b7f7a90240a98d888abdc2c1c91a8c3e30d9a/UML3.jpg)

3. 自关联

自关联在UML类图中用一个带有箭头且指向自身的直线表示。上图的意思就是Node类包含类型为Node的成员变量，也就是“自己包含自己”。

![图4 ：自关联](https://raw.githubusercontent.com/lvdouzhou/blopPic/d24b7f7a90240a98d888abdc2c1c91a8c3e30d9a/UML4.jpg)


### 聚合关系

![图5：聚合关系](https://raw.githubusercontent.com/lvdouzhou/blopPic/d24b7f7a90240a98d888abdc2c1c91a8c3e30d9a/UML5.jpg)

上图中的Car类与Engine类就是聚合关系（Car类中包含一个Engine类型的成员变量）。由上图我们可以看到，UML中聚合关系用带空心菱形和箭头的直线表示。聚合关系强调是“整体”包含“部分”，但是“部分”可以脱离“整体”而单独存在。比如上图中汽车包含了发动机，而发动机脱离了汽车也能单独存在。

### 组合关系

![图6：组合关系](https://raw.githubusercontent.com/lvdouzhou/blopPic/d24b7f7a90240a98d888abdc2c1c91a8c3e30d9a/UML6.jpg)

组合关系与聚合关系见得最大不同在于：这里的“部分”脱离了“整体”便不复存在。显然眼睛不能脱离人类而存在。


### 依赖关系

![图7：依赖关系](https://raw.githubusercontent.com/lvdouzhou/blopPic/d24b7f7a90240a98d888abdc2c1c91a8c3e30d9a/UML7.jpg)

在UML类图中，依赖关系用一条带有箭头的虚线表示。



### 继承关系 

![图8：继承关系](https://raw.githubusercontent.com/lvdouzhou/blopPic/d24b7f7a90240a98d888abdc2c1c91a8c3e30d9a/UML8.jpg)

继承关系对应的是extend关键字，在UML类图中用带空心三角形的直线表示。


### 接口关系 

![图9：接口关系](https://raw.githubusercontent.com/lvdouzhou/blopPic/d24b7f7a90240a98d888abdc2c1c91a8c3e30d9a/UML9.jpg)

这种关系对应implement关键字，在UML类图中用带空心三角形的虚线表示。


到这里我们就能基本看懂和绘制 UML 图啦。

[欢迎移步到小弟的 Blog，有更多意想不到的干货哦!](http://moguozhou.site/)

