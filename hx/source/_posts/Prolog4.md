title: Prolog学习笔记（四）
date: 2015-10-18 15:30:36
categories: Practice
tags: [prolog, AI, language]
---
这周的主要内容是**递归之于表**以及**内建谓词介绍**

<!--more-->

## 递归&表

### 引入——辨别一个表
我们说表是递归定义的，那么是否为表也可以用下面的一个递归规则来测试：

	is_a_list([]).
	is_a_list([_|T]) :-
		is_a_list(T). %Head is replaced with an underscore as we don’t want to use it.
### 为什么使用递归
* 它能让我们使用非常清晰和优雅的代码
* 关系也许是递归的
* 数据是递归定义的，最好的办法是迭代地处理
* 允许prolog在一个问题空间里进行一些复杂的搜索而不需要使用专用的算法

### 递归的形式

在Prolog里，递归的定义和其他语言是差不多的：

1. 首先，需要定义得到终止的条件
2. 然后，就是定义怎样来使一个较大的问题分解成一个相似但是较小的wen
### 递归的三种使用方法


1. 先在**base case**中计算出结果，然后使用这个结果在程序中**回溯**；

		listlength([],0).
		listlength([_|T],N1):-
  			listlength(T,N),
  			N1 is N+1.
2. 在**递归**进入程序时收集结果，最终结束于**base case**；

		listlength([],Acc,Acc).% final result
		listlength([_|T],Acc,Out) :-
			Acc1 is Acc+1,	% 当递归时累加器加一
			listlength(T,Acc1,Out).

3. 使用**未实例化**的变量递归，然后在回溯时收集结果

		listlength() :-
			listlenthlistless(List,0,Length).
		listlength2([], Acc, Acc). %利用了辅助谓词
		listlength2([_|T], Acc, Out) :-
			Acc is Acc+1,
			listlength(T,Accede,Out).

## 内建谓词

按功能来分，内建谓词有以下三类

### 一、识别术语
以下的均为一元谓词，参数即需要判断的术语。

* var/1 判断变量
* nonvar/1 判断非变量或已经实例化的变量
* atom/1 判断原子
* atomic/1 判断数或原子
* number/1 判断数
* integer/1 判断整数
* float/1 判断浮点数
* compound/1 判断结构
* ground/1 判断是否包含未实例化的变量

### 二、分解结构
1. =../2

	**Term =.. L**  
	当L是一个以Term中的主函数子为首，后面跟着它的变量为尾的**表**时为真。  
	ex:

		?- term(a,b,c) =.. L.
		L = [term, a, b, c].
2. functor/3  

	**fuctor(Term,F,N)**  
	当F是Term当的fuctor且N是F的参数数量时为真。  
	ex:

		?- functor(term(a,b,c),F,N).
		F = term,
		N = 3.
3. arg/3

	**arg(N,Term,A)**
	当A是Term的第N个参数时为真  
	ex:

		?- arg(3,term(a,b,c),c).    
		true.

上面三个内建谓词通常用于组合使用。
### 三、收集解答
举个例子，当你想要找到一个目标的所有结果时，你通常会去这么使用谓词：
以member/2为例：

	?- member(X, [a,b,c,d]).
回车，得到结果，然后不断敲击分号得到所有结果直至false（有的直接结束）。

	X = a ;
	X = b ;
	X = c ;
	X = d.
然而，现在我们用一个谓词搞定这些。

1. fingall/3  
	ex1:

		?- findall(X, member(X, [a,b,c,d]), Results).
		Results = [a, b, c, d].%输出的结果是列表形式的
	ex2:

		?- findall(X, (member(X, [1,2,3,4]),X > 2), Results).
		Results = [3, 4].%第二个参数可以是一个目标，也可以是复合目标

	ex3:

		?- findall(X/Y, (member(X, [1,2,3,4]), Y is X*X), Results).
		Results = [1/1, 2/4, 3/9, 4/16].%第一个参数可以是一个复杂的集合，用合一的思想就很好理解

	其他用法：

	* 变量的灵活使用
	* 允许嵌套
	* ^|等逻辑操作符在第二个参数中的使用

2. setof/3

	和findall/3基本一致，差别就是它容许结果出现**重复**而且是**升序排列**的(不仅仅是数字)。

3. bagof/3

	bagof/3和findall/3的区别就是bagof/3会输出第一个参数位置变量的结果，匹配第三个参数依次输出(意味着需要输入分号)，然而findall/3只关心第三个参数的结果，并将他们全部堆在一个表里输出。


上面的三个谓词被统称为**meta-predicates**(元谓词)  
	——因为它们能够操纵Prolog的证明策略


---------

### Remark:

学习的过程中产生了在prolog中**_双引号_**使用的疑惑，在[stack overflow](http://stackoverflow.com)上找到一个不错的[回答](http://stackoverflow.com/questions/8264699/what-is-the-difference-between-and-in-prolog)

时间原因就不翻译了，贴出来给大家学习：
>Single quoted items are always atoms.

>The meaning of double quotes depends on the Prolog flag double_quotes:

>**atom** — with this value `"a" = a`. Nowadays, this is rarely used. But you will find Prolog books where `["abc.pl"]` is written.

>**codes** — a list of character codes. This is frequently the default, but it leads to very unreadable answers like

>		?- phrase(("Ja tvoi ",("sluga"|"rabotnik"),"!"), Satz).
	Satz = [74,97,32,116,118,111,105,32,115,108,117,103,97,33] ;
	Satz = [74,97,32,116,118,111,105,32,114,97,98,111,116,110,105,107,33].
Even worse, if you use characters beyond ASCII:

>		?- phrase(("Я твой ",("слуга"|"работник"),"!"), Satz).
	Satz = [1071,32,1090,1074,1086,1081,32,1089,1083,1091,1075,1072,33] ;
	Satz = [1071,32,1090,1074,1086,1081,32,1088,1072,1073,1086,1090,1085,1080,1082,33].
**chars** — a list of one-char atoms.

>		?- set_prolog_flag(double_quotes,chars).
	true.

>		?- phrase(("Ja tvoi ",("sluga"|"rabotnik"),"!"), Satz).
	Satz = ['J',a,' ',t,v,o,i,' ',s,l,u,g,a,!] ;
	Satz = ['J',a,' ',t,v,o,i,' ',r,a,b,o,t,n,i,k,!].

>		?- phrase(("Я твой ",("слуга"|"работник"),"!"), Satz).
	Satz = ['Я',' ',т,в,о,й,' ',с,л,у,г,а,!] ;
	Satz = ['Я',' ',т,в,о,й,' ',р,а,б,о,т,н,и,к,!].
This notation gives more readable answers. It can be even more compactly displayed since the double quote notation can be used for printing any list of one-char atoms. For SWI, there is [library(double_quotes)](http://www.complang.tuwien.ac.at/ulrich/Prolog-inedit/double_quotes.pl).

>		?- use_module(library(double_quotes)).
	true.

>		?- phrase(("Ja tvoi ",("sluga"|"rabotnik"),"!"), Satz).
	Satz = "Ja tvoi sluga!" ;
	Satz = "Ja tvoi rabotnik!".

>		?- phrase(("Я твой ",("слуга"|"работник"),"!"), Satz).
	Satz = "Я твой слуга!" ;
	Satz = "Я твой работник!".

_Reference:_

1. _Prolog Programming for Artificial Intelligence 4th edition - chapter 6_
