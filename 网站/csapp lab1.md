---
layout: casapp
title: csapp lab1 datalab
date: 2020-06-20 17:36:28
tags: [lab,  csapp]
---
### LAB 1
这一个 lab 主要涉及了位运算，补码和浮点数等内容。个人觉得不算容易，因为限定了算子和操作数,从比特的层面实现一些功能。
#### 1. 用非和且实现异或
```c
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  // 异或，按位取反后取且
  return (~x&y);
}
```
<!-- more -->
#### 2.输出补码中最小的int
```c
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  // 因为10..0对应的是tmin
  int t = 1;
  return (t << 32);

}
```

#### 3.判断是否是int——max
```c
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  // max 是011...1,加1是tmin，相加等于负1，然后+1再取非.
  return return !(x + (x+1)+1);
}
```

#### 4.判断奇数位(级数次方对应的位）比特是否全为1
```c
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  // 所有级数位全为1，则和偶数位全为1的并是全1，再取反全0，再取非是1.
  unsigned int a = 5;
  unsigned int b = a + (a<<4) + (a<<8) + (a<<16) + (a<<24);
  return !~(b|x);
}
```
这个还想了挺久。

#### 5.取负数
```c
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  // 按位取反后相差负一，补上个1即可。
  return (~x+1);
}
```
#### 6.判断ASCII码
```c
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  // 在这中间的，两个判断语句的符号位都是0，右移31，全为0.
  return !(((x-0x30)>>31) | ((0x39-x)>>31) );
}
```
#### 7.条件语句
```c
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  // 只说左边，右边一样。目标是x取0时，输出0，x取其他时，输出y
  // 首先！x。！x=1, 1-1=0, 一交就是0了，！x=0，-1的二进制是111..，和x交等于x
  return return ((!x-1) & y) | ((~!x - 1) & z);;
}
```
这道题也卡了一小会，主要是利用-1那一开始没想到。
#### 8.小于等于
```c
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  // 右减左，然后左移31位，取非即可。
  return !((y-x)>>31);
}
```
#### 9.逻辑非
```c
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  // 要把0分离出来，0的特性~0+1=0，所以想到了利用这点，其他的数(~x+1)|x肯定是负数，
  // 右移31，得到-1，然后和1交即可。
  return (((~x+1)|x) >> 31) & 1;;
}
```
#### 10.统计比特数
太难了，我倒下了。
```c
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  return 0;
}
```
#### 11.浮点数乘2
```c
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
  // 阶数等于0xff返回本身,inf和nan
  unsigned int temp = uf;
  // 分别取出阶数和符号位和分数
  unsigned int exp = uf & 0x7f80000;
  unsigned int sign = (uf>>31)<<31 ;
  unsigned int frac = uf & 0x007fffff

  // 去除符号
  temp = temp & 0x7fffffff
  // 情况1：对非规格化数据，只需要分数位左移一位即可。
  if ((temp>>23) == 0){
    temp = (temp<<1) | sign;
    return temp;
  } 
  else if ((temp>>23) == 0xff) {
    // 这是对inf和nan数据
    return uf;
  } 
  else {
    // 最后对于规格化数据，阶数+1
    return (exp>>23 +1) | f | sign
  }
}
```

#### 12.float 2 int
```c
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) { 
  // 阶数等于0xff和0x00返回0x80000000u,inf和nan
  unsigned int temp = uf;
  // 分别取出阶数和符号位和分数
  unsigned int exp = uf & 0x7f80000;
  unsigned int sign = (uf>>31)<<31 ;
  unsigned int frac = uf & 0x007fffff
   // 去除符号
  temp = temp & 0x7fffffff 
  // 若是非规格化数据，返回0
  if ((exp>>23) == 0){
    return 0x0u;
  }
  // 阶数等于0xff和0x00返回0x80000000u
  else if ((temp>>23) == 0xff){
    return 0x80000000u;
  }
  else {
    // 阶数大于0，左移对应阶数。
    if (((exp>>23)-127) > 0){
      return sign|(frac<<((exp>>23)-127))
    }
    // 阶数等于0，非规格化数据，直接返回0
    if (((exp>>23) == 0){
      return sign | 0x0u
    }
    // 阶数小于0，右移对应阶数。我选择的舍入是向取整。
    else if (((exp>>23)-127) < 0)
    {
      return sign|(frac>>(-((exp>>23)-127)))
    }
  }
}
```

#### 13.2的整数幂次，关键在于确定范围。
```c
/* 
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4
 */
unsigned floatPower2(int x) {
  // 先给出2
  unsigned int temp = 2;
  unsigned int exp = (1<<23)+127;
  int sign = (x>>31)<<31;
  unsigned value = x & 0x7fffffff;
  // 负数，2右移
  if (sign == 0xffffffff){
    // 太小了，直接返回0；
    if (value > 127){
      return 0;
    }
    // 范围内，减去幂次，然后并起来。
    else {
      exp = exp - (value<<23);
      return (temp|exp);
    }
  }
  else if (sign == 0x00000000){
    // 太大了，溢出,返回inf
    if (value > 127){
      return (temp|(256<<23));
    }
    // 范围内，加上幂次，
    else {
      exp = exp + (value<<23);
      return (temp|exp);
    }
  }
}
```

### 总结
这一章主要讲的是二进制对数据的表示和数学运算。难度我觉得挺难的，主要是当限制到底层，用二进制实现一些在数学定义好的操作上，需要一个思维的转换过程.