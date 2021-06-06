---
title: csapp_chapter8
date: 2020-09-21 21:03:20
tags: [网课,  CSAPP,  第八章]
---
# CSAPP 和 CMU Introduction to computer system ([CS 15-213](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html) 2015 fall) 的笔记。

相关资料
1. [Textbook](http://csapp.cs.cmu.edu/3e/students.html)
2. [在vscode上使用C](https://www.zhihu.com/question/30315894)



## Exceptional control flow

处理器从启动到关闭，cpu都是简单的读和执行一个序列的指令。这个序列就是cpu的控制流。

需要异常控制流，解决一些系统状态的改变。发生在各个层次。

了解ECF的作用：

* 帮助理解系统概念。ECF是OS实现I/O，进程和虚拟内存的基本机制。
* 帮助理解如何与OS交互。
* 编写shell和web服务器程序。
* 理解并发。
* 理解软件异常如何工作。非本地跳转。
<!--more-->
### Exception

异常是控制的改变，用来应对一些时间（包括处理器状态的改变）。

异常处理，通过exception handler处理。

![异常处理](https://s1.ax1x.com/2020/09/23/wxeaVK.png)

异常的类型
* interrupt：中断是异步发生的，由处理器的外部导致的，通常放回next的指令。
* traps：有意的，是执行一条指令的结果。像system calls，break point。返回next。
* faults：无意的，且可能被修复的。返回到引起故障的指令重新执行，或者返回到内核中的abort，终止应用程序。例如page fault（要写的地址在硬盘上，需要调出来）
* aborts：无意的且不可修复的。例如非法指令。终止应用程序。

#### systems calls

linux每个系统调用都有一个唯一的整数号。

![systems-call](https://s1.ax1x.com/2020/09/23/wxuCE6.png)

### processes

进程，一个执行程序中的实例，是计算机最深刻，最成功的概念之一。

进程给程序提供了两个关键的抽象：
* 一个独立的逻辑控制流：使得每个程序看起来独占cpu。通过context跳转实现。
* 私有的地址空间：使得每个程序看起来独占内存，通过虚拟内存实现。

context是程序正确运行的所需状态组成。包括程序的代码和数据，它的栈，寄存器内容，计数器等等。

![context](https://s1.ax1x.com/2020/09/23/wxNSr8.png)

单核多进程：每个进程有自己独立的PID。每个进程交替执行，地址空间由虚拟内存管理，不在执行的寄存器的值存储在内存。

多核多进程：共享内存，单独执行不同的进程，由kernel控制执行什么进程。

* 并发（concurrent）：两个进程叫做并发的如果他们之间的流在时间上重叠。
* 并行（parallel）：如果两个流并发的运行在不同处理器核或计算机上。
* 内核（kernel）：OS留在内存的一系列代码。

### process control

系统级函数遇到错误时，通常会返回-1，而且设置全局整数变量errno来指示是什么错误。在写程序调用系统级函数的时候，记得检查错误。

进程分为三种状态：
* running：在执行，或者等待执行而且会被调度。
* stopped：被挂起（suspend)，且不会被调度直到未来收到信号。
* terminated：永远的停止了。

三种情况会导致terminated：
* 收到信号，信号的默认行为是终止。
* 从主函数返回。
* 调用exit函数。

fork 函数，创造一个子进程。
* 返回0给子进程，PID给母进程
* 子进程与母进程几乎独立。
* 子进程与母进程并发执行。
* 相同但独立的地址空间
* 共享文件。

回收子进程。进程终止时，内核并不会立刻清除，进程会处于一种已终止状态。需要母进程回收（wait或者waitpid）。如果母进程不回收，则孤儿进程会被init回收。 

#### Loading and Running Programs

```c
int execve(char *filename, char *argv[], char *envp[])
```
在当前进程的上下文加载并运行一个新程序。

![execve](https://s1.ax1x.com/2020/09/24/0p8Ii9.png)

开一个子进程调用

![](https://s1.ax1x.com/2020/09/24/0pGpRI.png)


### shells

shell是一个交互型应用程序，它代表用户运行其他程序。

下面是shell执行eval函数的例子：
![shell](https://s1.ax1x.com/2020/09/24/0pykI1.png)

### signals

信号就是一条告知进程系统里发生了一些事情的信息。

* 由内核发出到进程
* 由id整型id唯一识别信号类型

#### pending and Blocked

一个发出而没有接受的信号叫待定信号。任何时刻一种类至多只有一个待处理信号，若一个进程由一个类型为K的待处理信号，那么任何接下来发送到这个进程的类型为K的信号都不会排队，他们只是被简单的丢弃。

一个进程可以有选择性地阻塞接收某种信号。当一种信号被阻塞，它仍然可以发送，但是待处理地信号不会被接收，直到进程取消对这种信号的阻塞。


#### Sending Signals

* 每个进程属于一个进程组。
* 可以通过kill函数杀死进程。
* ctrl-c终止进程，ctrl-z挂起进程。
  
#### Receiving Signals

检查进程p未被阻塞的待处理信号（pending & ~blocked），若这个集合为空，内核将控制传递到p的逻辑控制流中的下一条指令。若非空，选择一个k，强制接收k。

#### Safe Signal Handling

信号处理是编程中的棘手问题。
* 与主程序并发运行，共享同样的全局变量，可能互相干扰。
* 如何以及何时接受信号的规则常常有违直觉。
* 不同的系统有不同的信号处理。

### nonlocal jumps

c语言中一种用户级异常控制流形式。将控制直接从一个函数转到另一个当前正在执行的函数，而不需要经过正常的调用-返回序列。（setjmp和longjmp)

## shlab

模拟一个shell。主要掌握几个函数的作用。

### int fork（void）

创建一个子进程。返回两次，返回0给子进程，返回子进程pid给母进程。

```c
pid_t pid	=	fork();	
if	(pid	==	0)	{	
			//	pid	is	0	so	we	can	detect	child	
			printf("hello	from	child\n");	
}	
else	{		
			//	pid	=	child’s	assigned	pid
			printf("hello	from	parent\n");	
}	
```

### int exec() or execve()

取代当前进程的状态的上下文，但会保留PID，打开的文件和信号。

提供了去执行另一个程序的方法。成功执行不返回。

### void exit(int status)

* 通常是status=0，其他数字意味着错误。
* 终止当前进程。
* 变成僵尸态，进程等待回收。

### int wait(int *child_status)，pid_t waitpid(pid_t pid, int *status, int options)

* 挂起当前进程，直到其中一个子进程终止。主要是因为父进程和子进程是并行的，不等待的话，执行顺序会随机。
* 返回子进程pid，pid>0，子进程被回收
* 若child_status != NULL，说明了子进程为什么终止
* waitpid 用的更多。

```c
int	status;	
pid_t child_pid	=	fork();	
if	(child_pid	==	0){	
			/*	only	child	comes	here	*/	
			printf(“Child!\n”);	
			exit(0);	
}	
else{	
			waitpid(child_pid,	&status,	0);	
			printf(“Parent!\n”);	
}	
```

### signal

![signal](https://s1.ax1x.com/2020/09/28/0Eo2N9.png)

### int sigsuspend(const sigset_t *mask)

* 不可以使用两次wait，这时候可以使用sigsuspend
* 短暂的用给定的屏蔽字代替进程的信号屏蔽器。
* 挂起进程直到传出一个信号，涉及信号信号处理或者终止进程。
* 返回，如果信号被截取。然后信号屏蔽字会回到之前的。
* 使用sigaddset（），sigemptyset（），设置信号屏蔽字。
  
### functions

![functions](https://s1.ax1x.com/2020/09/28/0EbPED.png)