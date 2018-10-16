title: Domain analysis —— 进入新领域的必由之路
date: 2017-03-10 16:38:02
categories: Theory
tags: [methodology, research]
---

进入一个未知的崭新领域，往往伴随着新奇与迷惑，在如今这个信息爆炸的时代尤其如此。最近在阅读综述时偶然发现Domain analysis这个terminology，看来其实前人早就为这个问题准备好了方法论，为了更好地继续进行我之前的探索，我也在这里总结一下Domain analysis相关的材料。希望从中获得一些做研究，写综述，作总结的一些启发。

<!--more-->

## 什么是domain analysis
Domain analysis最初是来源于SE(Software Engineering)的一个概念，加州大学Irvine分校的信息计算机科学系的教授 James Neighbors在1980年提出：

> The concept of domain analysis is introduced to describe the activity of identifying the objects and operations of a class of similar systems in a particular problem domain. A domain analysis is represented by a domain-specific language, a prettyprinter, source-to- source transformations, and software components.  

1990年，软件工程学家Kang, Kyo C.在他的论文Feature-Oriented Domain Analysis (FODA) Feasibility Study中对domain analysis有了更加详细的定义，他认为domain analysis是：

> “The process of identifying, collecting, organizing, and representing the relevant information in a domain based on the study of existing systems and their development histories, knowledge captured from domain experts, underlying theory, and emerging technology within the domain.“  

这个定义相比之下更具有普适意义，而这篇论文，也成了这个概念应用的被引用的最广泛的论文之一，FODA作为一种经典的领域分析方法，在计算机科学领域中大放异彩。

在SE之外，domain analysis也被应用于LIS(library and information science)。

概括来说，LIS中的domain analysis就是从文献的标题及内容中，提取出该领域的关键字群组，经过字词群组的整合之后，整理出该领域所蕴含的钙奶呢语文献架构。

Hjørland认为domain analysis应该由领域专家和LIS专家合作进行。而我们作为普通的科研人员，在了解一些domain analysis的方法论之后，也能够更好地整理自己研究领域的知识。

## Domain analysis的常用方法

### LIS领域：

Hjørland认为LIS领域的问题在于一般的学科专家并不是IS的专家，而domain analytic approach试图解决这些问题，提供学科专家对其领域的专业知识进行整理的方法。他从过去中的研究中整理出了十一种方法：

1. Producing literature guides or subject gateways
2. Constructing special classifications and thesauri
3. Indexing and retrieving specialities
4. Empirical user studies
5. Bibliometrical studies
6. Historical studies
7. Document and genre studies
8. Epistemological and critical studies
9. Terminological studies, language for special purpose  LSP),database semantics and discourse studies
10. Structures and institutions in scientific communication
11. Scientific cognition, expert knowledge and artificial intelligence  AI)

### SE领域

* DARE: Domain Analysis and Reuse Environment
* Feature-Oriented Domain Analysis (FODA)
* IDEF0 for Domain Analysis
* Model Oriented Domain Analysis and Engineering

## Domain analysis的过程

LIS中的一种过程：

(一)第一阶段 - 领域与知识范围的确认: 对选定的领域进行名词整理、定义与范围确认，将此领域主题的同义词整理出来，列出通用的同义词或者比较多人使用的名词，并对此领域进行定义与范围的确认。

(二)第二阶段 - 领域知识架构的形成:搜集这一领域相关的书籍、文献、分类表、网络资源等，归纳出现行的各种领域知识架构。
(三)第三阶段 - 确认和调整领域知识架构：
经由问卷调查，了解这一领域的多名专家对已经归纳出的知识架构有什么看法，并将最多专家认同的领域知识架构与领域专家再次讨论，修改得出最终的领域知识架构。
LIS的领域分析，更像是建立起了一个index，或者说找到一种taxonomy。最值得借鉴的是第一阶段，如果不事先确定好领域的范围，整理好领域内的一些概念，可能接下来会走不少弯路。

SE领域，论文FODA中将domain analysis主要分为以下三个阶段：

1. Context analysis: defining the extent (or bounds) of a domain for analysis.2. Domain modelling: describing the problems within the domain that are ad-dressed by software.
3. Architecture modelling: creating the software architecture(s) that implements a solution to the problems in the domain.

SE的领域分析更加复杂和和专业化，它的最终目的是从已有的系统中提取出可复用的部分，避免以后的重复研究。SE中有不少值得一看的关于领域分析的方法论，希望以后能有时间研读。


References:

[1] Kang, Kyo C., Sholom G. Cohen, James A. Hess, William E. Novak, and A. Spencer Peterson. Feature-oriented domain analysis (FODA) feasibility study. No. CMU/SEI-90-TR-21. Carnegie-Mellon Univ Pittsburgh Pa Software Engineering Inst, 1990.  
[2] 陳奕璇、阮明淑 （2006）。領域分析之方法學研究。Workshop of Recent Advances in Library and Information Science【線上查詢】，Autumn 2006。http://moodle.lips.tw/~tcasist/  
[3] Hjørland, Birger. "Domain analysis in information science: eleven approaches–traditional as well as innovative." Journal of documentation 58, no. 4 (2002): 422-462.  
[4]  https://www.wikiwand.com/en/Domain_analysis  
[5] https://www.wikiwand.com/en/Domain_engineering	  
[6] Neighbors, James Milne. "Software construction using components." PhD diss., University of California, Irvine, 1980.  
