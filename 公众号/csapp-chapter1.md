---
title: csapp_chapter1
date: 2020-06-13 19:24:20
tags: [网课,  CSAPP,  第一章]
---
# CSAPP 和 CMU Introduction to computer system ([CS 15-213](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html) 2015 fall) 的笔记。

相关资料
1. [Textbook](http://csapp.cs.cmu.edu/3e/students.html)
2. [在vscode上使用C](https://www.zhihu.com/question/30315894)
## 1.COURSE-OVERVIEW
从编程者的角度来讲述计算机系统。这也是为什么我第一门课选择这个的原因，因为自己是数学系的，之前学习的内容主要是偏向于算法和一些比较抽象的编程，没这么关注到计算机的底层实现（也许是大计基没上心。。。）所以选择这门课做为第一门补充课，希望能对计算机系统有个整体的认识，虽说自己之后是打算做数据挖掘，但对于基础的东西个人觉得还是需要有所了解。

### 主要内容包括：
1. COMPUTER ARITHMETIC (计算机算术规则)
2. ASSEMBLY (汇编)
3. MEMORY (内存)
4. ASYMPTOTIC COMPLEXITY (渐近性?)
5. IO (写入写出)

<!--more-->
### 一些重要的概念：
1. Amdahl 定律
   
     系统某部分执行时间和总时间比例为$\alpha$，而该部分性能提升为$k$，可计算加速比S
     <!-- ![](http://latex.codecogs.com/gif.latex?\\S=\frac{T_{old}}{T_{new}} =\frac{1}{(1-\alpha)+\alpha / k}) -->
     $$S=\frac{T_{old}}{T_{new}} =\frac{1}{(1-\alpha)+\alpha / k}$$
2. 并发和并行
     
     * 并发(concurrancy):同时具有多个活动的系统。
     * 并行(parallelism):使用并发来使系统运行更快。
3. 一些抽象
    
     * 文件是 I/O  设备的抽象。
     * 虚拟内存是程序存储器的抽象。
     * 进程(process)是正在运行程序的抽象。
     * 虚拟机是整个计算机的抽象。

## 2.BITS,BYTES,INTEGER,float
### BITS AND BYTES
1. 布尔代数的and, or, not, Xor运算形成布尔环(代数中的环）。
2. 集合运算的Intersection, Union, Symmetric difference, Complement。
3. 逻辑运算的&&, ||, !.
4. 左移(left shift): 补0。 右移：逻辑右移补0，算术右移根据补码补0/1.

### INT
1. represent
     * Unsigned 和 Two's Complement (补码)。
     * Range: $2^w-1$ 和 $[-2^{w-1}, 2^{w-1}-1]$
     * 无符号整型和补码之间的关系.![](/assets/photos/bits_T2U.png)
     * 扩张和截断，扩张是填充符号位，截断是取模。
2. arithmetic
     * 对于加减乘，因为会溢出，通常的做法不是扩大存储的字节，而是阶段取模。
     * $u*2^k = u << k$ 乘法左移，除法右移。
3. 内存可以看作是一个大的线性数组，每个地方都有对应的地址。

### Float
1. IEEE float: 
     $$(-1)^{S}\ \mathrm{M}\  2^{\mathrm{E}}$$
     
     * S 决定正负，M是[1.0, 2.0)的二进制小数，E决定阶数。
     以32位的float为例，0-22位是frac，即M; 23-30位是exp,$E = exp - bias$, 其中$bias = 2^{k-1} -1$, 这样做的好处是exp变为unsign的了；31位是S。

     * 根据exp的值，分为规格化(normalized)， 非规格化(denormalized(阶码全为0)), 和特殊值(infinity(阶码全1，分数为0), nan(阶码全1，分数不为0)).

     * 注意到浮点数的分布越趋于0越密集。
2. 舍入(rounding) 默认向偶数舍入。
3. arithmetic
     * 加：可交换不可结合(大数吃小数)，Abelian Group. 有单调性。
     * 移位对其后加减
     * 乘：可交换不可结合
     * $\mathrm{S} 1^{\wedge} \mathrm{S} 2$， $\mathrm{M} 1 \times \mathrm{M} 2$， $E 1+E 2$

