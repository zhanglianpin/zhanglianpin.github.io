---
layout: post
title: 基于Docker环境开发、调试嵌入式软件（Embedded Software develop/Debug using Docker）
categories: RTOS-μCOS
description: 使用一个具体的Demo来熟悉一下开发（using cross toolchain by docker）、调试环境（using qemu by docker）。
keywords: RTOS μCOS
---

本文使用一个具体的简单的Demo介绍此系列文章的Dev、Debug环境的使用方法，Dev、Debug环境都使用Docker技术提供OS层的环境隔离。以避免在搭建嵌入式开发环境上浪费时间，或者因搭建此嵌入式环境影响了其他开发环境。Docker技术可以解决以上这些困扰。开发环境使用Vi + GNU Cross  toolchain，测试环境使用Qemu仿真STM32-P103这款开发板。使用仿真软件仿真开发板也节省了大家购买硬件的开支，同时也方便在有即兴Idea时，随时进行验证。



# 准备开发、调试环境

开发、调试环境依赖于Docker，首先安装Docker。Docker有两个版本CE（Community Edition）和 EE（Enterprise Edition），EE功能强大些，且稳定可靠，公司用，收费。我们学习用当然选择CE版本啦。[官方安装教程](https://docs.docker.com/install/),不过Docker.com这个网站访问需要翻墙。不想翻墙的同学可以使用[国内的aliyun](https://yq.aliyun.com/articles/110806)。

> Tag:使用Win10的bash的同学注意啦，由于Docker使用了Linux的cgroups和namespace等一些技术，Win10 bash并不完全支持。Docker安装在Win10 bash上不能使用。如果真想在Win10上使用Docker，请直接安装官方文档安装Docker for Win10. 底层环境直接使用Virtualbox提供Linux环境虚拟环境，Docker运行在虚拟化的Linux环境里。

安装完成后，可以使用以下命令查看是否安装成功：

**input:**
```sh
docker version
```
**output:**

```sh
Client:
 Version:      17.09.0-ce
 API version:  1.32
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:42:38 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.09.0-ce
 API version:  1.32 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:41:20 2017
 OS/Arch:      linux/amd64
 Experimental: false
```

有了Docker ，就可以下载我们使用的开发、调试环境的Docker image了。

**For Dev:**

Docker hub(官方镜像源)：
```sh
docker pull zhanglianpin/stm32_compile_env:latest
```

Aliyun hub（阿里云镜像源）：
```sh
docker pull registry.cn-hangzhou.aliyuncs.com/bahutou/stm32_compile_env
```

**For Debug:**

Docker hub(官方镜像源)：
```sh
docker pull zhanglianpin/zhanglianpin/ucos_debug_env:latest
```

Aliyun hub（阿里云镜像源）：
```sh
docker pull registry.cn-hangzhou.aliyuncs.com/bahutou/zhanglianpin/ucos_debug_env
```

下载完Dokcker image后，可以使用以下命令测试其是否下载成功：

**input**

```sh
docker image ls
```

**output**
```sh
REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
zhanglianpin/stm32_compile_env   latest              48cd33a2339f        3 days ago          527MB
zhanglianpin/ucos_debug_env      latest              8d3c1b84e93f        3 days ago          203MB
ubuntu                           16.04               0b1edfbffd27        11 days ago         113MB
ubuntu                           14.04               8cef1fa16c77        11 days ago         223MB
linuxep/lepv0.1                  latest              92e0fb0aa864        7 months ago        785MB
hello-world                      latest              05a3bd381fc2        7 months ago        1.84kB
```

从上面的输出我们可以看到，我的本地Docker iamge包含了 zhanglianpin/stm32_compile_env和zhanglianpin/ucos_debug_env这两个image。

怎么使用这两个Docker image呐？看下面：

**input:**
```sh
docker run --rm  zhanglianpin/stm32_compile_env arm-none-eabi-gcc -v
```
**output:**
```sh
Using built-in specs.
COLLECT_GCC=arm-none-eabi-gcc
COLLECT_LTO_WRAPPER=/opt/gcc-arm-none-eabi-7-2017-q4-major/bin/../lib/gcc/arm-none-eabi/7.2.1/lto-wrapper
Target: arm-none-eabi
Configured with: /tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/src/gcc/configure --target=arm-none-eabi --prefix=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/install-native --libexecdir=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/install-native/lib --infodir=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/install-native/share/doc/gcc-arm-none-eabi/info --mandir=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/install-native/share/doc/gcc-arm-none-eabi/man --htmldir=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/install-native/share/doc/gcc-arm-none-eabi/html --pdfdir=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/install-native/share/doc/gcc-arm-none-eabi/pdf --enable-languages=c,c++ --enable-plugins --disable-decimal-float --disable-libffi --disable-libgomp --disable-libmudflap --disable-libquadmath --disable-libssp --disable-libstdcxx-pch --disable-nls --disable-shared --disable-threads --disable-tls --with-gnu-as --with-gnu-ld --with-newlib --with-headers=yes --with-python-dir=share/gcc-arm-none-eabi --with-sysroot=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/install-native/arm-none-eabi --build=x86_64-linux-gnu --host=x86_64-linux-gnu --with-gmp=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/build-native/host-libs/usr --with-mpfr=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/build-native/host-libs/usr --with-mpc=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/build-native/host-libs/usr --with-isl=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/build-native/host-libs/usr --with-libelf=/tmp/jenkins/jenkins-GCC-7-build_toolchain_docker-633_20171130_1512067137/build-native/host-libs/usr --with-host-libstdcxx='-static-libgcc -Wl,-Bstatic,-lstdc++,-Bdynamic -lm' --with-pkgversion='GNU Tools for Arm Embedded Processors 7-2017-q4-major' --with-multilib-list=rmprofile
Thread model: single
gcc version 7.2.1 20170904 (release) [ARM/embedded-7-branch revision 255204] (GNU Tools for Arm Embedded Processors 7-2017-q4-major) 
```

**input:**
```sh
docker run --rm  zhanglianpin/ucos_debug_env  qemu-system-gnuarmeclipse -v
```
**output:**
```sh
GNU ARM Eclipse 64-bits QEMU emulator version 2.8.0 (v2.8.0-644-ge45e0e1-dirty)
Copyright (c) 2003-2016 Fabrice Bellard and the QEMU Project developers
```

相信你应该有一些感觉了吧，使用这个Docker还是挺爽的，轻开销地提供环境隔离，爽到爆。是不是你一直梦寐以求的类，反正我为各种开发、测试环境弄得烦躁的时候还是有的，这下终于可以心平气和地做开发工作了。

# 一个简单的不能再简单的例子

Demo：使用STM32-P103这个开发板做一个最简单的“5+4”, R0=0x05   R1=0x04  把结果0X09放到R2中 。
直接上代码（add.s）：

```nasm
/**
  ******************************************************************************
  * @file      add.s
  * @author    Linc Zhang
  * @version   V1.0.0
  * @date      02-05-2018
  * @brief     exec some cortex instruction for testing
  ******************************************************************************
**/
	.thumb                       @(same as saying '.code 16')
        .syntax unified

    .text
	.equ STACKINIT,   0x20005000
vectors:        
        .word  STACKINIT         @stack pointer value when stack is empty
        .word _start + 1         @reset vector (manually adjust to odd for thumb)
        .word _nmi_handler + 1   @
        .word _hard_fault  + 1   @ 
        .word _memory_fault + 1  @ 
        .word _bus_fault + 1     @ 
        .word _usage_fault + 1   @ 
_start:                          @ Label, not really required
        mov   r0, #5             @ Load register r0 with the value 5
        mov   r1, #4             @ Load register r1 with the value 4
        add   r2, r1, r0         @ Add r0 and r1 and store in r2

stop:   b stop                   @ Infinite loop to stop execution

_dummy:                          @if any int gets triggered, just hang in a loop
_nmi_handler:
_hard_fault:
_memory_fault:
_bus_fault:
_usage_fault:
        add r0, 1
        add r1, 1
        b _dummy 
```


使用的Link script：
```ld

SECTIONS {
         . = 0x00000000;
         .text : { * (.text); }
		 end_code = .;
         . = 0x08000000;
         .data :AT(end_code) { * (.data); }
}

```

使用的Makefile：

```make
AS = arm-none-eabi-as
CC = arm-none-eabi-gcc
LD = arm-none-eabi-ld
CFLAGS=

add.elf: 
	$(LD)  -T main.ld -o add.elf  add.o

add.o: 
	$(AS) -g -o add.o add.s 


.PHONY: clean

clean:
	rm -f *.o *~ core *.elf *.bin
```

**Compile**

**input:**
```sh
docker run --rm  -v $(pwd):/home/work  -w /home/work  zhanglianpin/stm32_compile_env make
```
**output**
```sh
arm-none-eabi-as -o add.o add.s 
arm-none-eabi-ld  -T main.ld -o add.elf  add.o
```

**Debug**

**input:**
```sh
docker run --rm -i  -v $(pwd):/home/work/  zhanglianpin/ucos_debug_env  qemu-system-gnuarmeclipse  --verbose --verbose --nographic  --board STM32-P103 --image add.elf
```
**output:**
```sh
GNU ARM Eclipse 64-bits QEMU v2.8.0 (qemu-system-gnuarmeclipse).
Board: 'STM32-P103' (Olimex Prototype Board for STM32F103RBT6).
Device file: '/opt/gnuarmeclipse/qemu/2.8.0-201703022210-head/share/qemu/devices/STM32F103xx-qemu.json'.
Device: 'STM32F103RB' (Cortex-M3 r0p1, MPU, 4 NVIC prio bits, 43 IRQs), Flash: 128 kB, RAM: 20 kB.
Image: 'add.elf'.
Command line: (none).
Load     52 bytes at 0x00000000-0x00000033.
Cortex-M3 r0p1 core initialised.
'/machine/mcu/stm32/RCC', address: 0x40021000, size: 0x0400
'/machine/mcu/stm32/FLASH', address: 0x40022000, size: 0x0400
'/machine/mcu/stm32/PWR', address: 0x40007000, size: 0x0400
'/machine/mcu/stm32/AFIO', address: 0x40010000, size: 0x0400
'/machine/mcu/stm32/EXTI', address: 0x40010400, size: 0x0400
'/machine/mcu/stm32/GPIOA', address: 0x40010800, size: 0x0400
'/machine/mcu/stm32/GPIOB', address: 0x40010C00, size: 0x0400
'/machine/mcu/stm32/GPIOC', address: 0x40011000, size: 0x0400
'/machine/mcu/stm32/GPIOD', address: 0x40011400, size: 0x0400
'/machine/mcu/stm32/GPIOE', address: 0x40011800, size: 0x0400
'/peripheral/led:red' 12*10 @(331,362) active low '/machine/mcu/stm32/GPIOC',12
QEMU 2.8.0 monitor - type 'help' for more information
(qemu) Cortex-M3 r0p1 core reset.

(qemu) 
```

qemu 提供了一个monitor的UI，使用这个CLI 输入以下命令：

**input:**
```sh
info registers
```
**output:**
```sh
R00=00000005 R01=00000004 R02=00000009 R03=00000000
R04=00000000 R05=00000000 R06=00000000 R07=00000000
R08=00000000 R09=00000000 R10=00000000 R11=00000000
R12=00000000 R13=20005000 R14=00000000 R15=00000028
PSR=40000173 -Z-- T svc32
FPSCR: 00000000
```

可以看到，R00=0x05,R01=0x04,R02=0x09，确实执行了上面的  “5+4”的运算。

当然你还可以使用Gdb进行程序的调试。

**input:**
```sh
docker run --rm -i  -v $(pwd):/home/work/   zhanglianpin/ucos_debug_env  qemu-system-gnuarmeclipse  --verbose --verbose --nographic  --board STM32-P103 --image add.elf   --gdb tcp::1234
```

以上命令启动了一个Gdb server，等待gdb client 去连接调试。

新开一个Linux的CLI，然后输入：

**input:**
```sh
docker run --rm -i  -v $(pwd):/home/work  zhanglianpin/stm32_compile_env  arm-none-eabi-gdb
```
**output:**
```sh
GNU gdb (GNU Tools for Arm Embedded Processors 7-2017-q4-major) 8.0.50.20171128-git
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "--host=x86_64-linux-gnu --target=arm-none-eabi".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word".
(gdb)
```

在Gdb CLI中输入连接Gdb Server的命令：
```sh
target remote 172.17.0.2:1234
```
> Tag: Docker container的IP Addr查询方法：
```sh
docker inspect --format {% raw %}'{{ .NetworkSettings.IPAddress }}'{% endraw %} container_name_or_id
```
查询docker container ID的方法：
```sh
docker ps
```

连接上Gdb Server之后，一切都那么熟悉了，可以使用一切Gdb调试方法了，比如你想看下Registers：

**input:**
```sh
info all-registers
```
**output:**
```sh
r0             0x5      5
r1             0x4      4
r2             0x9      9
r3             0x0      0
r4             0x0      0
r5             0x0      0
r6             0x0      0
r7             0x0      0
r8             0x0      0
r9             0x0      0
r10            0x0      0
r11            0x0      0
r12            0x0      0
sp             0x20005000       0x20005000
lr             0x0      0
pc             0x28     0x28
cpsr           0x40000173       1073742195
MSP            0x20005000       536891392
PSP            0x0      0
PRIMASK        0x0      0
BASEPRI        0x0      0
FAULTMASK      0x1      1
CONTROL        0x0      0
```

自己动手搞一遍，我相信你应该知道怎么回事了，下面我捡那个重点说明一下，已经搞定的请绕行。以免耽误自己的宝贵时间。

编译的时候运行的那个命令

```sh
docker run --rm  -v $(pwd):/home/work  -w /home/work zhanglianpin/stm32_compile_env make
```
 
Docker run 的意思是运行一个Container,使用的镜像是我精心为您准备的STM32系列CPU的编译环境，--rm这个参数的含义是我运行结束后这个Container会自动删除，不保留上次运行的状态，因为我们只是单次编译下而已，没有必要保存上次Container运行的状态。 -v 这个参数的意思是让Container里面的编译环境能共享HOST主机的目录和文件，/home/work/ 这个目录是我设置的编译环境Container的默认工作目录，-w 是为Container里面的编译环境赋予写权限。zhanglianpin/stm32_compile_env是Docker iamge的名称，后面的make 是运行起来这个Container之后要执行的命令。使用Container共享HOST的文件还是很方便的，这样我就只用编译环境编译，其他的你像编辑啥的工作还是放在HOST主机上来做好了。

你理解了上面的套路之后，其他的使用Docker命令应该很好理解了。


# 简单的例子中蕴含的一些Key Point

本章节我只提出几个问题，以引起大家的思考。这些问题会在后面blog中一一解开。
1. STM32-P103上电后从哪里执行第一条指令？如何把需要执行的第一条指令放到STM32-P103需要的地方？(涉及到STM32F103RB的启动知识，链接脚本等等内容)
2. 如果程序里有变化的量（Variable），该怎么处理？这些变量下载到哪里？存储到哪里？使用之前有什么需要注意的地方？("Load Addr" **VS** "Excute Addr")
3. 使用编译器编译出来的ELF文件内部到底什么结构？从静态的文件到动态的程序之间需要做哪些工作？
4. 运行C语言需要哪些前提条件？为什么有了高级语言，汇编语言还存在？STM32-P103可以完全使用C语言开发吗？

本人认为理解这些事情，比较重要，理解了这些在调试一些程序BUG的时候能一针见血。

# 引用资料说明

我最早的时候思考过有必要用Docker搭建Embedded software develop environment 吗？我是看了[An Introduction to Docker for Embedded Developers](https://blog.feabhas.com/2017/09/introduction-docker-embedded-developers-part-1-getting-started/)打消了我的顾虑。

Docker 基本的概念、设计理念、使用方法最好的文档是[Docker Docs](https://docs.docker.com/)。

Demo编程我参考了[An Introduction to Docker for Embedded Developers](https://blog.feabhas.com/2017/09/introduction-docker-embedded-developers-part-1-getting-started/)和[ARM Cortex M3 Assembly Language Example](http://pygmy.utoh.org/riscy/cortex/led-stm32.html)。

qemu-system-gnuarmeclipse 的使用方法见[QEMU command](https://gnu-mcu-eclipse.github.io/qemu/options/)。

硬件Dataheet见[Datasheet stm32f103rb](http://www.st.com/en/microcontrollers/stm32f103rb.html)。


# 资源汇总
1. [Docker Docs](https://docs.docker.com/)
2. [Embedded Programming with the GNU Toolchain](http://www.bravegnu.org/gnu-eprog/index.html)
3. [An Introduction to Docker for Embedded Developers](https://blog.feabhas.com/2017/09/introduction-docker-embedded-developers-part-1-getting-started/)
4. [ARM Cortex M3 Assembly Language Example](http://pygmy.utoh.org/riscy/cortex/led-stm32.html)
5. [Datasheet stm32f103rb](http://www.st.com/en/microcontrollers/stm32f103rb.html)
6. [QEMU command](https://gnu-mcu-eclipse.github.io/qemu/options/)
