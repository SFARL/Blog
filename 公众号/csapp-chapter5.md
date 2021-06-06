---
title: csapp_chapter5
date: 2020-09-10 19:17:20
tags: [网课,  CSAPP,  第五章]
---
# CSAPP 和 CMU Introduction to computer system ([CS 15-213](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html) 2015 fall) 的笔记。

相关资料
1. [Textbook](http://csapp.cs.cmu.edu/3e/students.html)
2. [在vscode上使用C](https://www.zhihu.com/question/30315894)
## 3.Machine-­Level-Programming


## 5. program optimization
本章探讨如何优化程序的性能。
<!--more-->


### optimizing compiler

编译器对代码的优化主要分为下述几个部分：
1. 寄存器分配
2. 代码的排序
3. 消除无用代码
4. 消除低效率

但编译器优化也存在局限性：
1. 受限于基础代码
2. 许多优化只是基于单个处理过程
3. 许多分析基于静态信息。

### Generally Useful Optimizations

#### code motion 

通过移动代码，通常是出入循环，减少相同的计算。

#### reduction in strength

1.  用简单的操作代替复杂的操作。（加代替乘）
2.  共用表达式
3.  减少函数调用，对需要重复调用且结果相同的用一个变量存起来。（数组长度）
4.  减少频繁内存读取，写入。
  
#### memory aliasing

两个不太的内存引用了同一个地址，这在c中很常见，因为c允许进行地址计算，可以通过指针直达内存。

措施:在循环中的累加使用临时变量。

### Modern CPU Design

现代处理器逻辑图。

![CPU](https://s1.ax1x.com/2020/09/10/wYZiIP.png)

现代处理器是对多条指令并行执行，但若想提高性能，需要设计程序不存在序列依赖。

#### unrolling

通过循环张开，提高并行性，因为pipeline的存在，可同时并行多个加和乘，所以可以有效提升性能。但并行太多会导致寄存器溢出，调用堆栈，降低性能。

#### branch prediction

通过预测未来的指令，提高性能，若预测错误，则清空（fetched）。但不会影响内存数据。

### 其余提升
1. 加载和储存的性能。