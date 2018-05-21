---
layout: post
title: 交换机专栏的组织形式及内容
categories: SWITCH
description: 交换机专栏的内容组织形式和主要内容概览
keywords: SWITCH
---

交换机专栏主要涉及两个内容，使用交换机搭建小型园区网络、交换机内部原理。使用交换机搭建小型网络可以帮助你从更直观的角度理解TCP/IP协议栈以及应用，同时，你像常见的一些网络概念 IP 、Mask 、 GateWay 、 DNS 等有一个直观且全面的理解。交换机内部原理篇则重点关注交换机内部的硬件、软件设计，给一些爱好者解开传统交换机的神秘面纱，也给爱好者们提供一些入门思路。


### 交换机应用篇
	
交换机应用这一主题我计划使用一个校园网（园区网）作为演示案例，该网络使用3层架构：接入层（Access）、汇聚层（Convergence）、核心层（Core）。当时我在的学校的校园网用户大约7000人左右，整个网络运营管理是几个核心的老师和学生进行的，从那时候开始对网络构架和网络设备感兴趣。当然，我不可能使用真实的设备进行搭建演示环境（毕竟我是穷人一枚），选择使用[GNS3](https://www.gns3.com/)这个网络仿真软件，开源的[GNS3 git地址](https://github.com/GNS3)，有较强的可扩展性。这样，基本思路就清晰了，使用GNS3这样的网络拓扑仿真软件来模拟一个实际的校园网，用于说明现有的校园网基本架构和设计到的网络概念。

### 交换机研发篇

交换机研发篇，计划从交换机设计的两个大的方面入手：交换机硬件、交换机固件（底层固件和上层的协议软件）。计划从以下几个层面进行说明：硬件（CPU、Switch IC）、底层固件（BootLoader、OS、Switch SDK）、协议层。使用的具体内容，做Switch IC 和PHY IC的也就那么几家，我这里使用Broadcom的IC及开发SDK，上层的应用协议使用开源的[Quagga](http://www.nongnu.org/quagga/), [Quagga Git 地址](https://github.com/opensourcerouting/quagga)。

### 关于一些版权和License问题

交换机的硬件主要核心为交换IC，大部分都涉及版权问题。本系列博文尽量在不涉及侵权的情况下，屡清楚交换机的基本设计。

### 引用资源

[GNS3](https://www.gns3.com/)

[GNS3 git地址](https://github.com/GNS3)

[Quagga](http://www.nongnu.org/quagga/)

[Quagga Git 地址](https://github.com/opensourcerouting/quagga)

此交换机系列同步发布到[bahutou's blog](http://bahutou.cn) 和[CSDN的交换机专栏](https://blog.csdn.net/column/details/21343.html)。



