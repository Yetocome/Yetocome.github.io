title: Prolog学习笔记（二）
date: 2015-09-27 17:29:00
categories: Practice
tags: [prolog,AI,language]
---
第三周：这周的主要内容是算数操作符，合一的概念还有跟踪和调试的方法。

这是第一个版本，时间仓促，没有怎么看书就发了出来，如有出错还请见谅，之后会对错误和遗漏进行修改。

<!--more-->

### 算数操作符(Arithmetic Operators)
1. 操作符种类：
    * 运算操作符：+, -, *, /,^,mod（还可以使用div代表/，但是有所不同，/是实数除法，div是整数除法）
    * 比较操作符：<, >, =<, >=, =:= (equals), =\= (not equals)
2. 操作符使用：表达式的返回值（可以这么解释吗）就是结果。
    * 前缀(prefix)：像用谓词一样使用即可；+(2,3)表达的就是2+3，还可以嵌套，这可以直观的用操作符树来表示；
    * 中缀(infix)：和普通的计算公式相同相同；
    * 后缀(postfix)：用的比较少；
    * 自定义：可以通过自定义谓词来进行一些不同的计算过程，这周比较忙，这里就不再展开，先挖个坑
               <我是坑>
3. 操作符优先级：^before mod before /, *, before +,-(和平时使用相同，括号的优先级最高)
4. 计算结果：使用is才能够得到计算结果，不然只是一个表达式。
    对比：
          ?- X = 2 + 3.
          X = 2+3.
          ?- X is 2 + 3.
          X = 5.


### 合一(Unification)
同一(Unify)概念是在 Prolog 背后的主要想法，它是逻辑学当中的一个的重要概念。《Prolog Programming for Artificial Intelligence》一书强调了出于效能的原因，在Prolog里用unification是不准确的，应该使用匹配(matching)，
    比如我们问Prolog：

     ?- X = f(X).
     这句匹配请求应该是成功还是失败？在逻辑中的合一(Unification)，它应该是失败的，然而在prolog里就能实现。大家可以通过下面的跟踪感受一下：
     [trace]  ?- X = f(X), Y = X.
       Call: (8) _G951=f(_G951) ? creep
       Exit: (8) f(f(f(f(f(f(f(f(f(...)))))))))=f(f(f(f(f(f(f(f(f(...))))))))) ? creep
       Call: (8) _G956=f(f(f(f(f(f(f(f(f(...))))))))) ? creep
       Exit: (8) f(f(f(f(f(f(f(f(f(...)))))))))=f(f(f(f(f(f(f(f(f(...))))))))) ? creep
    X = Y, Y = f(Y).

    [trace]  ?- X = f(X), Y =f(X).
       Call: (8) _G951=f(_G951) ? creep
       Exit: (8) f(f(f(f(f(f(f(f(f(...)))))))))=f(f(f(f(f(f(f(f(f(...))))))))) ? creep
       Call: (8) _G958=f(f(f(f(f(f(f(f(f(...))))))))) ? creep
       Exit: (8) f(f(f(f(f(f(f(f(f(...)))))))))=f(f(f(f(f(f(f(f(f(...))))))))) ? creep
    X = Y, Y = f(_S1), % where
    _S1 = f(_S1).

总而言之，它表示绑定变量的内容的机制并可以看作为一种只一次的(one-time)赋值。在 Prolog 中，这种操作用符号 "=" 来指示。
1. 在传统 Prolog 中，未实例化的变量 X —— 就是说在它上面以前没有进行合一，可以合一于一个原子、一个项、或另一个未实例化的变量，因此在效果上变成了它的别名。在很多现代 Prolog 方言和一阶逻辑演算中，变量_不能合一于包含它的项_；这叫做出现检查。
2. Prolog 原子只能合一于同一个原子。
3. 类似的，项只能合一于另一个项，如果顶部函数符号和项的元数(arity)和这个项是一样的，并且参数可以同时合一。注意这是递归行为。
由于它的声明本性，一序列合一的次序(通常)是不重要的。

注意： 在一阶逻辑的术语中，原子是基本命题而且其合一同 Prolog 项一样。
example:
     1. 这些是合一的：
          red = fred.         true
          ‘Hey you’ = ‘Hey you’.    true
          fred=X.            X=fred.
          X=Y.            Y=X.
          foo(X) = foo(bar).        X=bar.
          foo(N,N) = foo(bar, X).        N=X, X=bar.
          foo(foo(bar)) = foo(X)        X=foo(bar)
     2. 这些不是合一的：
          1 = sin(pi/2).     //1 is sin(pi/2).也是false，然而1 =:= sin(pi/2)是true
          fred = jim.
          ‘Hey you’ = ‘Hey me’.
          frou(frou) = f(frou).
          foo(bar) = foo(bar,bar).
          foo(N,N) = foo(bar,rab).
### 其他命令：
* 跟踪(Trace)：
          ?- trace.//开始
          [trace] ?- notrace.//结束
* 调试(Debug)：
          [trace] ?- notrace.//从跟踪结束
          [debug] ?- nodebug.//结束调试


_Reference:_
1. _[https://zh.wikipedia.org/wiki/合一](https://zh.wikipedia.org/wiki/合一)_
2. _[http://www.swi-prolog.org/pldoc/doc_for?object=manual](http://www.swi-prolog.org/pldoc/doc_for?object=manual)_
3. _Prolog Programming for Artificial Intelligence 4th edition - chapter 2、3_
