# CS61B的一些笔记

## git

1. init 初始化
2. add 添加进追踪
3. commit 提交
4. log 日志
5. checkout 回退
6. rest 取消尚未提交的文件

## Java
* java中，除了几个基本类型，是在每次定义的时候指向实体。其余类，每次定义的时候是创造一个指针，指向实体。

```
int x = 5;
y = x;
x = 3;
结论是: x = 3, y = 5
```

```
class x;
x = new class();
y = x;
x.attribute = xxx;
结论：y.attribute = xxx
```

* java 函数传递的是值。但是类每次创建的类都是指针，所以传进函数的是指针的值，64位，如果是基本类型，则传递的是值。
* super 可以利用继承类的函数。**super()先构造先前类。**
* 继承会打破封装。
* 创建通用数组: Item[] temp = (Item[]) new Object[length]; Casting
* 可以通过接口，实现高阶函数，函数的函数，但是需要实体化一个类，然后高阶函数定义使用接口（interface)。
* 所有的类都是object的extend，但接口不是。
* overriding only apply to not static methods.
* java % 对负数不正常 （-1 % 4 = -1）， 需要用Math.floorMod