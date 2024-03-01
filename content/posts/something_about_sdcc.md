---
title: "something_about_sdcc"
date: 2024-02-29T15:02:48Z
# weight: 1
# aliases: ["/first"]
tags: ["Embedded"]
author: "IAKSH"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "SDCC随记"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
editPost:
    URL: "https://github.com/IAKSH/iaksh.github.io/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

# 0x00 | Keil C51和SDCC
+ Keil C51:
	+ 闭源的商业软件
	+ 自带（绑定）IDE
	+ IDE提供了仿真环境
	+ 生成的程序体积较小，汇编质量较高
	+ 网络资料较多
	+ 只要能接受那个IDE，门槛较低
+ SDCC:
	+ 免费的开源软件
	+ 更接近标准C语言
	+ 生成的程序体积较大，汇编质量一般
	+ 不止能给8051写程序
	+ 网络资料较少
	+ 有一点点的门槛（需要自己配置环境和工作流

# 0x01 |  从Keil C51迁移到SDCC
sdcc 更加接近标准C编译器，对于不属于标准C语言的特性，sdcc 在其标识符前加上两个下划线进行标识。
sdcc中没有`sbit`和`bit`这样的数据类型（但实际上有__sbit）。在Keil C51中常见的一些定义语句在SDCC中需要使用宏实现。
```C
sbit SDA = P1 ^ 5;
sbit CLK_ST = P1 ^ 6;
```
在 sdcc 中：
```C
#define SDA P1_5
#define CLK_ST P1_6

// 也可以使用__sbit，不过可能不是你想的那样
__sbit __at (0x90) P1_0 ;
```
另外，Keil C51中的`interrupt`关键字也被改为了`__interrupt`，在Keil C51中可以这样定义一个中断函数：
```C
void my_interrupt_func()  interrupt 4
{
	// ...
}
```
而在 sdcc 中需要这样：
```C
void my_interrupt_func()  __interrupt 4
{
	// ...
}
```
最后，sdcc 只支持以`__asm`开头，`__endasm`结尾的内联汇编。所以对于在Keil C51中常见的`_nop_()`，在 sdcc 中需要使用宏对其进行定义：
```C
#define _nop_() __asm NOP __endasm
// ...
_nop_()
```
当然，你也可以直接写_`_asm` ... `__endasm`段。

# 0x03 | packihx
sdcc 在默认参数配置下会把源文件编译出一堆东西，很不幸里面没有`.hex`。但是可以用 sdcc 提供的`packihx`工具把`.ihx`转换为`Intel HEX`格式的`.hex`。
（指令懒得写了，直接把路径接后面）（大概，没有测试，因为我用pio，不用自己弄（x