---
title: csapp_chapter6
date: 2020-09-18 16:48:20
tags: [网课,  CSAPP,  第六章]
---
# CSAPP 和 CMU Introduction to computer system ([CS 15-213](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html) 2015 fall) 的笔记。

相关资料
1. [Textbook](http://csapp.cs.cmu.edu/3e/students.html)
2. [在vscode上使用C](https://www.zhihu.com/question/30315894)



## 6.The-Memory-Hierarchy

由于计算机访问不同层次的结构的存储器时间不同，理解存储器层次结构，对程序的性能有巨大影响。整章的关键思想是局部性（locality），指程序倾向于访问相同的数据集或者邻近的数据集。

<!--more-->
### Storage technologies and trends

#### Random-­Access Memory （RAM）

随机访问存储器，分为静态的SRAM和动态的DRAM，SRAM更快更贵，访问速度快10倍。

![SRAM-vs-DRAM](https://s1.ax1x.com/2020/09/13/w0LjLq.png)

### NonvolaCle Memories

非易失性存储器，即断电后仍然可以保存信息，整体上称为ROM（只读存储器）。

#### Uses for NonvolaCle Memories

1. bios，网卡，磁盘控制器，gpu，
2. ssd（solid state disks）
3. 磁盘缓存

### Traditional Bus Structure Connecting  CPU and Memory

通过总线（bus）

![bus](https://s1.ax1x.com/2020/09/14/wBb6Vf.png)

### disk

磁盘广泛应用的大量数据存储设备。

![disk](https://s1.ax1x.com/2020/09/14/wBqPIO.png)

由于现代磁盘构造复杂，有多个盘面，盘面上有不同的记录区。于是就有了逻辑分区，存储在磁盘控制器上的映射（surface， track， sector）

### I/O Bus

![io-bus](https://s1.ax1x.com/2020/09/14/wBOEDI.png)

IO总线与底层cpu无关，尽管比系统总线，内存总线慢，但兼容更多的第三方设备。

### SSD

SSD是基于闪存的存储技术，是传统旋转磁盘的有力替代品。（快，贵）

![ssd](https://s1.ax1x.com/2020/09/14/wBjCXd.png)

1. 一个闪存有块（block）和页（plage）组成。
2. 数据以页为单位读写，读比写快。(擦除时间较长)
3. 只有在一页完全擦除后才能写入。
4. 一个块大约可以进行100000词重复写。

![存储速度对比](https://s1.ax1x.com/2020/09/14/wrROFs.png)

### Locality of reference

局部性通常分为两种不同的形式。
1. 时间局部性（temporal locality）：被引用过一次的内存位置很可能在不远的将来被再次引用。
2. 空间局部性（spatial locality）：被引用过的内存位置附近很可能被再次引用。
  
#### data reference
1. 数组按序访问（spatial）
2. 对一个变量重复引用（temporal）

#### instruction reference
1. 循环体内的指令（spatial）
2. 循环被执行多次（temporal）

### Caching in the memory hierarchy

由于存储技术硬件的差异和程序局部性的特性，计算机科学家设计了组织存储器系统的方法——存储器层次结构。

![memory-hierarchies](https://s1.ax1x.com/2020/09/14/wrhmZD.png)

每一层的缓存都源自于上一层。越往上，越小，越快。

**通过这样的层次结构，用便宜的储存器在底部构造了大的储存池，但程序的数据处理速度接近顶部的缓存。（这样做能成是因为局部性）**

#### types of cache misses

1. cold miss：当cache空的时候
2. conflict miss：因为硬件缓存采用严格的放置策略（随机放置定位成本太高）k+1层的某个块放置在k层的块中，会导致对象被映射到同意个块中，一直不命中。
3. capacity miss：缓冲块不够大。

### Cache memory organization and operation

缓冲器结构

![cache-organization](https://s1.ax1x.com/2020/09/14/wrobjI.png)

怎么看地址，tag是在行中定位（E）定位，set index 是在块中定位（S），b是偏移量。

![](https://s1.ax1x.com/2020/09/14/wrT93j.png)

为什么用中间的位做索引：因为可以避免连续的地址映射到同一个缓存区（conflict miss）

置换的原则通常是LRU（least recently used）

#### write

命中时的写入分两种
1. write-through：立刻写入内存
2. write-back：写入缓存，等被更换时再写入内存。

不命中的写入
1. write-allocate：读进缓存，更新缓存
2. 直接写入内存。

缓存的性能

![缓存性能](https://s1.ax1x.com/2020/09/14/wrLau9.png)

### Performance impact of caches

全书的封面，memory mountain，程序以步长stride扫描数组头elems个元素来产生读序列。然后返回测试出的吞吐量。

![memory-mountain](https://s1.ax1x.com/2020/09/14/wsPHnf.png)

#### Rearranging loops to improve spacial locality

通过调整矩阵循环的顺序提高空间局部性，其中典型的是矩阵乘法。

C语言数组的存储是行存储。对一个矩阵，行遍历的miss rate 是1/B当矩阵维度大于bolcksize的时候。但列遍历的话是1.

$$C_{ij} = \sum_k a_{ik} * b_{kj}$$

这里b会涉及到列遍历。通过把k放在循环最顶层，消除列遍历。

![matrix-multiplication](https://s1.ax1x.com/2020/09/14/wskhh4.png)

#### Using blocking to improve temporal locality

通过块的方法，提高时间局部性。

将数据分成一块块的，使得整块载入缓存计算，完成所有读写，然后下一个块。但是代码非常难写。