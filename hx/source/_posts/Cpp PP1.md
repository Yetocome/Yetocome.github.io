title: 《C++并发编程》学习笔记（一）
date: 2016-03-19 22:14:56
categories: Practice
tags: [c++, concurrency]
---
因为一些棘手的效率问题，手头的项目必须要引入并发编程。这里整理下我学习的《C++并发编程》的笔记，尽可能地记录下了重难点以及我练习的例程代码，作为自己学习的备忘，也供想要快速学习的人参考。不过还是建议直接去阅读原书，中英文版本的网盘链接我放在文章后面，请读者自取。

<!--more-->

### 第一章 你好，C++的并发世界
#### 何为并发
* 任务切换(task switching)：  
单核机 - 某一时刻下只能执行一个任务，通过任务切换达成并发效果，任务切换时需要切换上下文(context switch)；
* 硬件并发(hardware concurrency)：  
多核机 - 真正的多任务同时执行，但是核心数仍然供不应求。

![并发的两种方式](http://7xkwu7.com1.z0.glb.clouddn.com/%E5%B9%B6%E5%8F%91%E7%9A%84%E4%B8%A4%E7%A7%8D%E6%96%B9%E5%BC%8F.png)

并发的两种方式

![四任务两核心](http://7xkwu7.com1.z0.glb.clouddn.com/%E5%9B%9B%E4%BB%BB%E5%8A%A1%E4%B8%A4%E6%A0%B8%E5%BF%83.png)

四任务两核心

#### 并发的途径
*  多进程并发  
	![](http://7xkwu7.com1.z0.glb.clouddn.com/%E5%B9%B6%E5%8F%91%E8%BF%9B%E7%A8%8B%E9%97%B4%E9%80%9A%E4%BF%A1.png)
*  多线程并发  
	![](http://7xkwu7.com1.z0.glb.clouddn.com/%E8%BF%9B%E7%A8%8B%E5%86%85%E5%B9%B6%E5%8F%91%E7%BA%BF%E7%A8%8B%E9%80%9A%E4%BF%A1.png)

#### 为什么使用并发
* 为了分离关注点
* 为了性能提升

#### 什么时候不能用并发

>知道何时不使用并发与知道何时使用它一样重要。基本上，不使用并发的唯一原因就是，收益比不上成本。使用并发的代码在很多情况下难以理解，因此编写和维护的多线程代码就会产生直接的脑力成本，同时额外的复杂性也可能引起更多的错误。除非潜在的性能增益足够大或关注点分离地足够清晰，能抵消所需的额外的开发时间以及与维护多线程代码相关的额外成本(代码正确的前提下)；否则，别用并发。

### 第二章 线程管理

#### 线程管理基础
1. 启动线程  
	* 线程在`std::thread`对象创建时启动。
	* 为了让编译器识别`std::thread`类，需将`<thread>`头文件包含进去。
	* `std::thread`可以用可调用(callable)类型构造

			class background_task
			{
			public:
	  			void operator()() const
	  			{
	   			do_something();
	   			do_something_else();
	  			}
			};

			background_task f;
			std::thread my_thread(f);
	* 启动线程后需要明确是要等待线程结束(加入式)还是让其自主运行(分离式)，必须在对象销毁之前做出决定。

			清单2.1 函数已经结束，线程依旧访问局部变量
			struct func
			{
  				int& i;
  				func(int& i_) : i(i_) {}
  				void operator() ()
  				{
   					for (unsigned j=0 ; j<1000000 ; ++j)
    					{
      				do_something(i);           // 1. 潜在访问隐患：悬空引用
    					}
  				}
			};

			void oops()
			{
  				int some_local_state=0;
  				func my_func(some_local_state);
  				std::thread my_thread(my_func);
  				my_thread.detach();          // 2. 不等待线程结束
			}                              // 3. 新线程可能还在运行

		调用清单2.1中的`oops()`后，如果没有什么操作，看上去能够完美运行，可是一旦进行了某些操作，`i`引用地址的值就会发生改变，引发不想要的结果。
2. 等待线程完成  
	如果需要等待线程，相关的`std::thread`实例需要使用**join()**，在清单2.1中将`my_thread.detach()`替换为`my_thread.join()`就可以确保局部变量在线程完成后才被销毁。
	* **join()**等待线程完成或不等待；
	* **join()**清理了线程相关的存储部分，`std::thread`对象将不再与已经完成的线程有任何关联；
	* 只能对一个线程使用一次**join()**，一旦用过，其**joinable()**将会返回false，表示不能再次加入。
3. 特殊情况下的等待  
	如果打算等待对应线程，却因为线程运行后产生异常，在**join()**调用前被抛出，就意味着这次调用会被跳过。
	* 考虑用`try/catch`语句块在异常处理过程中调用**join()**，从而避免生命周期的问题
	* 另一种方式是使用RALL(Resource Acquisition Is Initialization)，并提供一个类，在析构函数中调用**join()**。

			清单2.2 使用RAII等待线程完成
			class thread_guard
			{
			  std::thread& t;
			public:
			  explicit thread_guard(std::thread& t_):
			    t(t_)
			  {}
			  ~thread_guard()
			  {
			    if(t.joinable()) // ①
			    {
			      t.join();      // ②
			    }
			  }
			  thread_guard(thread_guard const&)=delete;   // ③
			  thread_guard& operator=(thread_guard const&)=delete;
			};

			struct func; // 定义在清单2.1中

			void f()
			{
			  int some_local_state=0;
			  func my_func(some_local_state);
			  std::thread t(my_func);
			  thread_guard g(t);
			  do_something_in_current_thread();
			}    // ④

		解析：当线程执行到④时，局部变量即将被逆序销毁，然而`thread_guard`的对象**g**是第一个被销毁的，这时会调用其析构函数，被加入到原始线程中。注意：析构函数中的**joinable()**判断很重要，因为**join()**只能对给定的对象调用一次；
>拷贝构造函数和拷贝赋值操作被标记为=delete③，是为了不让编译器自动生成它们。直接对一个对象进行拷贝或赋值是危险的，因为这可能会弄丢已经加入的线程。通过删除声明，任何尝试给thread_guard对象赋值的操作都会引发一个编译错误。

4. 后台运行线程  
	通常称分离线程为守护线程(_daemon threads_)，由**detach()**引发，c++运行库将会保证线程退出时现相关资源的正确回收，其归属和控制也由其处理。
	* 守护线程的特点是长时间运行，它没有任何用户接口，生命周期可能会从某一个应用起始到结束；
	* Fire and forget：分离线程只能确定什么时候会结束；
	* 调用**detach()**后**joinable()**将会返回false；


这周就先学习到这里，欢迎大家留言交流。

中英文书籍：

1. [_英文版 - 网盘_](http://pan.baidu.com/s/1qXxyxuk)
2. [_中文版 - GitBook_](https://www.gitbook.com/book/chenxiaowei/cpp_concurrency_in_action/details)
