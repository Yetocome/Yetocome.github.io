title: Prolog学习笔记（三）
date: 2015-10-05 15:43:02
categories: Practice
tags: [prolog, AI, language]
---
本周的重点是AND/OR树、结构体和表。  
通过AND/OR树，我们可以很好地理解Prolog的证明策略；  
结构体是（一）里没有介绍过的类型，要注意与**事实(facts)**的区别；  
List的构造与使用让我们真正感受到了在prolog里递归的力量，你会感叹设计递归的精妙与艰辛:p

<!--more-->
### Representing Proofs using Trees
为了更好地理解Prolog的证明策略，我们可以用AND/OR树来表示它的行为，利用下图来理解：

![](http://7xkwu7.com1.z0.glb.clouddn.com/prologAND%3AOR%20Tree.png)

对应：  
P :- Q, R.  
P :- S.

Q :- T.  
Q :- U.

**特征**

1. 询问是自顶向下(Top-Bottom)的
2. 树向下生长
3. 每一个分支表示一个子目标
	1. 左边的数字表示序号
	2. 右边的表示变量以及其实例化
4. 每个分支结束于：
	1. 成功的匹配用空心圆圈表示
	2. 不成功则在圆圈里加上叉或
	3. 另一个子目标（backtracking）

### Structures

##### 简介

结构体就是一个拥有一个或几个组成成分的对象，组成成分其本身也可以是结构体，这种嵌套的定义使得树的意义再一次在prolog里体现。

##### 使用

比方说1983年五月一日可以写成：

		date(1,May,1983)
在这里date被称为functor（函数子？），这个结构的组成都是常数，实际上它可以是变量：

		date(Day,may,1983)
它表示的是1983年五月的某一天（其实只是我们这么认为，当然可以有别的解释）

##### 匹配
结构体的匹配原则与在（二）里有较详尽介绍的合一一致。

##### 与事实的区别
语法形式上结构和事实很像，但它们不是一个东西：

1. 当结构未被存储在数据库里时，它不是事实（需要以句号结尾）；
2. 结构通常只被用在组织数据上；
3. 它们的functors不需要去匹配谓词。

但是谓词却能够以结构形式存储...

eg: **command(X) :- X.**

	?- X = write('Passing a command'), command(X).
	Passing a command
	X = write('Passing a command') ?
	true

是不是感觉有些混乱？关于facts、predicates、functors和terms，大家可以参考stackoverflow下的一个问答：  
[http://stackoverflow.com/questions/19115712/in-prolog-is-a-fact-the-same-as-a-functor](http://stackoverflow.com/questions/19115712/in-prolog-is-a-fact-the-same-as-a-functor)

里面的问题是：
>If you have for instance next line of Prolog declaration:
>
>		move(state(middle, onbox, middle, hasnot),
     		grasp,
     		state(middle, onbox, middle, has)).
>
>Are both move and state functors?
>
>I'm kind off confused by facts, functors, terms, ...

这里截取一位热心网友的回答（太长就不翻译了）：
>In Prolog functors are syntactic elements we use to build structures (compound terms) from simpler ones.
>
> Think of a hierarchy of bound Prolog terms, with the base containing the simplest "atomic" cases, i.e. atoms and numbers. Add to these Prolog variables, which may be bound or not depending on context. The rules for Prolog functor names (identifiers) are the same as for Prolog atoms
>
>Functors are syntactic units that have a finite number of arguments ("arity"), and if a functor is supplied with terms for those arguments, then we get a compound term. In your example there is a principal functor move with three arguments, so its arity is 3. The functor name and arity are often combined, since technically Prolog treats the same functor name with two distinct arities as different functors, and so we might refer to move/3 as the outer functor of your compound term.
>
>Note that the first and third arguments in your example of a term are themselves compound terms, built using functor state/4...

更多的自己去原文链接看吧，个人觉得说的还是挺有道理的。

### List
表(list)是一系列数据(包括表自己)的集合，由方括号包着，内部通过逗号(commmas)分隔。

eg：[ann, tennis, tom, skiing]  
还可以是空的：[]

##### 不同的表示形式
第一个元素，我们称之为表头(_head_)，剩下的部分统称为尾(_tail_),所以在prolog里表又有不同的表示形式（喂喂，这里的因果关系有问题吧"(ºДº*)）。  
.(Head, Tail)

所以上面的表可以表示为(可以用树来理解)：  
.(ann,.(tennis,.(tom,.(sking,[]))))

![](http://7xkwu7.com1.z0.glb.clouddn.com/prologlist_tree.png)

更为直观地表示为：  
[Head | Tail]

这其实是list的递归表示，在polog里，它的存在让许多表的操作可以优雅地以**_递归_**方式实现，应用的例子直接从下面的List操作中体现。


##### List 操作

1. concatenation（连接操作）  
	实现：

		conc([], L2, L2).
		conc([H|T1],L2,[H|T]) :-
			conc(T1, L2, T).
	使用

		?- conc([a,b], [c,d], P).
			P = [a, b, c, d].

	实际上你还可以把它当删除用，在query时在List1或者List2的位置用变量就可以了。  
	某种意义上可以用它实现接下来所有操作，但是有些操作还是重写比较简洁，也不容易出现问题，conc在连接单个元素时要注意加上[]，因为它并没有定义单个元素的base case，直接用常量会出现问题，prolog将不会认为那是一个list然后得出false的结论。

2. 删除  
	实现：

		delete(A,[A|X],X).
		delete(A,[B|X],[B|Y]) :-
			delete(A,X,Y).
	使用：

		?- delete(a, [a,b,e,a], L).
		L = [b, e, a] ;
		L = [a, b, e] ;
		false.
	注意这种删除只支持删除一个元素，如果重复的话，它输出的是删除一次的不同情况。
3. 头部添加  
	实现：

		add_front(X, L , [X|L]).
	使用：

		?- add(a,[b,c,d],L).
		L = [a, b, c, d].
4. 是否成员
	实现：

		mem(T,[T|_]).
		mem(A,[_|T]) :-
			mem(A, T).
	不写使用了，累了。

	这里的下划线(underscore)代表它只是个无用的变量，内容是什么无关紧要，prolog在查阅时会跳过它。

5. 子表(sublist)
	实现：

		sublist(S, L) :-
			conc(_, L2, L),
			conc(S, _, L2).
	使用：

		?- sublist(S, [a,b,c]).
		S = [] ;
		S = [a] ;
		S = [a, b] ;
		S = [a, b, c] ;
		S = [] ;
		S = [b] ;
		S = [b, c] ;
		S = [] ;
		S = [c] ;
		S = [] ;
		false.
	这个使用conc比较好理解，你将会得到输入表的所有子表（注意左边的是子表，用反就没完没了了）。
6. 插入
	实现：

		insert(X, L, D) :-
			delete(X, D, L).

7. 计数
	实现：

		len([], 0).
		len([_|L], N) :-
			len(L, N_),
			N is N_ + 1.

8. prolog里自带的list操作

	上面的的操作其实很多prolog已经写好了，直接调来用就可以了，这里给出我用的swi-prolog里的原生操作：

	![](http://7xkwu7.com1.z0.glb.clouddn.com/prologlist.png)  
	有点多，请读者自行在prolog下用help(谓词名)来获取实用信息。

##### list应用——全排列
* 方法一

		permutation([], []).
		permutation([X|L], P) :-
			permutation(L, L1),
			insert(X, L1, P).
* 方法二

		permutation([], []).
		permutation_(L, [X|P]) :-
			delete(X, L, L1),
			permutation(L1,P)

_Reference:_

1. _Prolog Programming for Artificial Intelligence 4th edition - chapter 2/3_
