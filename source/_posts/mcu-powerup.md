---
title: 单片机上电启动流程及相关配置详解
date: 2016-02-19
tags: mcu
---

## 单片机上电后启动流程

单片机执行程序的过程，实际上就是执行所编程序的过程，即逐条指令的过程。
执行一条指令都可分为三个阶段进行，即`取指令---分析指令---执行指令`。 
取指令的任务是：根据程序计数器PC中的值从程序存储器读出现行指令，送到指令寄存器。

<!-- more --> 

分析指令阶段的任务是：将指令寄存器中的指令操作码取出后进行译码，分析其指令性质。如指令要求操作数，则寻找操作数地址。


单片机执行程序的过程实际上就是逐条指令地重复上述操作过程，直至遇到停机指令、可循环等待指令。 


单片机中的程序一般事先我们都已通过写入器固化在片内或片外程序存储器中，因而一开机即可执行指令。

 
下面用一个实例来说明指令的执行过程。


开机时，程序计数器PC变为0000H。然后单片机在时序电路作用下自动进入执行程序过程。执行过程实际上就是取出指令和执行指令的循环过程。 

　　
例如执行指令：MOV A,#0E0H，其机器码为“74H E0H”，该指令的功能是把操作数E0H送入累加器，0000H单元中已存放74H（该命令代表向A累加器写入立即数），0001H单元中已存放E0H。


当单片机开始运行时，首先是进入`取指阶段`，其次序是：

1. PC的内容（这时是0000H）送到地址寄存器；　　　
2. PC的内容自动加1（变为0001H）； 
3. 地址寄存器的内容（0000H）通过内部地址总线送到存储器，以存储器中地址译码电跟，使地址为0000H的单元被选中； 
4. CPU使读控制线有效； 
5. 在读命令控制下被选中存储器单元的内容（此时应为74H）送到内部数据总线上，因为是取指阶段，所以该内容通过数据总线被送到指令寄存器。 


至此，取指阶段完成，进入译码分析和执行指令阶段。 

由于本次进入指令寄存器中的内容是74H（操作码），以译码器译码后单片机就会知道该指令是要将一个数送到A累加器，而该数是在这个代码的下一个存储单元。所以，执行该指令还必须把数据（E0H）从存储器中取出送到CPU，即还要在存储器中取第二个字节。其过程与取指阶段很相似，只是此时PC已为0001H。指令译码器结合时序部件，产生74H操作码的微操作系列，使数字E0H从0001H单元取出。因为指令是要求把取得的数送到A累加器，所以取出的数字经内部数据总线进入A累加器，而不是进入指令寄存器。

至此，一条指令的执行完毕。

单片机中PC=0002H，PC在CPU每次向存储器取指或取数时自动加1，单片机又进入下一取指阶段。这一过程一直重复下去，直至收到暂停指令或循环等待指令暂停。CPU就是这样一条一条地执行指令，完成所有规定的功能。


## 单片机上电后ROM与RAM执行流程

**简单介绍**

`ROM：(Read Only Memory)`
在单片机中用来存储程序数据及常量数据或变量数据，凡是c文件及h文件中所有代码、全局变量、局部变量、常量数据、startup.asm文件中的代码(类似ARM中的bootloader或者X86中的BIOS，一些低端的单片机是没有这个的)通通都存储在ROM中。

`RAM：(Random Access Memory)`
用来存储程序中用到的变量。凡是整个程序中，所用到的需要被改写的量，都存储在RAM中，“被改变的量”包括全局变量、局部变量、堆栈段。

程序经过编译、汇编、链接后，生成hex文件。用专用的烧录软件，通过烧录器将hex文件烧录到ROM中。因此，这个时候的ROM中，包含所有的程序内容：无论是一行一行的程序代码，函数中用到的局部变量，头文件中所声明的全局变量，const声明的只读常量，都被生成了二进制数据，包含在hex文件中，全部烧录到了ROM里面。

此时的ROM，包含了程序的所有信息，正是由于这些信息，“指导”了CPU的所有动作。

ROM中包含所有的程序内容，在MCU上电后，CPU开始从第1行代码处执行指令。这里所做的工作是为整个程序的顺利运行做好准备，或者说是对RAM的初始化，工作任务有几项：

* 为全局变量分配地址空间

如果全局变量已赋初值，则将初始值从ROM中拷贝到RAM中，如果没有赋初值，则这个全局变量所对应的地址下的初值为0或者是不确定的。当然，如果已经指定了变量的地址空间，则直接定位到对应的地址就行，那么这里分配地址及定位地址的任务由“连接器”完成。

* 设置堆栈段的长度及地址

用C语言开发的单片机程序里面，普遍都没有涉及到堆栈段长度的设置，但这不意味着不用设置。堆栈段主要是用来在中断处理时起“保存现场”及“现场还原”的作用，其重要性不言而喻。而这么重要的内容，也包含在了编译器预设的内容里面，确实省事，可并不一定省心。

* 分配数据段data，常量段const，代码段code的起始地址

代码段与常量段的地址可以不管，它们都是固定在ROM里面的，无论它们怎么排列，都不会对程序产生影响。但是数据段的地址就必须得关心。数据段的数据时要从ROM拷贝到RAM中去的，而在RAM中，既有数据段data,也有堆栈段stack，还有通用的工作寄存器组。通常，工作寄存器组的地址是固定的，这就要求在绝对定址数据段时，不能使数据段覆盖所有的工作寄存器组的地址。

**注意：** 这里所说的“第一行代码处”，并不一定是你自己写的程序代码，绝大部分都是编译器代劳的，或者是编译器自带的demo程序文件。高级一点的单片机，这些内容，都是在startup的文件里面。

通常的做法是：普通的flash MCU是在上电时或复位时，PC指针里面的存放的是“0000”，表示CPU从ROM的0000地址开始执行指令，在该地址处放一条跳转指令，使程序跳转到_main函数中，然后根据不同的指令，一条一条的执行。

当中断发生时(中断数量也很有限，2~5个中断)，按照系统分配的中断向量表地址，在中断向量里面，放置一条跳转到中断服务程序的指令，这样，整个程序就跑起来了。

### I/O口寄存器：

可以被改变的量，它被安排在一个特别的RAM地址，为系统所访问，而不能将其他变量定义在这些位置。

### 中断向量表：

中断向量表是被固定在MCU内部的ROM地址中，不同的地址对应不同的中断。每次中断产生时，直接调用对应的中断服务子程序，将程序的入口地址放在中断向量表中。





## CodeWarrior执行步骤

    
1. 初始化堆栈，堆栈是通过编译参数的传递过来的；
2. 初始化内存，将内存清零，从ROM区复制数据到特定的内存区；
3. 调用main函数


CW创建的工程包括以下几个部分：

* Sources文件夹下面放的是源代码，mian.c为主函数所在文件，若要新建源文件也要放在此文件夹下面。datapage.c文件为数据页面配置文件，一般写程序用不到，与编译有关，不可删除。
* Startup Code文件夹下面的Start12.c文件是开发环境编译时默认的最先执行的一段程序，在Start12.c的最后调用main.c的主函数。在Start12.c中，创建工程所要注意的是对地址的重分配映射。
* Linker Files文件夹下面的prm文件里面是关于整个工程在编译连接时的一些规则设置，在此文件中，要注意的是RAM和ROM的地址定义和堆栈大小。



### 使用监控程序进行程序烧写调试的方法说明

对单片机进行调试、程序烧写可以有两种方式:
    
* 用BDM通过专门接口进行
* 利用监控程序通过串口进行

BDM方式是芯片厂商提供的调试、烧写方式，在MCU上留有管脚，通过仿真器将目标板和PC相连，PC通过Hiwave程序将程序通过仿真器烧写到MCU中。BDM的一大特点是需要专门的仿真器和开发环境，在没有仿真器或者Hiwave程序时就无法进行程序的烧写。

另一种方式是利用监控程序通过串口进行程序烧写的方式，不用专门的调试器，不用专门的程序就可以进行程序的烧写。

**原理：**

通过串口烧写程序就是用已经烧写在单片机内部的一小段程序，通过异步串行接口与PC机通信，把得到的程序数据写到MCU的Flash中。

已烧写进MCU的程序称为监控程序(官方提供)在提供开发板之前已用BDM烧写进MCU内。

由于监控程序的存在，单片机一些存储空间被占用，中断向量表的位置也要改变。

已有监控程序的单片机在运行时，首先运行的程序就是监控程序，监控程序首先会等待3秒，同时查询串口是否接收到任何数据，若接收到数据，就进入调试状态，通过指令进行程序烧写、调试。若3秒内没有从串口上接收到数据，就去执行用户烧写到MCU内的程序。


使用CW进行程序编写时的**注意事项：**

由于使用了监控程序，在进行程序编写时就要比平常多注意两个方面的东西，一是对整个程序存储空间的定义，一是中断向量的偏移。

**程序存储空间的定义：**

监控程序占用了`$F000~$FEFF`的存储空间，并且通过串口烧写程序时对此存储空间进行了写保护，故用户在写程序时要注意必须将程序编译连接至此存储空间中。为了解决此问题，要在prm文件中进行改动，如下：
`ROM_C000  =  READ_ONLY 0xC000  TO  0xEEFF;`

中断向量表偏移的处理：

监控程序将中断向量表从$FF00~$FFFF偏移至$EF00~$EFFF, 即用户的程序入口和中断程序的向量号需要改变。

程序入口的改变在prm文件中改动，如下：
`VECTOR 0 _Startup`   改为   `VECTOR 2048  _Startup`
各中断程序的向量号，需要在原来的基础上加上2048。



`一般情况下Start12.c中的设置(利用监控程序通过串口进行)`

在创建的CW工程中，Start12.c文件中没有对各存储单元进行地址重分配，造成了地址的重叠，为了解决这个问题，需要在Start12.c中进行一些改动，把各存储单元进行地址重映射，充分利用各存储单元。具体改变如下：

第118行：
```
	#ifdef _HCS12_SERIALMON
   	   /* for Monitor based software remap the RAM & EEPROM to adhere
     	    to EB386. Edit RAM and EEPROM sections in PRM file to match 	these. */
	#define ___INITRM      (*(volatile unsigned char *) 0x0010)
	#define ___INITRG      (*(volatile unsigned char *) 0x0011)
	#define ___INITEE      (*(volatile unsigned char *) 0x0012)
	#endif
```
注释掉#ifdef和#endif开头的两行。416行改动一样。

**prm文件的设置(利用监控程序通过串口进行)**

在prm文件中，由于在Start12.c中进行了地址的重映射，所以要改动RAM和ROM的地址定义。另外，如果编写的程序需要较大的栈（比如多重的程序调用，中断嵌套），还要改变堆栈的大小。
ROM的地址定义在一般情况下不用改变，但在使用监控程序进行程序下载、调试时，由于单片机里面本身已经含有一段监控程序，并且监控程序在烧写用户程序是对自己所占据的地址空间是自我保护的，所以在使用监控程序时要在prm中改动ROM的地址定义使用户程序不与监控程序冲突。
    
RAM的地址定义：
    `RAM = READ_WRITE 0x2000  TO 0x3FFF;`

若要改变堆栈的大小：

将STACKSIZE 0x100后面的0x100改为所需大小。

经过以上步骤，就可以建立一个可以使用的工程框架，就可以进行各用户的程序编写了。



### 在CW中指定堆栈大小及堆栈指针


#### CodeWarrior自动生成的工程中堆栈指针的初始化机制

打开CodeWarrior中的一个新建工程，在Start12.c文件中可以看到语句
“`INIT_SP_FROM_STARTUP_DESC()`”，
这是系统自动生成对堆栈指针的初始化语句。

这条语句是个宏函数，具体为
`#define INIT_SP_FROM_STARTUP_DESC() __asm LDS #__SEG_END_SSTACK;`
其中__SEG_END_SSTACK是由编译器根据配置文件自动生成的，从而达到按照用户要求配置堆栈指针的目的。

#### 几种不同的堆栈的内存分布及配置方法


* RAM区的开始是堆栈区，随后是数据区

这时堆栈区的大小由prm文件中STACKSIZE参数指定，而SP的具体值由编译器根据指定的堆栈区大小计算得出。

这个内存分布是新建工程的默认配置，是由prm文件中PLACEMENT区语句：
“`SSTACK, DEFAULT_RAM INTO RAM;`”决定的。

当把语句换成：
“`DEFAULT_RAM, SSTACK INTO RAM;`”时，RAM区的分布会变为开始是数据区，随后才是堆栈区。

而且，当不指定SSTACK在内存中分布时，即语句变为：
“`DEFAULT_RAM INTO RAM;`”时，RAM区的分布也会变为开始是数据区，随后才是堆栈区.


* 堆栈区在RAM区任意位置

实现这种分布有两种方法：

I. 利用prm文件

这时SP的值由prm文件中STACKTOP参数指定（需要用户自己添加），堆栈区的大小也需要在prm文件中自己指定，例子如下：
```
	SEGMENTS
	MY_STK = NO_INIT 0xB00 TO 0xBFF;
	MY_RAM = READ_WRITE 0xA00 TO 0xAFF;
	MY_ROM = READ_ONLY 0x800 TO 0x9FF;
	END
	PLACEMENT
	DEFAULT_ROM INTO MY_ROM;
	DEFAULT_RAM INTO MY_RAM;
	SSTACK INTO MY_STK;
	END
	STACKTOP 0xB7E
```
本例中堆栈区大小为从0xB7E到0xB00，SP的值为0xB7E。

注：在并不关注堆栈区的位置而仅仅关注堆栈区大小的情况下，CodeWarrior推荐用STACKSIZE的方式定义堆栈，即此时不推荐用STACKTOP的方式。

II. 在start12.c文件中自己指定

在函数_Startup的开始用语句：`__asm LDS #VAR;`(VAR为自己指定的SP的值)来指定SP的值，堆栈区的大小由prm文件中STACKSIZE参数指定。