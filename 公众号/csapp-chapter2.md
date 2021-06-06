---
title: csapp_chapter2
date: 2020-09-02 18:17:20
tags: [网课,  CSAPP,  第二章]
---
# CSAPP 和 CMU Introduction to computer system ([CS 15-213](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html) 2015 fall) 的笔记。

相关资料
1. [Textbook](http://csapp.cs.cmu.edu/3e/students.html)
2. [在vscode上使用C](https://www.zhihu.com/question/30315894)
## 3.Machine-­Level-Programming


### BASIC

计算机执行机器代码，用字节序列编码低级的操作，包括处理数据，管理内存，读写存储设备上的数据，以及利用网络通信。GCC 以汇编代码的形式产生输出，汇编代码是机器代码的文本表示。

机器级编程中，两种抽象很重要。第一种是Instruction Set Architecture (ISA，指令集) 定义了机器级程序的格式和行为。第二种是，机器级程序使用的内存地址是虚拟地址，内存模型看上去是个大数组。

 ![cpu和内存交互](https://s1.ax1x.com/2020/08/30/dq4QEj.png)

<!--more-->

#### Turning C into Object Code

在终端通过
```
gcc –Og –S sum.c
```
将会输出sum.s的汇编代码文件。

在终端通过
```
gcc –Og –c sum.c
```
将会输出sum.o的二进制代码文件。

##### 反汇编器
在终端通过
```
objdump -d sum.o
```
将会输出sum.s的汇编代码文件。(也可以对exe文件操作)

#### Assembly Basics: Registers, operands, move

操作分为立即数，寄存器，内存：

![操作](https://s1.ax1x.com/2020/08/31/dX0z4O.png)

内存计算方式：

![内存计算方式](https://s1.ax1x.com/2020/08/31/dXDkz4.png)

#### Arithmetic & logical operations

leaq（很常见） src,dst
* Src是一个地址类型表达式

![乘法和左移](https://s1.ax1x.com/2020/08/31/dXDgwq.png)

基本二元算术运算

![基本算术运算](https://s1.ax1x.com/2020/08/31/dXDz1e.png)

### control

#### Condition codes

1. 条件码寄存器：
* CF： 无符号溢出
* ZF：零
* SF：负数
* OF：有符号溢出

2. 
   ```
   cmpq b, a 
   ```
   
   计算a-b的条件码，不改变寄存器的值。
   ```
   testq b, a 
   ```
   计算a&b的条件码，不改变寄存器的值

3. 访问条件码
   ```
   setX
   ```
   根据条件码的组合，将最低位设为0或1.

   ![条件码](https://s1.ax1x.com/2020/08/31/dXfpjK.png)

#### Conditional branches

```
jX
```
跳到其他部分

![跳跃](https://s1.ax1x.com/2020/08/31/dXI0Q1.png)

但使用条件控制在现代处理器上可能会非常低效（因为有pipeline的存在，如果预测错误，需要全部重新计算），替代的策略是使用数据的条件转移 。计算条件操作的所有结果然后根据条件，选取一个，但这种方法需要只有在一些情况下才可行（各分支独立？）。

#### Loops

通过jump来控制循环。

#### switch statements

### procedures
过程是软件中一种重要的抽象，提供了一种封装代码的方式，用一组指定的参数和一个可选的返回值实现某种功能。函数是过程的一种。
#### stack structure

c语言过程调用机制的以恶搞关键特性在于使用了栈数据结构，栈向低地址方向增长。栈指针%rsp指向栈顶元素。通过pushq,popq指令控制进出栈。

#### Calling Conventions

```
callq A <fun>
```
通过指令callq将地址A（返回地址）压入栈中，同时将起始地址设为函数的的起始地址。

一个函数若需要传输超过6个整型参数（寄存器数量）就要通过栈来传递超出的参数了。

函数调用中，寄存器临时变量的存储。要保存一个寄存器的值不变，要么不改变它，要么压入栈中，改变寄存器的值，然后再返回前从栈中弹出旧值。

![临时变量](https://s1.ax1x.com/2020/09/01/dzPVUO.png)

![](https://s1.ax1x.com/2020/09/01/dzP1qP.png)

#### illustration of recursion

通过栈的特性，使得递归调用与调用其他函数没用本质的不同。

### data

#### Arrays

一维数组的基本表示格式
```c
T A[L]
```

c中数组的运算通常根据地址的指针运算实现（c语言中数组的表示）。

二维数组存储表示（行优先）

![二维数组存储表示](https://s1.ax1x.com/2020/09/02/wSwNPP.png)

动态数组

![动态数组](https://s1.ax1x.com/2020/09/02/wSBBAs.png)

### structures

c语言提供了两种将不同类型对象组合到一起创建数据类型的机制。
* 结构(structure)：将多个对象集合到一个单位中。
* 联合(union)：允许用几种不同的类型来引用一个对象。

![结构体定义](https://s1.ax1x.com/2020/09/02/wSrJOS.png)

#### Alignment Principles

由于许多计算机系统要求某种对象类型地址必须是某个值的k（对象占用字节）倍，所以对数据地址需要对齐（可以提高性能）。

将大字节的数据放在结构体前面可以节省空间。

### floating point

数据存储和操作在xmm寄存器中。

### 缓冲区溢出

c对于数组引用不进行任何边界检查，而且局部变量和状态信息都放在栈中，对越界数组的写操作会破坏存储在栈中的信息。

下面是一个缓冲区溢出的例子

```c
/* Implementation of library function gets() */
char *gets(char *s)
{
int c;
char *dest = s;
while ((c = getchar()) != ’\n’ && c != EOF)
*dest++ = c;
if (c == EOF && dest == s)
/* No characters read */
return NULL;
*dest++ = ’\0’; /* Terminate string */
return s;
}

/* Read input line and write it back */
void echo()
{
char buf[8]; /* Way too small! */
gets(buf);
puts(buf);
}
```
在echo函数，buf设置的缓冲区大小只有8字节，然后调用get函数，根据输入字节的不同，会有不同的后果。

![](https://s1.ax1x.com/2020/09/02/wpaAje.png)

#### 对抗缓冲区溢出攻击
1. 栈随机化：栈的位置在每次运行时都有变化。
2. 栈破坏检测：检测越界写。
3. 限制可执行代码区域：限制那些内存区域能够存放可执行代码。其余位置只能读写。