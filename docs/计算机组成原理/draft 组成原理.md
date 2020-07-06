

存储元件(又称存储基元、存储元)用来存放一位二进制信息。存储单元由若干个存储元件组成能存放多位二进制信息。许多个存储单元可组成存储矩阵(又称存储体)。每个存储单元中二进制代码的组合即为存储字它可代表数值、指令、地址或逻辑数等。每个存储单元中二进制代码的位数就是存储字长。

CPU：中央处理器，运算器+控制器 

  PC：程序计数器，用来存放当前欲执行指令的地址，它与主存的MAR之间有一条直接通路，且具有自动加1的功能，即可自动形成下一条指令的地址。 

  IR：用来存放当前的指令，IR的内容来自于主存的MDR 

  CU：控制单元，用来解释存储器中的指令，并发出各种操作命令来执行指令 

  ALU：算逻部件，用来完成算术逻辑运算 

  ACC：累加器，运算器中的寄存器的一种 

  MQ：乘商寄存器 

  X：操作数寄存器 

  MAR：存储器地址寄存器，用来存放欲访问的存储单元的地址，其位数对应存储单元的个数 

  MDR：存储器数据寄存器，用来存放从存储体某单元取出的代码或者准备往某存储单元存入的代码，其位数与存储字长相等。 

  I/O：输入与输出设备，也称外部设备 

  MIPS：单位时间内执行指令的平均条数（百万条指令每秒） 

  CPI：执行一条指令所需的时钟周期（机器主频的倒数） 

  FLOPS：浮点运算次数每秒

![图片](https://uploader.shimo.im/f/xVYuhYTyecb45ooJ.png!thumbnail)

![图片](https://uploader.shimo.im/f/cpzNYARHEfqqBGd8.png!thumbnail)

1-6取指令 分析指令

![图片](https://uploader.shimo.im/f/OAam2dzP4Ab3QIwr.png!thumbnail) 

计算机硬件指标

1.机器字长

2.运算速度 主频 核数 吉普森法（加权指令时间）CPI执行一条指令的时钟周期 MIPS 每秒执行百万指令 FLOPS每秒浮点运算次数

# # 分块和分页
内存要逻辑上连续，因此出现了分块，问题是分块会产生内存碎片问题，所以有了交换分区，先写入磁盘，然后请求不到的时候操作系统抛异常，然后捕获处理，问题是磁盘读取速度远小于内存，所以要优化，减少内存碎片的数量

分页，每块内存分成固定大小4KB，一页一页，这样交换也是几页内的交换，粒度更小


# 二进制
# 原码 补码 反码

我尝试硬生生的把它们串起来哈 

数字在自然界中抽象出来的时候，一棵树，两只猪，是没有正数和负数的概念的

计算机保存最原始的数字，也是没有正和负的数字，叫没符号数字

如果我们在内存分配4位（bit）去存放无符号数字，是下面这样子的

![图片](https://uploader.shimo.im/f/NzRZLBuczRIFAPBo.png!thumbnail)

后来在生活中为了表示“欠别人钱”这个概念，就从无符号数中，划分出了“正数”和“负数”

正如上帝一挥手，从混沌中划分了“白天”与“黑夜”

为了表示正与负，**人们发明了"原码"，把生活应该有的正负概念，原原本本的表示出来**

把左边第一位腾出位置，存放符号，正用0来表示，负用1来表示

![图片](https://uploader.shimo.im/f/Jz1YH81rGZA7PiOe.png!thumbnail)

但使用“原码”储存的方式，方便了看的人类，却苦了计算机

>![图片](https://uploader.shimo.im/f/OPlXoiOPr40XXB3p.png!thumbnail)

我们希望 （+1）和（-1）相加是0，但计算机只能算出0001+1001=1010 (-2)

这不是我们想要的结果 (╯' - ')╯︵ ┻━┻

另外一个问题，这里有一个（+0）和（-0）

**为了解决“正负相加等于0”的问题，在“原码”的基础上，人们发明了“反码”**

“反码”表示方式是用来处理负数的，符号位置不变，其余位置相反![图片](https://uploader.shimo.im/f/ahlyR8q3slQ9Xnuc.png!thumbnail)

当“原码”变成“反码”时，完美的解决了“正负相加等于0”的问题

过去的（+1）和（-1）相加，变成了0001+1101=1111，刚好反码表示方式中，1111象征-0

人们总是进益求精，历史遗留下来的问题——  有两个零存在，+0 和 -0

**我们希望只有一个0，所以发明了"补码"**，同样是针对"负数"做处理的

"补码"的意思是，从原来"反码"的基础上，补充一个新的代码，（+1）

我们的目标是，没有蛀牙（-0）

![图片](https://uploader.shimo.im/f/QFwSmG12GJgJmE7m.png!thumbnail)

有得必有失，在补一位1的时候，要丢掉最高位

我们要处理"反码"中的"-0",当1111再补上一个1之后，变成了10000，丢掉最高位就是0000，刚好和左边正数的0，完美融合掉了

这样就解决了+0和-0同时存在的问题

另外"正负数相加等于0"的问题，同样得到满足

举例，3和（-3）相加，0011 + 1101 =10000，丢掉最高位，就是0000（0）

同样有失必有得，我们失去了(-0) , 收获了（-8）

以上就是"补码"的存在方式

**结论：保存正负数，不断改进方案后，选择了最好的"补码"方案**

原码 符号位不变 取反 变成反码 

反码 加1丢掉高位 为补码

# 电路  
# 与非门 异或

用电路表示二进制加法

不算进位的话，0   0   ，0    1    ，1  0  ， 1  1  四种情况

0  0 ，1  1   得到 0 

1   0  ，0   1 得到1   

这个结果 和什么逻辑相符合？ **异或**！ 

单个位 不考虑进位的加法已经实现了，那么考虑到进位呢？两个都是1 1 的时候，就会出现进位，那么这就是**与门逻辑**。

# 半加器

![图片](https://uploader.shimo.im/f/lablVhHnKK8ow9Qn.png!thumbnail)

AB输入 S和 C进位。

半加器无法实现后位进位的加法。

# 全加器

![图片](https://uploader.shimo.im/f/kiDvd2rNfiosuEZF.png!thumbnail)

两个半加器 +一个或门

![图片](https://uploader.shimo.im/f/UmYYX5EDEsg2DrQr.png!thumbnail)

# 乘法器

二进制乘法 12*9

![图片](https://uploader.shimo.im/f/1UDsvQn5v9M9TInu.png!thumbnail)

因为二进制只有0 1 ，所以乘法可以用位移和加法来实现。

# 电路并行


# 浮点数和二进制

![图片](https://uploader.shimo.im/f/DzkWet8XOX8jHuq0.png!thumbnail)

浮点数转换成二进制

有效为默认是1.xxx 所以省略

![图片](https://uploader.shimo.im/f/eA2g1TnT0wcAIOCx.png!thumbnail)

Keep multiplying the number after decimal by 2 till it becomes 1.0:

[https://www.h-schmidt.net/FloatConverter/IEEE754.html](https://www.h-schmidt.net/FloatConverter/IEEE754.html)

![图片](https://uploader.shimo.im/f/c7Miavg5j88NabEa.png!thumbnail)

10.101科学计数法

10.101

0 1000 0000  01010000000000000000000

计算9.1 二进制浮点数

![图片](https://uploader.shimo.im/f/z7sle3PjmewtdFbC.png!thumbnail)

9的2进制是1001

0.1的2进制

0.1 *2=0.2    0

0.2*2=0.4    0

0.4*2=0.8    0

0.8*2=1.6     1

0.6*2=1.2      1

0.2*2=0.4    0

0.4*2=0.8    0

0.8*2=1.6     1

0.6*2=1.2      1

0.2*2=0.4    0

...


1001.0001 1001 1001 1001 1001  100 23位截断

科学计数法表示

1.001 0001 1001 1001 ...*2^3

s   0 

1  0010 0011 0011 0011 0011 001~~1 00 ~~

f=0010 **0011 0011 0011 0011 ****001 **

e=127+3=130   1000**0010 **

      

  0 10000010 00100011001100110011010  

    

# 浮点数加法

先对其 再加

指数小的数需要右移，有效位数就会丢失。

注意 对齐是指数位对齐

左右移是有效数位左右移

0.3 + 0.6 

```
0.3(10)二进制表示法
0.3*2=0.6   0
0.6*2=1.2   1
0.2*2=0.4   0
0.4*2=0.8   0
0.8*2=1.6   1
0.6*2=1.2   1
            0
            0
            1
            ...
            
0.0100110011001(非规格化)
s=0
e(指数)=-2
m(有效数)=/*01*/.00110011001
转化为规格化
s   e+127=125 
0 0111 1101 00110011001100110011010
0.6
0.100110011001...
s=0
e=-1
m=1.00110011001...
0 0111 1110 00110011001100110011010
```
对齐 统一成较大的

```
0 0111 1101 0011 0011 0011 0011 0011 010
+
0 0111 1110 0011 0011 0011 0011 0011 010
指数对齐
0 0111 1110 //有效位数右移1位0011 0011 0011 0011 0011 010
0 0111 1110 00011001100110011001101
+
0 0111 1110 00110011001100110011010
------------------------------------------
```

```
0.01001100110011001100110
0.10011001100110011001100


```


0.25 + 0.65

0.25

0.25*2=0.5   0

0.5*2=1.0      1

0.01（非规格化）

规格化

e=-2

m=0000000000

0 01111101 000000000000000000000

0.65

0.65*2=1.3   1

0.3*2=0.6    0

0.6*2=1.2    1

0.2*2=0.4    0

          0.8    0

            1.6       1

                0.6  0

0.1010010100101001

e=-1

m=010010100101001

0 01111101 00000000000000000000

0 01111110 10000000000000000000

0 01111110 01001100110011001100110

0 01111110 11001100110011001100110   0.9






- 0.75

二进制表示


float 32


0.75*2=1.5 1

0.5*2=1.0  1


二进制 未规划

-0.11

规划 

e=-1.1 *2 ^-1  =-1   -1+127=126

1 0111 1110 10000000000000000000

![图片](https://uploader.shimo.im/f/54IVEDwRMEcBiTqn.png!thumbnail)

double

![图片](https://uploader.shimo.im/f/p5HufOxlhl0xFe43.png!thumbnail)


# 存储器
存储介质分类

1.半导体存储器 TTL MOS                     易失

2.次表面存储器 磁头、载磁体

3.磁芯存储器    硬磁材料、环状原件

4.光盘   激光、磁光材料

存取方式分类

1.存取时间与物理地址无关（随机访问）

随机存储器 ROM 可读可写

只读存储器RAM 只能读

2.存取时间与物理地址有关（串行访问）

顺序存取存储器 磁带

直接存取存储器 磁盘

在计算机中的作用分类

主存储器   RAM ROM

Flash Memory 

高速缓冲存储器 cache

辅助存储器

# 主存储器

## 1.基本组成

![图片](https://uploader.shimo.im/f/uPH6u3iZ44NnUoqb.png!thumbnail)

## 2.主存和CPU的联系

![图片](https://uploader.shimo.im/f/wAmyGhO9DgjG33FR.png!thumbnail)

## 3.主存中存储单元地址的分配

 ![图片](https://uploader.shimo.im/f/G1WN2jglI9MlhuyO.png!thumbnail)

## 
## 

## 

## 随机存取存储器

### 静态RAM

1.保存0和1的原理是什么?   双稳态触发器

2.基本单元电路的构成是什么?

3.对单元电路如何读出和写入?

4.典型芯片的结构是很么样子的?

5.静态RAM芯片的如何进行读出和写入操作? 

### 动态RAM

1.保存0和1的原理是什么?    电容（读出的信息与原存的信息是相反）

2.基本单元电路的构成是什么?

3.对单元电路如何读出和写入?

4.典型芯片的结构是很么样子的?

5.动态RAM芯片的如何进行读出和写入操作? 

6.动态RAM为什么要刷新，刷新方法?

静态ram和动态ram

静态RAM（SRAM）速度非常快，只要电源存在内容就不会自动消失。其基本存储电路为6个MOS管组成1位，因此集成度相对较低，功耗也较大。一般高速缓冲存储器用它组成。

  动态RAM（DRAM）的内容在10-3或l0-6秒之后自动消失，因此必须周期性的在内容消失之前进行刷新。由于它的基本存储电路由一个晶体管及一个电容组成，因此它的集成度高，成本较低，另外耗电也少，但它需要一个额外的刷新电路。DRAM运行速度较慢，SRAM比DRAM要快2~5倍，一般，PC机的标准存储器都采用DRAM组成。

## 只读存储器ROM


## 存储器与CPU的链接

![图片](https://uploader.shimo.im/f/MdOcmc0dxVYVtKfs.png!thumbnail)

## ![图片](https://uploader.shimo.im/f/mXsPmruZQ0QX0KaF.png!thumbnail)

## 存储器校验

汉明码

0101 按’偶校验‘ 配置汉明码。

![图片](https://uploader.shimo.im/f/FmRNtwM9cXYqA8j7.png!thumbnail)

![图片](https://uploader.shimo.im/f/Dsys2sKNAPQMYKU2.png!thumbnail)

纠错过程


![图片](https://uploader.shimo.im/f/w4dSzCMHuvTG0Rzb.png!thumbnail)


## IO中断

链式排队器

筛选优先级最高的中断源

![图片](https://uploader.shimo.im/f/UmDrgCTMNK5LoI61.png!thumbnail)

第一个发出中断请求 INTR=1，——INTR=0；

![图片](https://uploader.shimo.im/f/TNNJoDrwlL6mfiXR.png!thumbnail)

第二个发出中断请求

![图片](https://uploader.shimo.im/f/MKKSGaCPDexolFM4.png!thumbnail)

![图片](https://uploader.shimo.im/f/Pkt4VOO4nvbcBsTU.png!thumbnail)

IO中断处理过程

。。。



## DMA

CPU输入输出 CPU要中断，参与传输。

DMA Direct Memory Access，直接存储器访问

进一步把CPU从数据传输中解放。

三种方式：

1.停止CPU访问主存

控制简单

CPU处于不工作状态或保持状态未充分发挥CPU对主存的利用率

2.周期挪用（周期窃取）

DMA访问主存有三种可能

CPU此时不访问

CPU正在访问

CPU和DMA同时访问 （此时CPU总线控制权交给DMA）

![图片](https://uploader.shimo.im/f/AYubGEjRIm0ZPPka.png!thumbnail)

3.DMA与CPU交替访问




DMA工作过程

1.传送过程

预处理 、数据传送、后处理


# 数字
无符号数

有符号数 

机器数与真值

原码表示法

定义：

![图片](https://uploader.shimo.im/f/jriqokzmOZaz32er.png!thumbnail)

![图片](https://uploader.shimo.im/f/fl4NeZpcDCkDB4Yu.png!thumbnail)

![图片](https://uploader.shimo.im/f/rfyKEZ4DLB9DpwgV.png!thumbnail)

![图片](https://uploader.shimo.im/f/ZcnebX5Bs6pzyr13.png!thumbnail)

![图片](https://uploader.shimo.im/f/PyQov0WvtJ9RoQab.png!thumbnail)

补码表示法


![图片](https://uploader.shimo.im/f/pMRsVT5ZVNWozDhb.png!thumbnail)

	一个负数可用它的正补数来代替，而这个**正补数可以用模加上负数本身**来得到。

	一个正数和一个负数互为补数时，两数的绝对值之和为模

	正数的补数为其自身。


原码-》补码 符号位不变  按位取反末尾+1 来源推导：

![图片](https://uploader.shimo.im/f/GJt8kZ536KvCsZdb.png!thumbnail)

补码-》原码 符号位不变 按位取反末尾+1 

![图片](https://uploader.shimo.im/f/uPygrmqu7ojlPXV0.png!thumbnail)

![图片](https://uploader.shimo.im/f/XlfX5src2d1B6JBo.png!thumbnail)

补码好处是将减法转换成加法

反码表示法

![图片](https://uploader.shimo.im/f/8jZR618re2YMvikQ.png!thumbnail)

移码表示法:





## 定点表示和浮点表示

### 定点表示



### 浮点表示

![图片](https://uploader.shimo.im/f/UmdrfL6SxZRTetVH.png!thumbnail)



### 加减法

![图片](https://uploader.shimo.im/f/GOyRFUtM3HWl7HGG.png!thumbnail)


![图片](https://uploader.shimo.im/f/AM3Ue1B5GRMzaNiT.png!thumbnail)

### 溢出判断

![图片](https://uploader.shimo.im/f/KYOddShRoTJR85uT.png!thumbnail)

### 乘法运算





你吃饭吃到一半，电话来了，你一直到吃完了以后才去接，这就说明你不支持并发也不支持并行。

你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你支持并发。

你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你支持并行。

并发的关键是你有处理多个任务的能力，不一定要同时。

并行的关键是你有同时处理多个任务的能力。所以我认为它们最关键的点就是：是否是『同时』。



