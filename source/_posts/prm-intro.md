---
title: PRM文件详解
date: 2016-02-22
tags: mcu
---

## PRM文件中内存划分的方式

### 一. 由"SEGMENTS"开始到"END"为止，中间可以添加任意多行内存划分的定义，每一行用分号";"结尾。

定义行的语法型式为：`[块名] = [属性] [起始地址] TO [结束地址]`;
其中，

1. "块名"的定义和C语言变量定义相同，是以英文字母开头的一个字符串。

2. "属性"可以有三种不同的类型。对于只读的Flash-ROM区属性一定是"READ_ONLY"，对于可读写的RAM区属性可以是"READ_WRITE"，也可以是"NO_INIT"。它们两者关键区别是ANSI-C的初始化代码会把定位"READ_WRITE"块中的所有全局和静态变量自动清零，而"NO_INIT"块中的变量将不会被自动清零。对于单片机系统，变量在复位时不被自动清零这一特性有时是很关键的。

3. 起始地址和结束地址决定了一内存块的物理位置，用16进制表示。

4. 如要划分一个全局地址空间则：在地址后面加单引号和G表示全局地址
    如：**EEE_D_FLASH  =READ_ONLY 0x107400'G TO 0x107FFF'G;**

用"SEGMENTS"只是从单片机的物理内存这一角度对其进行空间划分。源程序本身并不知道物理内存被分割和属性定义的这些细节。它们两者之间必须通过下面的"PLACEMENT"建立联系。

### 二. 程序段和数据段的放置

"PLACEMENT － END"内所描述的信息是告诉连接器源程序中所定义的各类段应该被具体放置到哪一个内存块中去。其语法型式为：`[段名1], [段名2],... [段名n] INTO [内存块名]`

其中 

1. 段名就是在源程序中用` "#pragma" `声明的数据段、常数段或代码段的名字。如果用缺省名"DEFAULT ", 则默认的数据段名为DEFAULT_RAM，代码段和常数段名为DEFAULT_ROM。若程序中定义的段名没有在PLACEMENT 中提及，则将被视同为DEFAULT。几个相同性质但不同名字的段可以被放置到同一个内存块中，相互之间用逗号","分隔。 INTO 是系统保留的关键词，在这里为"放入"的意思。

2. 内存块名就是前面介绍的用"SEGMENTS"划分好的不同的内存块名字。利用这样直观的定位描述文本可以方便灵活的将你的数据或代码定位到芯片内存任意可能的位置，实现某些特殊目的的应用。下面举几个例子，注意各种段名、PLACEMENT和SEGMENTS 之间的对应关系。

## 数据在不同地址空间上的访问 

-----

下面将介绍如何用HCS12X的编译器去进行不同的数据访问，包括以下几个部分：

* 分配在固定地址空间的变量
* 分配在拓展地址空间的变量
* 分配在banked寻址空间的变量-- Logical Addressing
* 分配在banked寻址空间的变量-- Global Addressing
* 分配常量
* Logical Addressing 和 Global Addressing 的对比
* 转换地址


**注意**：
	下面的内容只适用在SMALL和BANKED内存模式，对于LARGE模式不适用；对于小于32k的代码建议用SMALL，大于的用BANKED。


### 一. 分配在固定地址空间的变量

1. 定义变量
	
```		#pragma DATA_SEG __SHORT_SEG MyShortData
		unsigned char rub_short_var;
		#pragma DATA_SEG DEFAULT
		```

2. 声明变量

```		#pragma DATA_SEG __SHORT_SEG MyShortData
		extern unsigned char rub_short_var;
		#pragma DATA_SEG DEFAULT```
3. 指针申明和使用

		```unsigned char* ptr_on_short_var;
								ptr_on_short_var = &rub_short_var;
								(*ptr_on_short_var)++;```
4.对于定义在__SHORT_SEG段的变量，SEGMENT和PLACEMENT的设置如下：

```
		SEGMENTS
			DIRECT_PAGE 	= READ_WRTIE 0X2010  	TO  	0X20FF;
		....
		END
		PLACEMENT 
				MyShortData			INTO  	DIRECT_PAGE;
		....
		END
```

**直接地址区域的配置：**

在HCS12XE系列中，固定页可以移动，编译器只需如下配置：

1. 编译器和汇编器选项中得加 -CpDirect选项，比如：如果DIRECT寄存器被初始化为0x20，那么Direct窗口就是0x2000到0x20FF，那么编译器和汇编器选项就是-CpDirect0x2000，把这个加入到编译器和汇编器的设置当中；
2. DIRECT寄存器必须被用户代码初始化，默认代码初始化不了DIRECT;
3. 在PRM文件中，MyShortData段必须得被这块区域分配，即0x2000~0x20FF;
4. 对于错误的固定地址分配是没有错误提示的，所以必须小心。


### 二. 分配在拓展地址空间的变量

1. 定义变量
		
		```unsigned char rub_var;```
2. 声明变量
		
		```extern unsigned char rub_var;```
3.使用变量
		
		```rub_var = 7；```
3. 指针申明和使用
		
		```unsigned char* ptr_on_var;
								ptr_on_var = &rub_var;
								(*ptr_on_var)++;```
4. 对于定义在DEFAULT_RAM段的变量，SEGMENT和PLACEMENT的设置如下：
		```
								SEGMENTS
									RAM 	= READ_WRTIE 		0X2100  	TO  	0X3FFF;
									...
								END
								PLACEMENT 
									DEFAULT_RAM			INTO  			RAM;
									...
								END```


### 三. 分配在banked寻址空间的变量

分配在banked寻址空间的变量可以用Logical Addresses(RPAGE)或者Global Addresses

##### 用logical Addresses时，需要以下步骤：

1. 定义变量

		```#pragma DATA_SEG __RPAGE_SEG PAGED_RAM
								unsigned char rub_far_var;
								#pragma DATA_SEG DEFAULT```
2. 声明变量

		```#pragma DATA_SEG __RPAGE_SEG PAGED_RAM
								extern unsigned char rub_far_var;
								#pragma DATA_SEG DEFAULT```

3. 使用变量
		```rub_far_var = 5;```
4.	指针申明和使用
		
		```unsigned char * __rptr ptr_on_far_var;
						  		ptr_on_far_var = &rub_far_var;
								(*ptr_on_far_var)++;```
5. 对于定义在RPAGE段的变量，SEGMENT和PLACEMENT的设置如下：
		
		```SEGMENTS
									RAM_FB  = READ_WRTIE 0xFB1000 TO 0xFB1FFF;
									RAM_FC  = READ_WRTIE 0xFC1000 TO 0xFC1FFF;
									RAM_FD  = READ_WRTIE 0xFD1000 TO 0xFD1FFF;
									...
								END
								PLACEMENT 
									PAGED_RAM			INTO  	RAM_FB, RAM_FC, RAM_FD;
									...
								END```

**NOTES:** 定义在RPAGE中的变量也可以用far指针来访问，比如 `unsigned char * __far ptr_on_far_var`, 在这种情况下，访问指针指向的对象将采用全局寻址模式。


##### Global Addresses

用global addresses的主要原因是对象也许塞不进一个Logical Addresses, 用global addresses时，小于内存大小或者小于64k的对象都可以被访问到。
global addresses被指针（== __far指针）用来指向任意对象，不需要任何条件。

1. 定义变量
		```
								#pragma DATA_SEG __GPAGE_SEG PAGED_RAM
								unsigned char rub_far_var;
								#pragma DATA_SEG DEFAULT```
2. 声明变量
		```
								#pragma DATA_SEG __GPAGE_SEG PAGED_RAM
								extern unsigned char rub_far_var;
								#pragma DATA_SEG DEFAULT```
3. 使用变量
		
	```	rub_far_var = 7;```
4. 指针申明和使用
		
		```unsigned char * __far ptr_on_far_var;
								ptr_on_far_var = &rub_far_var;
								(*ptr_on_far_var)++;```
5. 对于定义在GPAGE段的变量，SEGMENT和PLACEMENT的设置如下：

		```SEGMENTS
										RAM_BANKED 		= NO_INIT   	0xF9000'G  TO 0xFCFFF'G;
								END
								PLACEMENT 
									PAGED_RAM			INTO  	RAM_BANKED;
								END```



### 四. 分配常量

常量可以放在EEPROM或者FLASH中，在EEPROM中可以用EPAGE访问，在FLASH中可以用PPAGE访问，也可以用global addresses访问 

##### 在EEPROM中用logical addresses 

1. 定义常量
		```
								#pragma CONST_SEG __EPAGE_SEG PAGED_CONST
								const unsigned char cub_far_const = 1;
								#pragma CONST_SEG DEFAULT```
2.	声明常量
		```
								#pragma CONST_SEG __EPAGE_SEG PAGED_CONST
								extern const unsigned char cub_far_const=1;
								#pragma CONST_SEG DEFAULT```
4.	指针申明和使用
		
		```const unsigned char * __eptr ptr_on_far_const;```


**注意**：也可以用far指针来访问在EPAGE段的常量，`const unsigned char * __far ptr_on_far_const;`

##### 在flash中用logical addresses

只有当代码没有在任何paged区域时，在flash中用logical addresses 访问常量才可以，所以不建议用这种方式，建议用global addressing来访问在flash中的所有对象。

##### 用global addresses

1.	定义常量

```
		#pragma CONST_SEG __GPAGE_SEG PAGED_CONST
		const unsigned char cub_far_const=1;
		#pragma CONST_SEG DEFAULT
```
2.	声明常量
		
		```#pragma CONST_SEG __GPAGE_SEG PAGED_CONST
								extern const unsigned char cub_far_const = 1;
								#pragma CONST_SEG DEFAULT```
3. 指针申明和使用
		
		```const unsigned char * __far ptr_on_far_const;```


**注意**：

1. 要在complier的option中加入-D__FAR_DATA
2. 所有定义的指针的地址在DEFAULT_ROM，但是指向的地址是banked区域的。都占3个byte(需要确认)
3. global address方式没有local address执行指令快，除非需要寻址空间大，最好不要使用global address.
4. 以上针对HCS12X系列CPU，HCS12没有global address的说法。


### 五. Logical Addresses 和 Global Addresses对比

* 对于小于4k（Banked RAM窗口大小）的变量，用logical addresses比用global addresses效率更高；
* 对于大于4k的变量，链接器就必须过界分配，所以建议用global addresses;
* 建议用global addresses来访问分配在flash中的常量和字符串常量；
对于所有的对象建议用logical addresses, 包括栈，代码，直接变量，拓展变量，I/O寄存器。
* 为了定义一个paged变量，必须用DATA_SEG pragma， 不要用__far，far关键字只是让编译器知道如何访问变量，但是不改变变量分配的地方。


### 六. 转换地址，(logical to glbal, ...)

如果要将__rptr指针转换到__far指针，在datapage.c中有可用来转换地址的运行函数。

**代码示例：**

```
	char data;
	volatile char temp;

	void func1(void)
	{
		char *__far ptr;
		char *__rptr rptr;

		ptr = &data;  //global address
		rptr = ptr;   //coverting global to logical
	
		temp = *rptr;
	}
```

### 七. 用logical addresses定义一个变量，用global addresses访问



即使一个对象在__RPAGE, __EPAGE或者__SHORT段被定义，这个对象也可以用far指针来访问。

**代码示例：**
```
		#pragma DATA_SEG __RPAGE_SEG PAGED_RAM
		char data[10] = {
			0x10, 0x20, 0x30, 0x40, 0x50
		};
		#pragma DATA_SEG DEFAULT

		volatile char tmep;

		voild func1(void)
		{
			char *__far ptr;
			ptr = data;
			temp = *ptr;
		}
```

#### 附：far指针介绍 

1. 使用far指针的目的在于不同page间的访问，可以在当前ROM区通过3字节的far指针去访问其他paged区的数据
2. far指针使用方法：(只举例RAM访问)

应用场景：在相应的paged RAM定义了一个变量或数组，要在DEFAULT_RAM去访问这些数据：

**步骤：**

1. 在paged RAM区定义一个变量或数组，如下：
		```
								#pragma push    /* 保存先前的编译器配置，pop为恢复编译器配置（比如优化等级改变之类）*/
								#pragma CONST_SEG __GPAGE_SEG GLOBAL_FLASH 
								static unsigned char Fontmap[96];
								static unsigned char var;
								#pragma pop     ```
定义好这些数据，在程序的任何地方都可以直接使用它们，比如：Fontmap[0] = 0xffu;
但是，如果我们要使用指针去处理这些数据就要用到far指针。

2. 我们可以在任何地方定义far指针，比如说：在某一函数定义一个far指针，
```
		unsigned char * __far ptr_far;
		ptr_far = Fontmap;			/* 不需要强转 */```
这样直接对这个far指针操作就可以了。

最后，需要注意的是：

* far指针不用定义在被访问的paged RAM，应该定义在访问的地方。
* far指针与非far指针赋值，最好显性类型转换。

