---
layout: post
title: RTOS-硬件运行环境和μCOS版本说明
categories: RTOS-μCOS
description: 本系列文章使用的硬件平台和μCOS版本
keywords: RTOS μCOS
---

本系列文章中使用的软件运行环境：硬件，QEMU(STM32)仿真; 软件，μCOS-III(V3.03.01)。


# 运行环境说明

为良好地说明RTOS的方方面面，必须上手亲自调试代码。为了减少CPU架构和指令集方面的理解负担，我们使用RISC指令集的CPU。基于ARM Cortex-M3处理器架构的STM32系列Soc在低功耗嵌入式领域有着比较广泛的应用。因此，我们选择这款Soc作为其硬件平台。考虑到实验的方便性和有些初学者手里还没有STM32的开发板等原因，我们使用QEMU软件仿真一个基于STM32 Soc的开发板[STM32-P103](https://www.olimex.com/Products/ARM/ST/STM32-P103/)。如大家有条件购买实际的开发板，建议大家购买实际的STM32开发板。

针对μCOS，我们选择μCOS-III 这个版本，从官网上看这个版本是最新的，添加了一些用户反馈的新功能，比如Round Robin Scheduling。
这个版本网上资料也最多，并且这个版本除了提供基本的RTOS kernel 之外，也同时提供了TCP/IP Stack、μCOS-FS、μCOS-GUI等等基础服务。真可谓是学习RTOS的绝好材料。



## 硬件环境

硬件使用QEMU仿真，目前官网上QEMU的仿真支持的开发板没有STM32，万能的工程师们怎么能允许这种情况发生呐！[qemu_stm32](https://github.com/beckus/qemu_stm32)

这个作者真是活雷锋啊，他还提供了测试这个仿真出来的STM32开发板的DEMO程序，从简单的串口打印“hello world” 到复杂的RTOS。可惜，他用的是FreeRTOS，我们要用μCOS-III。[stm32_p103_demos](https://github.com/beckus/stm32_p103_demos)。

qemu_stm32这个软件的编译、安装请参考Project的ReadMe文件，Try it , enjoy it!!!
关于QEMU软件的使用请参考官网网站[QEMU](https://www.qemu.org/)。

QEMU(2.11)最新版本支持的ARM 板卡有：

```sh
qemu-system-arm  -M help
```

```	
Supported machines are:
akita                Sharp SL-C1000 (Akita) PDA (PXA270)
ast2500-evb          Aspeed AST2500 EVB (ARM1176)
borzoi               Sharp SL-C3100 (Borzoi) PDA (PXA270)
canon-a1100          Canon PowerShot A1100 IS
cheetah              Palm Tungsten|E aka. Cheetah PDA (OMAP310)
collie               Sharp SL-5500 (Collie) PDA (SA-1110)
connex               Gumstix Connex (PXA255)
cubieboard           cubietech cubieboard
emcraft-sf2          SmartFusion2 SOM kit from Emcraft (M2S010)
highbank             Calxeda Highbank (ECX-1000)
imx25-pdk            ARM i.MX25 PDK board (ARM926)
integratorcp         ARM Integrator/CP (ARM926EJ-S)
kzm                  ARM KZM Emulation Baseboard (ARM1136)
lm3s6965evb          Stellaris LM3S6965EVB
lm3s811evb           Stellaris LM3S811EVB
mainstone            Mainstone II (PXA27x)
midway               Calxeda Midway (ECX-2000)
mps2-an385           ARM MPS2 with AN385 FPGA image for Cortex-M3
mps2-an511           ARM MPS2 with AN511 DesignStart FPGA image for Cortex-M3
musicpal             Marvell 88w8618 / MusicPal (ARM926EJ-S)
n800                 Nokia N800 tablet aka. RX-34 (OMAP2420)
n810                 Nokia N810 tablet aka. RX-44 (OMAP2420)
netduino2            Netduino 2 Machine
none                 empty machine
nuri                 Samsung NURI board (Exynos4210)
palmetto-bmc         OpenPOWER Palmetto BMC (ARM926EJ-S)
raspi2               Raspberry Pi 2
realview-eb          ARM RealView Emulation Baseboard (ARM926EJ-S)
realview-eb-mpcore   ARM RealView Emulation Baseboard (ARM11MPCore)
realview-pb-a8       ARM RealView Platform Baseboard for Cortex-A8
realview-pbx-a9      ARM RealView Platform Baseboard Explore for Cortex-A9
romulus-bmc          OpenPOWER Romulus BMC (ARM1176)
sabrelite            Freescale i.MX6 Quad SABRE Lite Board (Cortex A9)
smdkc210             Samsung SMDKC210 board (Exynos4210)
spitz                Sharp SL-C3000 (Spitz) PDA (PXA270)
sx1                  Siemens SX1 (OMAP310) V2
sx1-v1               Siemens SX1 (OMAP310) V1
terrier              Sharp SL-C3200 (Terrier) PDA (PXA270)
tosa                 Sharp SL-6000 (Tosa) PDA (PXA255)
verdex               Gumstix Verdex (PXA270)
versatileab          ARM Versatile/AB (ARM926EJ-S)
versatilepb          ARM Versatile/PB (ARM926EJ-S)
vexpress-a15         ARM Versatile Express for Cortex-A15
vexpress-a9          ARM Versatile Express for Cortex-A9
virt-2.10            QEMU 2.10 ARM Virtual Machine
virt                 QEMU 2.11 ARM Virtual Machine (alias of virt-2.11)
virt-2.11            QEMU 2.11 ARM Virtual Machine
virt-2.6             QEMU 2.6 ARM Virtual Machine
virt-2.7             QEMU 2.7 ARM Virtual Machine
virt-2.8             QEMU 2.8 ARM Virtual Machine
virt-2.9             QEMU 2.9 ARM Virtual Machine
xilinx-zynq-a9       Xilinx Zynq Platform Baseboard for Cortex-A9
z2                   Zipit Z2 (PXA27x)
```	

通过查看qemu官方文档里 3.5 ARM System emulator相关章节得知，支持的STM32 Soc的板子有：

> lm3s6965evb          Stellaris LM3S6965EVB

> lm3s811evb           Stellaris LM3S811EVB

我们可以基于上述这两个Board做开发、验证。

[qemu_stm32](https://github.com/beckus/qemu_stm32)  这个Project里用的是QEMU2.1.3，作者添加了Olimex STM32 P103这个开发板的支持。

```sh
./qemu-system-arm -M help
```

```
Supported machines are:
lm3s811evb           Stellaris LM3S811EVB
canon-a1100          Canon PowerShot A1100 IS
vexpress-a15         ARM Versatile Express for Cortex-A15
vexpress-a9          ARM Versatile Express for Cortex-A9
xilinx-zynq-a9       Xilinx Zynq Platform Baseboard for Cortex-A9
connex               Gumstix Connex (PXA255)
n800                 Nokia N800 tablet aka. RX-34 (OMAP2420)
lm3s6965evb          Stellaris LM3S6965EVB
versatileab          ARM Versatile/AB (ARM926EJ-S)
borzoi               Borzoi PDA (PXA270)
tosa                 Tosa PDA (PXA255)
cheetah              Palm Tungsten|E aka. Cheetah PDA (OMAP310)
midway               Calxeda Midway (ECX-2000)
mainstone            Mainstone II (PXA27x)
n810                 Nokia N810 tablet aka. RX-44 (OMAP2420)
terrier              Terrier PDA (PXA270)
stm32-maple          OPEN SOURCE HARDWARE MAPLE / ARDUINO LIKE DEVELOPMENT BOARD
highbank             Calxeda Highbank (ECX-1000)
cubieboard           cubietech cubieboard
sx1-v1               Siemens SX1 (OMAP310) V1
sx1                  Siemens SX1 (OMAP310) V2
realview-eb-mpcore   ARM RealView Emulation Baseboard (ARM11MPCore)
kzm                  ARM KZM Emulation Baseboard (ARM1136)
akita                Akita PDA (PXA270)
z2                   Zipit Z2 (PXA27x)
musicpal             Marvell 88w8618 / MusicPal (ARM926EJ-S)
stm32-p103           Olimex STM32 p103 Dev Board
realview-pb-a8       ARM RealView Platform Baseboard for Cortex-A8
versatilepb          ARM Versatile/PB (ARM926EJ-S)
realview-eb          ARM RealView Emulation Baseboard (ARM926EJ-S)
realview-pbx-a9      ARM RealView Platform Baseboard Explore for Cortex-A9
spitz                Spitz PDA (PXA270)
none                 empty machine
virt                 ARM Virtual Machine
collie               Collie PDA (SA-1110)
smdkc210             Samsung SMDKC210 board (Exynos4210)
verdex               Gumstix Verdex (PXA270)
nuri                 Samsung NURI board (Exynos4210)
integratorcp         ARM Integrator/CP (ARM926EJ-S)
```

可以看到新添加了以下两个Cortex-M3的开发板：

> stm32-maple          OPEN SOURCE HARDWARE MAPLE / ARDUINO LIKE DEVELOPMENT BOARD

> stm32-p103           Olimex STM32 p103 Dev Board

最近又查阅了些关于Cortex-m3使用GNU Toolchain开发的相关资料，发现了一个支持大部分Cortex-m3 处理器的改进版[QEMU](https://github.com/zhanglianpin/qemu)。支持绝大部分基于Cortex-m3的开发板。

```sh
qemu-system-gnuarmeclipse -M help
```

```sh
Supported boards:
  Maple                LeafLab Arduino-style STM32 microcontroller board (r5)
  NUCLEO-F103RB        ST Nucleo Development Board for STM32 F1 series
  NUCLEO-F411RE        ST Nucleo Development Board for STM32 F4 series
  NetduinoGo           Netduino GoBus Development Board with STM32F4
  NetduinoPlus2        Netduino Development Board with STM32F4
  OLIMEXINO-STM32      Olimex Maple (Arduino-like) Development Board
  STM32-E407           Olimex Development Board for STM32F407ZGT6
  STM32-H103           Olimex Header Board for STM32F103RBT6
  STM32-P103           Olimex Prototype Board for STM32F103RBT6
  STM32-P107           Olimex Prototype Board for STM32F107VCT6
  STM32F0-Discovery    ST Discovery kit for STM32F051 line
  STM32F4-Discovery    ST Discovery kit for STM32F407/417 lines
  STM32F429I-Discovery ST Discovery kit for STM32F429/439 lines
  generic              Generic Cortex-M board; use -mcu to define the device
```
**这个针对Cortex-m3改进版的QEMU的详细介绍**[The GNU MCU Eclipse QEMU](https://gnu-mcu-eclipse.github.io/qemu/)。

**GNU MCU Eclipse QEMU的COMMAND的使用方法**[The GNU MCU Eclipse QEMU command line options](https://gnu-mcu-eclipse.github.io/qemu/options/).

**结论：**
开发板选择Olimex STM32 p103，硬件模拟器选择[The GNU MCU Eclipse QEMU](https://gnu-mcu-eclipse.github.io/qemu/)。

关于开发板的输入输出：一般嵌入式开发中不能使用通用lib库（提供基本的输入输出系统等），因为通用库是基于OS（Linux、Unix、Windos）提供的服务的。我们嵌入式开发有时候涉及到裸（bare metal）开发，那就得对程序的来龙去脉有更好的掌握。有的嵌入式集成开发环境（keil、DS-5）提供一定的IO库，为程序员提供标准的printf scanf等函数，使用串口作为物理输入输出接口。这样的话，针对不同的硬件开发板还需要处理uart的初始化和使用的细节问题，不是很方面。ARM提供了一个叫Semihosting的技术，程序运行在目标平台上，需要使用输入输出这些资源时，通过调试接口（JLINK）调用HOST开发主机的键盘输入资源和显示资源。我们使用的QEMU支持这个Semihosting技术，所有的输入输出都使用这项技术，解决了目标开发板的输入输出问题。这项技术之用在开发阶段，产品成型阶段当然要实现自己的IO喽。不过，我们这里仅仅Debug代码，因此使用Semihosting技术比较合适。一图胜千言：
![Semihosting overview](/images/posts/2018-04-11-RTOS-dev-env/Semihosting.png "Semihosting overview")

对这个技术感兴趣的可以看这里：[ARM Semihosting](http://www.keil.com/support/man/docs/armcc/armcc_pge1358787045051.htm)


## 软件环境


软件使用 μCOS-III(V3.03.01),我们直接下载μCOS-III基于STM32开发板的版本。
下载地址：

> [Download μCOS-III](https://www.micrium.com/downloadcenter/)

官网上的开发环境一般是基于Keil 或者IAR的，这种集成开发环境适合工程开发，并不适合入门学习，因为好多东西都自动化了，不利于学习者学习掌握核心重点，因此我们使用GNU Tools(GCC、GDB)来做编译、调试。我们使用的GNU tools 上面已经介绍了。

## 开发环境

编译器使用gnu交叉编译器编译，编译器下载链接：[GNU Arm Embedded Toolchain](https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads)

开发嵌入式软件准备环境还是挺繁琐又没技术含量的事情，当我接触了Docker之后，一切都改变了，Docker可以帮你快速部署开发环境，又不破坏其他的开发环境。Docker提供了OS级别上的运行环境隔离。关于Dcoker的基本概念和基本使用方法参见[Docker user manual](https://docs.docker.com/engine/docker-overview/)。

因此，我计划发布两个Docker image：

> [Build environment docker image](https://hub.docker.com/r/zhanglianpin/stm32_compile_env/)

> [Debug environment using qemu docker image](https://hub.docker.com/r/zhanglianpin/ucos_debug_env/)

如果你访问Docker慢的话，请选择使用国内aliyun

Build environment docker image：
```sh
docker pull registry.cn-hangzhou.aliyuncs.com/bahutou/stm32_compile_env
```

Debug environment using qemu docker image：
```sh
docker pull registry.cn-hangzhou.aliyuncs.com/bahutou/ucos_debug_env
```

关于这两个Docker image的具体使用方法具体见后面的使用qemu调试Cortex-M3 程序的demo。

生成Docker image的Dockerfile 托管在github上，如果你想自己定制Docker image，你可以修改Dockerfile，然后自己动手build。

Dockerfile地址：
Build environment Dockerfile：[Build environment Dockerfile](https://github.com/zhanglianpin/stm32_compile_env)

Debug environment Dockerfile：[Debug environment Dockerfile](https://github.com/zhanglianpin/ucos_debug_env)


# 引用资源

1. [STM32仿真软件](https://github.com/zhanglianpin/qemu)
2. [P103开发板测试代码](https://github.com/zhanglianpin/stm32_p103_demos)
3. [P103开发板资料下载地址](https://www.olimex.com/Products/ARM/ST/STM32-P103/)
4. [STM32-SOC-STM32F103RB资料地址](http://www.st.com/en/microcontrollers/stm32f103rb.html)
5. [uCOS资料下载](https://www.micrium.com/)
6. [Develop embedded software using Docker](https://blog.feabhas.com/2017/09/introduction-docker-embedded-developers-part-1-getting-started/)
