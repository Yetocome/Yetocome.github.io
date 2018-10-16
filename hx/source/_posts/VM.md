title: 了解虚拟机
date: 2016-03-12 23:53:50
categories: Theory
tags: [VM, OS]
---

虚拟机是一款提供跨平台一致性体验的"软件"。   
虚拟机最初被Popek and Goldberg定义为_"an efficient, isolated duplicate of a real machine"_。现在通常指与实际硬件不直接相关的虚拟机器。

<!--more-->


### 简介

在传统意义上，一个应用程序是和特定平台（硬件+OS）所绑定的。用户层面上，这意味着你将无法在不同的平台上获得一致的体验，很多软件都只适配了某些特定的平台；开发者层面，因为不同系统适配的麻烦令许多中小开发者放弃对多平台的开发，这也是用户层面问题产生的重要原因。切换平台后，很多人会对新系统无所适从，发现常用的软件以及熟悉的操作方式不复存在。  
虚拟机就是为了消除这种现实平台的限制而存在的，提供了一套更加灵活的解决方案。
### 虚拟机的必要性
1. 便携性(Portability)在当今的网络计算下至关重要；
2. 类型安全、面向对象编程、基于VM的语言成为主流；
3. CPU的创新往往被旧的接口所限制；
4. 跨处理器的优化十分困难。

### 虚拟机的优点
1. 跨平台的兼容性；
2. 增加安全性；
3. 提升性能；
4. 简化软件迁移。

### 虚拟机的历史
许多年前，IBM出售昂贵的主机给大型组织，并且产生了一个问题：要是组织想要同时在一台机器上运行不同的操作系统该怎么办？一些应用被开发在某个系统上然而有些却在其他系统上。结果IBM以Virtual machine monitor(VMM, also hypervisor)的方式间接实现了它。

### 计算机系统的接口
在介绍虚拟机之前，先介绍下计算机的结构，计算机系统是通过不同层级的抽象构建起来的，见下图：
![Computer Architecture](http://7xkwu7.com1.z0.glb.clouddn.com/Computer_Architecture.png)

其中两类接口至关重要：

1. ISA接口
    Instruction Set Architecture，又称指令集或指令集体系。连接软硬件。包括用户级ISA（对应用和OS可见）和系统级ISA（仅对OS可见）
2. ABI接口
    Application Binary Interface，是应用与OS间的接口，由用户级ISA和OS系统调用

### 虚拟机的分类系统
1. Process VMs
位于ABI层，将独立的进程虚拟化 - 虚拟机随进程终止而终止。  
进程虚拟机将ABI层虚拟化，**仿真**了用户级ISA和OS系统调用，这里的VM也可以被称作runtime，VMM则被称作runtime system。  
Runtime将负责管理客户程序，负责其与OS的沟通。客户程序可能会和主程序掺杂在一起。
    1. Same ISA
        * Multiprogrammed Systems
        * Dynamic Binary Optimizers
    2. Different ISA
        * Dynamic Translators
        * HLL VM - 高级语言虚拟机， 典型代表JVM，赋予了语言跨平台的特性

	![ProcessVM_1](http://7xkwu7.com1.z0.glb.clouddn.com/ProcessVM%201.png)
	![ProcessVM_2](http://7xkwu7.com1.z0.glb.clouddn.com/ProcessVM%202.png)
2. System VMs
位于ISA层，虚拟出一个完整的系统 - 支持多进程，支持设备I/O，支持GUI界面。
    1. Same ISA
        * Classic System VM - VMM直接搭在纯硬件系统上(通常采用私密模式)，为客户系统解释和实现所有私密操作。
        * Hosted VM - 直接将VMM搭在常规宿主操作系统上，缺点是效率较低。
    2. Different ISA
        * Whole System VM - 客户系统和宿主系统采用不同的ISA。
        * Codesigned VM - 为了能够使用一些创新的ISAs和/或硬件实现，从而能够提升性能与能耗
	![SystemVM_1](http://7xkwu7.com1.z0.glb.clouddn.com/SystemVM_1.png)
	![SystemVM_2](http://7xkwu7.com1.z0.glb.clouddn.com/SystemVM_2.png)

### 虚拟化技术
#### 纯硬件之上的虚拟化（本机执行）
这种方法被称之为对硬件完全的虚拟化，可以使用type 1或type 2的当时执行管理程序：type 1 管理程序直接跑在硬件上，type 2则跑在另一个操作系统之上。每个虚拟机上可以运行任何一个被底层硬件所支持的操作系统，用户也因此可以同时运行两个甚至更多的"Guest"操作系统，相互之间无法干涉。  
这种虚拟机甚至可以是recursive的——虚拟机之上可以运行虚拟机。

#### 非本机系统模拟
虚拟机可以承担模拟器的责任，让软件和操作系统为另一个计算机处理器的结构所运行。模拟可以完全是软件层面的，或者可能包含一些硬件的成份比如说微指令。
####  操作系统级别的虚拟化
这种虚拟化是在操作系统之上的服务器虚拟化技术。可以这么理解，一个单独的物理服务器被分片成好几个小部分（又称作VE, VPS, guests, zones, etc），每个部分从用户的角度看像是真的服务器一般。

**内容还很简陋，如有错误欢迎指出，不定期更新。**


_Reference_

1. Virtual machine monitor, 2014, ARPACI-DUSSEAU， The easy piece
2. [Runtime system - Wikiwand](https://www.wikiwand.com/en/Runtime_system)
3. [Vitual machine - Wikiwand](https://www.wikiwand.com/en/Virtual_machine)
