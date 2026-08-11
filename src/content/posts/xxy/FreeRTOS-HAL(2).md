---
title: 基于Cubemx的STM32HAL库实时操作系统FREERTOS学习——创建工程与任务创建
published: 2026-08-08
description: 基础知识
tags: [电子, 嵌入式, 教程, FreeRTOS]
category: 技术分享
draft: false
---
 

## 前言:

本系列默认读者都有基本的Cubemx的基础认识，所有会直接带大家了解FREERTOS在Cubemx中的使用，基础配置部分将不会再讲述。

---

## 1.设置HAL库的Timebase Source （基础时钟源）

![每个任务之间隔着个时间片](/img/posts/xxy/2-1.png)

这个操作本质上就是为HAL库指定了一个独立专用的定时器（如图中TIM1）来产生自己的心跳。  
这样一来：  
SysTick 定时器就完全交给 FreeRTOS专用，保证了任务调度的稳定。  
TIM1 定时器专门服务于 HAL 库，确保了 HAL_Delay() 等函数的准确性。

两者互不干扰，各司其职，这是解决冲突的标准做法。

若没有设置基础时钟源，在生成代码的时候就会出现这样的警告。
![每个任务之间隔着个时间片](/img/posts/xxy/2-2.png)

## 2.配置FREERTOS
![每个任务之间隔着个时间片](/img/posts/xxy/2-3.png)
![每个任务之间隔着个时间片](/img/posts/xxy/2-4.png)

#### Task Name（任务名称）

仅仅是 CubeMX 图形界面中给你的标识/标签，方便你区分多个任务。  
它通常会被用来生成该任务的句柄（Handle）变量名前缀（例如你填 myTask02，代码里可能会生成 osThreadId_t myTask02Handle）。  
**注意**，它不等于最终代码里的入口函数名。

####  Priority（任务优先级）

决定这个任务在 RTOS 调度器中的“话语权”。优先级越高，越容易抢占 CPU。  
可选值（从上到下即优先级从低到高）

其中osPriorityRealtime：最高优先级，几乎实时响应，**慎用！**
![每个任务之间隔着个时间片](/img/posts/xxy/2-5.png)

####  Stack Size (Words)（栈大小，单位：字）

分配给该任务的栈空间大小（1 Word = 4 字节）。任务运行时，局部变量、函数调用、中断嵌套都会消耗栈空间。

####  Entry Function（入口函数名）

这是核心。任务创建后，RTOS 调度器第一次运行该任务时，会跳转到这个函数执行。

####  Code Generation Option（代码生成选项）

控制 CubeMX 如何生成该任务的初始化代码。

Default（默认）：CubeMX 会在 main.c 的 MX_FREERTOS_Init() 函数中，自动生成创建该任务的标准代码（调用 osThreadCreate 或 osThreadNew）。

As external (作为外部函数): 选择此项，STM32CubeMX会在 freertos.c 中用 extern 关键字声明任务函数，但不会生成函数的定义（即函数体）。

As weak (作为弱函数): STM32CubeMX会生成一个带有 \__weak 属性的任务函数定义。

![每个任务之间隔着个时间片](/img/posts/xxy/2-6.png)

一般直接默认选择Default就好了。

####  Parameter（参数）

传递给入口函数的用户参数。

####  Allocation（内存分配方式）

决定任务的控制块（TCB，Task Control Block）和栈从哪里分配内存。

Dynamic（动态分配）：从 FreeRTOS 的堆（Heap）中申请内存。最常用，灵活性高。

Static（静态分配）：使用用户预先定义的静态变量作为 TCB 和栈。需要额外填写 Buffer Name 和 Control Block Name 字段。适合对内存确定性要求极高的安全关键场景。

![每个任务之间隔着个时间片](/img/posts/xxy/2-7.png)

####  Buffer Name（缓冲区名称）

仅在 Allocation 选择 Static 时有效。用于指定用户预先定义的栈缓冲区（Stack Buffer） 的变量名。

####  Control Block Name（控制块名称）

仅在 Allocation 选择 Static 时有效。用于指定用户预先定义的任务控制块（TCB） 的变量名。


## 3.配置好后生成代码
![每个任务之间隔着个时间片](/img/posts/xxy/2-9.png)
配置好后点OK。

![每个任务之间隔着个时间片](/img/posts/xxy/2-8.png)

MX_FREERTOS_Init()中Task02任务创建代码

```bash
 /* definition and creation of myTask02 */

osThreadDef(myTask02, StartTask02, osPriorityIdle, 0, 128);

myTask02Handle = osThreadCreate(osThread(myTask02), NULL);
```

Task02任务函数

```bash
/* USER CODE END Header_StartTask02 */

void StartTask02(void const \* argument)

{

/* USER CODE BEGIN StartTask02 */

    /* Infinite loop */

    for(;;)

    {

        osDelay(1);

    }

    /* USER CODE END StartTask02 */

}
```
**注意** 任务函数中for循环函数中不能为空。

如果是 for(;;) { }（空循环，无延时）：会导致该任务始终占用CPU，“占着茅坑不拉屎”，低优先级任务永远无法运行，系统几乎卡死。

---
任务创建完成。
