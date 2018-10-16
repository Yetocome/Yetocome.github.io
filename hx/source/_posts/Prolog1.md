title: Prolog学习笔记（一）
date: 2015-09-18 21:18:00
categories: Practice
tags: [prolog,AI,language]
---
### 前言：

选修了人工智能导论，要求的语言就是prolog，上了两节觉得这个语言还是挺有趣的，和以前的完全不是一个风格，这里整理一下自己初步的学习。

### Prolog简介：

有别于一般的函数式语言，prolog的程序是基于谓词逻辑的理论。最基本的写法是定立对象与对象之间的关系，之后可以用询问目标的方式来查询各种对象之间的关系。系统会自动进行匹配及回溯，找出所询问的答案。

<!--more-->

配置环境（macbook air）：
1. 直接用homebrew安装swi-prolog（没有安装的参见另一篇博文：[Homebrew 安装使用总结](http://xmhtech.me/2015/09/19/Homebrew安装使用总结/)）
>What is swim-prolog?
>它是一个prolog的解释器

    brew install homebrew/x11/swi-prolog

2. 常用命令

        swipl //调出主界面
        halt. //退出，Control+D
        consult('’). //
        reconsult().//

基本语法

1. 谓词(predicate)：Prolog的基本组成元素，可以是一段程序、一个数据类型或是一种关系，由谓词名和参数组成，名称相同、参数不同的谓词是不同的谓词；
2. 参数(argument)：
     * 变量(variables)
          - 总是以大写字母或者下划线开始
          - 对于变量没有类型而言，它能选用任何值
          - 变量有它自己的作用域，其他子句的同名变量不会继承上一子句的值
          - 变量可以被看做是全称量词，读作“任给”
     *常量(constants) :
        - 原子(atom)：
             - 以小写字母开头，由字母、数字和下划线组成
             - 由特殊符号组成
             - 由单引号围起来的句子
        - 数字(number)：绝对值小于某数的正数或负数
     * 结构体(structure)：后面介绍
3. 规则(rules)
     - 结论(子句头)：由条件决定是否为真
     - 条件(子句体)：一系列用逗号隔离的目标，逗号被认作为“与”
4. 事实(facts)：可以看做只有头的规则
5. 问题(query)：可以看做只有体的规则
6. 注释(comment)
     * 单行：%
     * 多行：/*开始*/结束

其他：
     1. 询问时，分号的作用：使当前子句无效，继续搜索答案。

**Prolog是如何回答问题的?**

Prolog寻找证据的顺序正好与我们的推理相反，即执果索因，使用目标、规则、代换和当前目标来推导出新的目标，直到新目标恰好是简单事实时，给出答案。

_Reference:_

1. _Prolog Programming for Artificial Intelligence 4th edition - chapter 1_
