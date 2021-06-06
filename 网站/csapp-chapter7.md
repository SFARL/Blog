---
title: csapp_chapter6
date: 2020-09-21 21:03:20
tags: [网课,  CSAPP,  第七章]
---
# CSAPP 和 CMU Introduction to computer system ([CS 15-213](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html) 2015 fall) 的笔记。

相关资料
1. [Textbook](http://csapp.cs.cmu.edu/3e/students.html)
2. [在vscode上使用C](https://www.zhihu.com/question/30315894)



## linking

从这章开始，后续主要是将程序和操作系统之间的交互关系。

linking（链接）是指将各种代码和数据片段收集并组合成一个单一文件的过程，然后加载到内存并执行。链接可以发生在编译，加载，甚至运行时。

链接使得我们可以建立库，而不必每次编译一个大文件。

学习链接知识的作用：
1. 构造大型程序
2. 避免bug
3. 理解语言的作用域
4. 理解其他的系统概念
5. 利用共享库

链接器干什么？
1. 符号解析：全局变量和函数。存储在符号表中。
2. 重定位：将数据和代码合在一个节上。将内存分配给新的节。修改节中对符号的引用。

<!--more-->
### Executable and Linkable Format (ELF)

ELF是标准的二进制格式的目标文件。

三种目标文件

![三种目标文件](https://s1.ax1x.com/2020/09/21/wbz7yq.png)

ELF 文件格式

![ELF](https://s1.ax1x.com/2020/09/21/wqSSp9.png)

![ELF2](https://s1.ax1x.com/2020/09/21/wqS961.png)

### 符号解析

* 局部变量：局部变量存在栈中，局部静态变量存在.bbs（未初始化），.data中初始化的。

若变量之间有重复定义的（可能发生在不同文件之间，会有比较的关系，而这有可能导致bug，比如说两个未初始化的同名全局变量。所以尽可能使用静态全局变量，不能被其他模块引用，减少bug发生。）

### Loading Executable Object Files

将可执行文件加载到内存

![EFL3](https://s1.ax1x.com/2020/09/21/wqiRwq.png)

### lib

#### static lib

静态库，将相关的目标文件重定位到一个文件（archive）。例如: libc.a libm.a

但是使用静态库需要注意引用的顺序，若存在依赖，被依赖的要前。

而且静态库会造成存储和运行时的重复。而且需要定时更新和维护。

#### share lib

共享库是一个目标模块，在运行或加载时，可以加载到任意的内存地址，并和内存中的程序链接起来。linux中的.so，windows中的.ddl。

值得注意的是，使用共享库，代码和数据节并没有被复制到可执行目标文件中，知识链接器复制了一些重定位和符号表的信息，使得运行时可以引用共享库中的代码和数据。

### Library Interpositioning

库插入？运行程序拦截对共享库的调用。

可以发生在编译，链接，运行时。

可以用来监控函数被调用次数，以及输入输出。

