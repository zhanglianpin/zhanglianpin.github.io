---
layout: post
title: Stack在函数调用、中断（异常）、RTOS中的应用
categories: RTOS-μCOS
description: Stack是一种重要的数据结构，在函数调用、中断、RTOS中有着广泛的应用
keywords: RTOS-μCOS Cortex-M3
---

在计算机程序中，分支[Branch]具有很重要的意义。函数调用、中断产生时程序的跳转、OS中的Multiple Task的切换等等这些均属于分支范畴，说的直白点就是根据需求合理的控制执行流。执行这些跳转共有一个特征：还需要再返回到跳转前的Point。怎么记录需要返回到哪里？多层级的调用和返回都是有顺序的，怎么保证这些顺序？跳转前如有执行的环境怎么原封不动地保存？这样返回时才能继续之前的工作。程序执行的环境或者说执行流从CPU层次来看主要包括哪些核心内容？本篇文章重点介绍这些内容。


### 何为Stack？

[Stack](https://en.wikipedia.org/wiki/Stack_%28abstract_data_type%29)是这样一种结构:本身是一段连续的内存空间，那怎么使用这样一种内存空间才算是起到了栈的实际作用呐？首先要规定这一段连续空间的基地址，然后就从这个基地址开始依次放东西。取东西时也是从最上面的开始取。Stack这个英文含义就是把东西堆叠起来的意思。就像水桶盛水一样，进水的时候是从低往上，放水的时候是从上往低。**存取有一定的先后顺序.** 按照上面的方案管理这一段存储空间，就是发挥了栈的作用。因为栈使用的频率实在是太高了，所以在计算机汇编层次就有专门操作栈的指令。包括Push（入栈）、Pop（出栈）等。

其实栈又有一些逻辑上的分类：

根据先腾出空间再用还是先用再腾空间分为：

1. 满堆栈：即入栈后堆栈指针sp指向最后一个入栈的元素。也就是sp先减一（加一）再入栈。

2. 空堆栈：即入栈后堆栈指针指向最后一个入栈元素的下一个元素。也就是先入栈sp再减一（或加一）。

根据从高地址开始用还是从低地址开始用分为：

1. 递增堆栈：即堆栈一开始的地址是低地址，向高地址开始递增。就如同一个水杯（假设上面地址大）开口的是大地址，从杯底开始装水。自己画一画图就清楚了。我就偷懒一下不画了。

2. 递减堆栈：即堆栈一开始的地址是高地址，向低地址开始递增。就如同还是刚才说的那个水杯，现在开口的是小地址，从大地址开始用，往下走，相当于杯子口朝下。我们用的时候是把水往上一点点压上去。呵呵呵，不过这样的杯子就失去了用途。但在内存上还是可以的。	

那么根据这上面分类方法，我们就可以得到四种栈的类型。

**Cortex-M3中使用的是递减满堆栈(full descending stack)。**


### Stack在函数调用时的应用

我们使用高级语言设计程序时，为了程序的简介性和易读性，使用合理的函数抽象和函数调用是比较常用的设计方法。在函数调用时，有两个核心的问题需要关注：**指令流(PC)的控制、临时数据(FP)的保存和恢复**。这个也好理解，数据结构里提到的程序=数据结构+算法也是一个意思，一个程序就有两个重要的内容：指令、数据。通俗地描述一下函数调用时需要考虑的问题，大家想一想，我们写c语言时用到函数调用，有时候还嵌套调用很多函数。还有有些函数还需要参数和返回值。怎么处理各个函数的参数和返回值，以及当每一个函数完成工作时该返回到那个地方。这些都是要解决的问题。当然最容易想到的也是必须做的是在进行调用跳转之前，把我这个函数现有的状态保存起来，保存什么那，调用函数返回后的下一条指令，还有我这个函数需要的哪些数据。还有就是保存这些信息到哪些地方哪？这些都是我们要解决的问题。还有就是你不光要保存这些信息，还要保存这些信息的顺序。因为函数调用本身有顺序，你像a调用b，b又接着调用c。在c执行完后要返回到b，b执行完再返回a。呵呵，有顺序。

我们一一想办法来解决，当然别人已经用栈的策略解决的很完美了，我们只是想一些更简洁的最容易想起来的但是不完善的方法，也正说明了人家的策略是多么的优秀。

关于调用函数的问题，我们可以把返回地址保存到一些地方，当然程序员知道在那？还知道顺序，再根据顺序返回就好了，但做这样的工作太累了，除了写程序还要记这些东西。这样太累了，聪明的程序员肯定也不这样做。关于传参，有这样可以考虑的，用专门规定好的寄存器来做传参。行，但有缺陷,如果传的参数很多或者是变化的，就不好用寄存器传参了。而且我们有操作系统时往往要求编译器产生的代码具有可重入性，也就是保证代码和数据的相对独立性。一个函数被调用两次，都有两次的参数环境。到底现在我们是怎么做的呐。答案是用栈。

其他废话不多说了，直接上个Cortex-M3的Demo。

function_stack.c ：

```c
int  add(int a,int b);
void test(void);


void  main_frame(void)
{
        int result = 11;
        result = add(1,2);

}



int  add(int a,int b)
{
        int result_temp = 22;

        result_temp = a + b;
        test();
        return result_temp;
}

void test(void)
{
        int a = 31,b = 32,c = 33;


}
```

function_stack.s  :

```nasm
        .cpu arm7tdmi
        .eabi_attribute 20, 1
        .eabi_attribute 21, 1
        .eabi_attribute 23, 3
        .eabi_attribute 24, 1
        .eabi_attribute 25, 1
        .eabi_attribute 26, 1
        .eabi_attribute 30, 6
        .eabi_attribute 34, 0
        .eabi_attribute 18, 4
        .file   "function_stack.c"
        .text
        .align  2
        .global main_frame
        .syntax unified
        .arm
        .fpu softvfp
        .type   main_frame, %function
main_frame:
        @ Function supports interworking.
        @ args = 0, pretend = 0, frame = 8
        @ frame_needed = 1, uses_anonymous_args = 0
        push    {fp, lr}
        add     fp, sp, #4
        sub     sp, sp, #8
        mov     r3, #11
        str     r3, [fp, #-8]
        mov     r1, #2
        mov     r0, #1
        bl      add
        str     r0, [fp, #-8]
        nop
        sub     sp, fp, #4
        @ sp needed
        pop     {fp, lr}
        bx      lr
        .size   main_frame, .-main_frame
        .align  2
        .global add
        .syntax unified
        .arm
        .fpu softvfp
        .type   add, %function
add:
        @ Function supports interworking.
        @ args = 0, pretend = 0, frame = 16
        @ frame_needed = 1, uses_anonymous_args = 0
        push    {fp, lr}
        add     fp, sp, #4
        sub     sp, sp, #16
        str     r0, [fp, #-16]
        str     r1, [fp, #-20]
        mov     r3, #22
        str     r3, [fp, #-8]
        ldr     r2, [fp, #-16]
        ldr     r3, [fp, #-20]
        add     r3, r2, r3
        str     r3, [fp, #-8]
        bl      test
        ldr     r3, [fp, #-8]
        mov     r0, r3
        sub     sp, fp, #4
        @ sp needed
        pop     {fp, lr}
        bx      lr
        .size   add, .-add
        .align  2
        .global test
        .syntax unified
        .arm
        .fpu softvfp
        .type   test, %function
test:
        @ Function supports interworking.
        @ args = 0, pretend = 0, frame = 16
        @ frame_needed = 1, uses_anonymous_args = 0
        @ link register save eliminated.
        str     fp, [sp, #-4]!
        add     fp, sp, #0
        sub     sp, sp, #20
        mov     r3, #31
        str     r3, [fp, #-8]
        mov     r3, #32
        str     r3, [fp, #-12]
        mov     r3, #33
        str     r3, [fp, #-16]
        nop
        add     sp, fp, #0
        @ sp needed
        ldr     fp, [sp], #4
        bx      lr
        .size   test, .-test
        .ident  "GCC: (GNU Tools for Arm Embedded Processors 7-2017-q4-major) 7.2.1 20170904 (release) [ARM/embedded-7-branch revision 255204]"
```

先用gdb直观的感觉一下function stack。
在函数test处打个Break。

```sh
(gdb) bt
#0  0x0800023c in test () at main.c:48
#1  0x0800022a in add (a=1, b=2) at main.c:42
#2  0x080001e2 in main () at main.c:29
(gdb) frame 0
#0  0x0800023c in test () at main.c:48
48              int a = 31,b = 32,c = 33;
```

再来张图直观感受下，DrawSquare调用DrawLine。

![Call stack](/images/posts/2018-05-22-stack-using/call_stack.png "Call stack Frame")


下面我们好好分析一下函数调用的函数帧。
整个函数栈帧  main---add---test 。
下面详细分析下main函数调用add函数，然后add函数调用test的整个过程，分析下Stack在其中的作用。
main函数在调用add时，有两方面的工作需要做：保存main函数的当前数据状态(核心Registers)、传递的参数、返回参数和test函数执行完毕后的返回地址等相关的执行流。我们看编译器编译出来的汇编代码，控制data相关的Registers是：FP、SP ;控制指令流的Registers为PC、LR。一个基本原则是如果函数调用发生后，子函数需要改变的那些个Registers都需要保存到Stack中。
```nasm
	push	{fp, lr}
	add	fp, sp, #4
	sub	sp, sp, #8
	mov	r3, #11
	str	r3, [fp, #-8]
```
我们知道add函数会使用FP来操作add函数内部的data registers，而add函数里面又调用了test函数，从而改变了LR的值，因此需要将FP和LR保存到main函数栈帧中。这样当add函数执行完返回时，弹出原来保存的FP、LR就可以恢复到原来main函数时的状态。下面是调整FP的指针值，使其指向Stack中FP的位置。以后在main函数栈帧里操作数据都使用FP这个指针，以这个为基础操作Stack。接下来就要分配main函数需要的局部变量了，包括main函数里自己申请的局部变量result，以及其他需要传递给add函数的参数。我们看到我们将result的值11放到了R3中，然后存到了main的栈帧中，原因是因为main函数里需要使用这个R3，但子函数add中也有可能使用R3这个Register，因此需要将R3保存到main函数的栈帧中，到时候从add函数返回时可以恢复到未调用add函数之前的main函数状态。

后面就开始给add函数传递参数了。
```nasm
	mov	r1, #2
	mov	r0, #1
```
可以看到使用的是R0和R1寄存器来传递1 和 2 这两个参数。这个是要参考AAPCS的，这个规范里就是这么规定的。

下面直接执行bl 指令用于跳转。

```nasm
	bl	add
```

没事，别担心，我们已经把main函数相关的内容都保存起来了，不怕add函数破坏，破坏了我可以再恢复。^_^。
bl指令修改了两个Registers的值，PC和LR。

下面开始到了add的地盘，
```nasm
	push	{fp, lr}
	add	fp, sp, #4
	sub	sp, sp, #16
	str	r0, [fp, #-16]
	str	r1, [fp, #-20]
	mov	r3, #22
	str	r3, [fp, #-8]
	ldr	r2, [fp, #-16]
	ldr	r3, [fp, #-20]
	add	r3, r2, r3
	str	r3, [fp, #-8]
```

老套路，因为后面add函数会调用test函数，所以先把核心的FP LR保存起来，调整FP指针。add函数用到了R0,R1这两个Registers，而后面又要调用test函数，因此要把R0，R1这两个Registers保存起来。后面还有一个result_temp的值为22，也需要保存起来。后面执行了具体的加法操作。把结果存到了R3中。然后执行bl指令，跳转到test函数。

test函数里面和main、add函数栈帧也基本一致，只不过test没有要调用的子函数了，不会修改LR指针，因此不需要保存LR了。其他的倒是没什么特别的地方。
```nasm
str	fp, [sp, #-4]!
	add	fp, sp, #0
	sub	sp, sp, #20
	mov	r3, #31
	str	r3, [fp, #-8]
	mov	r3, #32
	str	r3, [fp, #-12]
	mov	r3, #33
	str	r3, [fp, #-16]
	nop
	add	sp, fp, #0
	@ sp needed
	ldr	fp, [sp], #4
	bx	lr
```

调用正向过程分析完了，我们接着分析一下Return过程。重点分析add返回到main的情况。
```nasm
	ldr	r3, [fp, #-8]
	mov	r0, r3
	sub	sp, fp, #4
	@ sp needed
	pop	{fp, lr}
	bx	lr
```
add函数需要把结果返回到main函数中，使用当前栈帧的FP将add的返回值load到R3中，然后放到R0中，最后根据FP调整SP，最后把弹出刚进到add函数时保存到FP,LR的值从Stack中弹出来。这样LR的值就是add函数执行结束后，需要返回的值，FP就是main函数的栈帧FP指针。R0中保存的是add函数的返回值，当然也是AAPCS规定的喽。执行bx之后，一切后回到了main函数栈帧。

分析完了，这里面有两个链表结构：一个PC和LR组成的指令流链表；一个SP和FP组成的数据流链表。以FP作为例子，FP始终指向当前Stack存放上一个FP的栈地址上，操作这个FP可以读取到当前栈帧的Data，当前函数返回后，将FP指向的Stack里存的上一个FP的值放到FP中，就保证了FP永远指向当前函数的栈帧。

**Call Stack 最好是自己动手画画动态的栈帧变化，你就理解什么叫Call Frame了，也顺便理解了什么是Local Variable了.**

**同时，从上面的分析我们也可以看到，从CPU角度来看，一个执行流的核心资源就是一些Registers再加上一些逻辑运算和Mem的操作**

对计算机描述比较清楚的一本书是[Structured Computer Organization(Andrew S.Tanenbaum)](https://eleccompengineering.files.wordpress.com/2014/07/structured_computer_organization_5th_edition_801_pages_2007_.pdf) 欢迎阅读这本书。

要想系统的了解汇编语言级别的函数调用规范和二进制规范还请参考以下规范：
* **AADWARF**  DWARF for the ARM Architecture
* **AAELF**    ELF for the ARM Architecture
* **AAPCS**    Procedure Call Standard for the ARM Architecture
* **ADDENDA**  Addenda to, and errata in, the ABI for the ARM Architecture
* **BPABI**    Base Platform ABI for the ARM Architecture
* **BSABI**    This document ABI for the ARM Architecture (Base Standard)
* **CLIBABI**  C Library ABI for the ARM Architecture
* **CPPABI**   C++ ABI for the ARM Architecture
* **EHABI**    Exception Handling ABI for the ARM Architecture
* **EHEGI**    Exception handling components, example implementations
* **RTABI**    Run-time ABI for the ARM Architecture
* [**GC++ABI**](http://mentorembedded.github.com/cxxabi/abi.html) Generic C++ ABI
* [**GDWARF**](http://dwarfstd.org/Dwarf3Std.php) DWARF 3.0, the generic debug format.
* [**GABI**](http://www.sco.com/developers/gabi/)  Generic ELF, 17th December 2003 draft.
* [**GLSB**](http://www.linuxbase.org/spec/refspecs/)  gLSB v1.2 Linux Standard Base
* [**Open BSD**](http://www.openbsd.org/) Open BSD standard


### Stack在中断或者异常发生时的应用

我们以STM32F103(Cortex-M3)为例，具体说明下中断或者异常发生时Stack的作用。
前面说了，只要有Branch的产生，就需要保存跳转之前的一些运行状态。正常情况下，Cortex-M3会在Thread Mode运行一个执行流。当有外部中断或者CPU异常发生时，会切换到Handle Mode，且需要使用Stack保存一些当前状态，以保证中断/异常服务程序返回时，能继续被打断的工作。

具体这些内容请参见
[STM32 Programming manual](http://www.st.com/content/ccc/resource/technical/document/programming_manual/5b/ca/8d/83/56/7f/40/08/CD00228163.pdf/files/CD00228163.pdf/jcr:content/translations/en.CD00228163.pdf) 的2.3 章节Exception model 和 [Cortex-M3 Devices Generic User Guide](http://infocenter.arm.com/help/topic/com.arm.doc.dui0552a/DUI0552A_cortex_m3_dgug.pdf) 的2.3章节的内容。

这里仅仅简单介绍一下大致流程，当中断或者异常发生时，硬件会自动保存部分关键Registers到当前使用的Stack中。当然，其他寄存器需要软件保存到Stack。然后将一个特殊的Return Value(0xFFFFFFFx) 放到LR寄存器中，当中断/异常服务程序返回时，CPU通过判断这个特殊的返回值得知这是一个中断或者异常跳转，然后硬件自动将之前保存的关键Registers弹出来以恢复Thread的运行。这里有一些细节，RTOS用到的一些硬件特性会有一个专门的文章介绍。

### Stack在RTOS中的应用

前面说了，Cortex-M3这个CPU默认的执行流只有两个，一个是Thread Mode下的执行流，一个是当中断或者异常发生时产生的Handle Mode下的执行流。RTOS提供的一个核心服务是抽象出更多的执行流，也叫Task。关于为什么要抽象出更多的执行流，前面文章里也给出了明确的说明。这个核心控制器一般CPU是单core的，只能提供一个执行流，再加上Interrupt，可以说整体就有了两个可管理的执行流，且中断的执行流优先级要大于CPU提供的执行流。

如果利用软件管理手段能把CPU上的执行流管理起来，势必能加速整体的吞吐量和实时响应能力。因为CPU上的执行流从宏观上来看利用CPU进行运算的时间是间隔的，这个也很好理解，因为CPU运算能力是很强的，但是一个任务一般会涉及到等待外部事件，外部事件发生后才去做逻辑处理或者数值运算。在等待外部相对于CPU运算来说慢的多的多的事件时，如果能把CPU让给其他任务来用，那CPU的整体利用率就很高了。真是不错的想法。一个嵌入式产品不可能不与外部交互，毕竟科技产品是为人服务的。这样一个执行流在等待一些慢的事件时，可以把CPU让出来让其他Task使用，这样整个CPU利用率就提高了，系统整体响应就快了。

切换Task对CPU来讲，就是保存当前Task的一些核心的Registers状态，然后Load要切换的Task原先保存好的各个寄存器。就实现了Task切换。每个Task都有自己独立的Stack，保存了当前CPU所有的Registers。
来张图：
![Call stack](/images/posts/2018-05-22-stack-using/RTOS_stack.png "RTOS stack")

你看，每一个Task都有一个独立的Stack，用于保存自身的运行状态。

### 引用资源

[C语言和堆栈](https://blog.csdn.net/zhanglianpin/article/details/7334451)

[AAPCS: The ARM-THUMB Procedure Call Standard](http://infocenter.arm.com/help/topic/com.arm.doc.espc0002/ATPCS.pdf)

[BSABI: ABI for the ARM Architecture (Base Standard)](http://infocenter.arm.com/help/topic/com.arm.doc.ihi0036b/IHI0036B_bsabi.pdf)

[STM32 Programming manual](http://www.st.com/content/ccc/resource/technical/document/programming_manual/5b/ca/8d/83/56/7f/40/08/CD00228163.pdf/files/CD00228163.pdf/jcr:content/translations/en.CD00228163.pdf)

[Cortex-M3 Devices Generic User Guide](http://infocenter.arm.com/help/topic/com.arm.doc.dui0552a/DUI0552A_cortex_m3_dgug.pdf)





