---
title: "something_about_msc_51"
date: 2024-02-29T15:03:40Z
# weight: 1
# aliases: ["/first"]
tags: ["Embedded","8051"]
author: "IAKSH"
# author: ["Me", "You"] # multiple authors
showToc: false
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "比较早期的一篇文章，可能存在问题"
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


# 0x00 | 历史
`MCS-51`架构是Intel搞的东西，就和今天的所谓”架构“一样，实际上是一组指令集和一些硬件标准，用在了自家的`Intel 8051`，`Intel 8751`等等芯片上。
不止`MCS-51`，Intel还搞出过`MCS-48`，`MCS-96`。
基于`MCS-51`架构的`Intel 8051`设计简单，使用方便，价格低廉，功耗（在当时）较低，受到一众吹捧，美国各高校也开始将其列入计算机课程。当然也少不了来自世界各地的效仿者。

# 0x01 | 现状
~~Intel 已经不再设计和生产单片机了~~，但是在这之前 Intel 疯狂售卖`MCS-51`架构的授权。一些拿到这一授权的公司就开始生产并修改自己的兼容`MCS-51`架构下程序的单片机，比如中国台湾的STC宏晶，ATMEL。
虽然社会上对`MCS-51`架构单片机的需求早已不复从前，`MCS-51`架构在各高校中还是有一些市场的。时至今日，STC和ATMEL等当年的一些`MCS-51`授权厂也依然在生产基于或兼容`MCS-51`架构的单片机。
目前全球最大，且可能是唯一在`MCS-51`架构上梭哈的就是STC宏晶，对于国内大学生而言其最广为人知的产品应该是`STC89C52RC`。
值得一提的是，STC公司不仅坚持传统的`MCS-51`单片机，还在此基础上研发出了32位的`MCS-51`指令集兼容内核。

# 0x02 | 硬件资源
由于各授权厂普遍存在修改`MCS-51`架构的情况，所以目前能买到的”51单片机“硬件资源只会比 Intel 的`MCS-51`架构下的更多。
而对于使用`MCS-51`架构的`Intel 8051`，其片上拥有：
+ 8位总线
+ 4KB内部储存，最大可扩充到64KB
+ 128Bytes运行内存，最大可扩充到64KB
+ 4组IO口（P0,P1,P2,P3）
+ 2组16位计时器（T0，T1）
+ 5个中断源（INT0，INT1，T0，T1，RXD，TXD）
+ 1组全双工串口

对于使用`MCS-51`架构的`Intel 8052`，其片上拥有：
+ 8位总线
+ 8KB储存，最大可扩充到64KB
+ 256Bytes运行内存，最大可扩充到64KB
+ 4组IO口（P0,P1,P2,P3）
+ 3组16位计时器（T0,T1,T2）
+ 6个中断源（INT0,INT1,T0,T1,T2,RXD,TXD）
+ 1组全双工串口

# 0x03 | 软件生态
由于`MCS-51`架构曾经的辉煌，以及其目前在各个高校中的地位，时至今日依然有不少`MCS-51`内核单片机的开发工具和其他软件依然在保持维护。
对于编译器，有：
+ SDCC：一个开源的C语言编译器，可编译出运行在`MCS-51`架构单片机和其他一些小型设备上的程序。该编译器同时提供了相关的头文件和程序打包工具。
+ MDK Keil C51：一套闭源的商业软件，是`MDK Keil`集成开发环境的一部分，提供了对`MCS-51`架构单片机的C语言编译器和仿真软件。

有关`MCS-51`的各种外设代码在互联网上很容易找到，不过其中大部分是使用的是 `MDK Keil C51`。