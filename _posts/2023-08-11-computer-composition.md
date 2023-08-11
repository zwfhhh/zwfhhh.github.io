---
layout: post
title: 计算机组成原理
date: 2023-08-11 09:04:00 +0800
category: Basic
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/basic.jpg
icon: note
---


* content
{:toc}
# 计算机组成原理

## 循环冗余校验码

![image-20230107103328153](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230107103328153.png)

==主要注意如何构造CRC码，和如何校验==

当k个信息位大小在一定范围内，循环冗余校验码是具有一定纠错能力的

不同位置出错具有产生的余数是固定的，但是余数转换为10进制和出错位置没有必然的联系

![image-20230107103846977](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230107103846977.png)

## 定点数

> 小数点位置固定

对于一个8个bit位的数值来说，首位为符号位0/1（正负），其他位为数值位

在定点数中，对于所有的整数来说，

正数：原码，反码，补码是相同的

负数：反码=原码符号位不变，其他位不变

​			补码=反码加1，注意进位

移码：将所有的补码的符号位取反（只能表示定点整数）

补码转换位原码和原码转换位补码是一样的方法：

将非符号位取反加1

技巧：由[x]补快速求[-x]补的方法

符号位，数值位全部取反，末位+1

## 加减法

有符号数的加减法：

对于有符号数的减法，我们可以将-xxx这个数变为补码，就变成了和补码相加，将减法变为加法在一定程度上节约了硬件

最原始的求补码的方式是通过  ==模（100000000 也就是 2^8）-a的绝对值=a的补数==

![image-20230108121849038](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230108121849038.png)

## 移码

### 算数移位

![image-20230108155859378](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230108155859378.png)



对于补码的算数位移有

![image-20230108155742041](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230108155742041.png)

### 原码的加减运算

![image-20230108161010965](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230108161010965.png)

## 溢出判断

当采用双符号位时，有:

01：发生上溢

10：发生下溢

其他情况则没有发生溢出

## 原码乘法运算

![image-20230109103027298](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109103027298.png)

![image-20230109103344325](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109103344325.png)

在运算器中， ACC存放结果，MQ放乘数，x放被乘数，刚开始ACC里面都是0，符号位不参与运算

## 补码的一位乘法运算

![image-20230109104257968](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230109104257968.png)

MQ里面的倒数第二位是符号位，倒数第一位是辅助位

## 原码除法（加减交替法）

和整数除法差不多的，一般商除到题目中给出的n长那么多位就可以了，有余数也没关系

计算器中的实现

![image-20230110102404317](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110102404317.png)

除法计算器中是通过商0/1来看acc里面的数符号位是0还是1来确定是否正确

里面的减法使用的是除数的【-|y|】补码，这样直接省掉了一个减法的逻辑单元，因为正数的原码和补码相同，所以原码和它相除没有关系，最后符号位单独通过异或运算处理

 == 注意区别==0正 1负   |   0假1真

在进行下一位商的确定的时候需要提前对余数进行算数左移

![image-20230110104959142](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110104959142.png)

优化思路

![image-20230110105222641](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110105222641.png)

![image-20230110105354639](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110105354639.png)

余数的正负有两个作用

1.确定商0还是1

2.确定下一步加上[-|y|]补码还是[|y|]补码

## 补码除法

和原码除法的区别：

![image-20230110105943311](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110105943311.png)

![image-20230110110313015](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230110110313015.png)

末尾商恒置为1减小误差

## 强制类型转换（c语言中）

c语言中定点整数是用补码来存储的

无符号数和有符号数强制类型转换的时候不改变数据内容，只改变解释的方式

长整数变短整数，高位截断，低位保留

## 浮点数尾数的规格化

浮点数有前面的阶数和后面的尾数构成的

![image-20230111162112429](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230111162112429.png)

我们可以用IEEE 754标准来确定这个阶码采用补码还是移码，尾数采用原码还是补码

这个标准里面阶码采用移码，尾数采用原码

移码=阶码真值+偏移量    偏移量=2^n-1  -   1（==n为阶码位数==）

### 总结

1.在一定意义上也可以将浮点数尾数的规格化叫做将尾数的最高位变为实数，也就是小数点后面第一位

2，阶码的真值是指2上面的指数

3，![image-20230112105136625](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112105136625.png)

将用补码表示的尾数进行规格化有点特殊，需要化为1.0的形式，最高位为0，其他的都是最高位为1，具体小数点前面0/1，要看看正负

4

![image-20230112105949385](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112105949385.png)

5 IEEE 标准

![image-20230112111246991](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112111246991.png)

表示的标准

![image-20230112112735407](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112112735407.png)



6尾数最高位为小数左边为1的时候需要隐含1

7 将其他进制的数转化为单精度浮点数真值

![image-20230112113321010](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112113321010.png)

8特殊情况

![image-20230112113708834](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230112113708834.png)

## ==注意==

1.只有题目说明使用ieee 745标准才会默认隐藏最高位的1，不是的话一般默认不隐藏最高位

2.运算中一般采用补码，题目中如果没说是ieee745的话，阶码和尾数一般都是补码表示

3.加减运算的时候一般减法一般是搞成加上负数的补码，这里用补码的原因是因为计算机一般都是用补码运算

![image-20230113110530766](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113110530766.png)

## 浮点数的加减运算

![image-20230113111916751](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113111916751.png)

尾数相减之后出现01或10的情况时候，发生溢出，就需要做舍入运算

![image-20230113112515184](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113112515184.png)、

## 强制类型转换

![image-20230113112713637](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113112713637.png)

注意这个32位和64位的机器的int 和long型，实际上的话double的64位中53位是尾数，所以当64位的long转为double时候，是会丢失精度的

![image-20230113113319736](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230113113319736.png)











 





















