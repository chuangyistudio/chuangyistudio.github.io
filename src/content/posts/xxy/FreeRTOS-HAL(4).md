---
title: 基于Cubemx的STM32HAL库实时操作系统FREERTOS学习——二进制信号量
published: 2026-08-13
description: 基础知识
tags: [电子, 嵌入式, 教程, FreeRTOS]
category: 技术分享
author: "Miss Apple"
draft: false
---


## 前言：
由于主包在学习V1版本的过程中发现了V1版本在信号量等地方存在BUG，所以后面将转用V2版本（再次踩坑证明出现新版本是有道理的）。BUG在后面会进行解释，并且后面的内容中将讲述一些V1对应的内容。前面的内容有机会的话再改成V2版本的，但其实差别不会很大。

---

## 一.  什么是二进制信号量

信号量(Semaphore)，有时被称为信号灯，是在多线程环境下使用的一种设施，是可以用来保证两个或多个关键代码段不被并发调用。在进入一个关键代码段之前，线程必须获取一个信号量；一旦该关键代码段完成了，那么该线程必须释放信号量。其它想进入该关键代码段的线程必须等待直到第一个线程释放信号量。

二进制信号量是一种特殊类型的信号量，其计数值只能为0或1，类似于开关。它通常用于实现任务之间的简单同步，确保在某一时刻只有一个任务可以访问共享资源，可以把它想象成一个“通行证”或“一把钥匙”。

---

## 二、CubeMx配置参数介绍
![每个任务之间隔着个时间片](/img/posts/xxy/4-1.png)
#### Semaphore Name（信号量名称）
仅仅是 CubeMX 图形界面中给你的标识/标签，方便你区分多个信号量。

#### Initial State（初始状态）
定义信号量在系统启动时的初始可用状态。这是二值信号量最关键的配置项。

Available（可用）：信号量初始计数值为 1。意味着系统刚启动时，这个信号量就已经“被释放”了，任何任务第一次调用 osSemaphoreWait 都能立即成功获取它。

Not Available（不可用）：信号量初始计数值为 0。意味着系统刚启动时，这个信号量处于“被占用”状态。任何任务第一次调用 osSemaphoreWait 都会阻塞等待，直到某个中断或任务调用 osSemaphoreRelease 释放它。
![每个任务之间隔着个时间片](/img/posts/xxy/4-14.png)
#### Allocation（内存分配方式）
决定信号量的控制块（Semaphore Control Block）从哪里分配内存。

Dynamic（动态分配）：从 FreeRTOS 的堆（Heap）中申请内存。最常用，灵活性高，不需要用户操心内存布局。

Static（静态分配）：使用用户预先定义的静态变量作为控制块。需要额外填写 Control Block Name 字段。适合对内存确定性要求极高的安全关键场景。
![每个任务之间隔着个时间片](/img/posts/xxy/4-15.png)
#### Control Block Name（控制块名称）
仅在 Allocation 选择 Static 时有效。用于指定用户预先定义的信号量控制块的变量名。

---

## 三、实例：按键释放信号量

配置按键外部中断
![每个任务之间隔着个时间片](/img/posts/xxy/4-2.png)
![每个任务之间隔着个时间片](/img/posts/xxy/4-3.png)
记得打开中断并配置优先级
![每个任务之间隔着个时间片](/img/posts/xxy/4-4.png)
编写按键释放信号量代码
```bash
/\* USER CODE BEGIN 2 \*/

void HAL_GPIO_EXTI_Callback(uint16_t GPIO_PIN)

{
    if (GPIO_PIN == Key1_Pin )
    {
        osSemaphoreRelease (KeySemaphoreHandle);
    }
}

/\* USER CODE END 2 \*/
```

---

创建二进制信号量KeySemaphore
![每个任务之间隔着个时间片](/img/posts/xxy/4-5.png)

MX_FREERTOS_Init()中信号量的创建并初始化代码
```bash
/\* Definitions for SendSem \*/

osSemaphoreId_t SendSemHandle;

const osSemaphoreAttr_t SendSem_attributes = {

.name = "SendSem"

};

/\* creation of KeySemaphore \*/

KeySemaphoreHandle = osSemaphoreNew(1, 0, &KeySemaphore_attributes);
```

---

创建按键任务
![每个任务之间隔着个时间片](/img/posts/xxy/4-6.png)
编写接收信号量代码，通过串口输出获取到信号量。
```bash
/\* USER CODE END Header_StartKeyTask \*/

void StartKeyTask(void \*argument)
{
    /\* USER CODE BEGIN StartKeyTask \*/

    char message\[50\] = "获取到信号量";

    \* Infinite loop \*/
    for(;;)
    {
        osSemaphoreAcquire(KeySemaphoreHandle,osWaitForever);
        HAL_UART_Transmit (&huart1, (uint8_t \*)message, strlen(message), 1000);

        osDelay(1);
    }
/\* USER CODE END StartKeyTask \*/
}
```
这样按键任务就会在循环开始就尝试获取KeySemaphore，获取不到就会一直在阻塞态，不会像原来那样每10ms就查看一次，占用资源。  
这样只有有任务释放KeySemaphore，才会醒来完成按键判断。  


实验结果：  
按下按键,串口会收到信号，证明任务进入运行态。

![每个任务之间隔着个时间片](/img/posts/xxy/4-7.png)

二次信号量的弊端就是，只能知道事件是否发生，无法计数事件发生了几次。如果事件快速发生了很多次，但是任务处理又比较慢的话，可能会只处理一次。要解决这个问题就需要用到下节要讲的计数信号量了。

---

## 四、V1版本和V2版本的函数区别

V2版本函数：

osSemaphoreAcquire(KeySemaphoreHandle,osWaitForever);

V1版本函数：

osSemaphoreWait(KeySemaphoreHandle, osWaitForever);

虽然功能相同，但在具体细节上有一些差异

返回值类型不同：

osSemaphoreAcquire (v2)：返回 osStatus_t 类型，是一个枚举值，直接告诉你操作是否成功（如 osOK, osErrorTimeout, osErrorParameter 等）。

osSemaphoreWait (v1)：返回 int32_t 类型。在成功获取信号量时，它返回信号量的当前计数值（在获取之后）；如果发生超时或错误，则返回一个负值。


## 五、V1版本的问题  
以计数信号量为例：
![每个任务之间隔着个时间片](/img/posts/xxy/4-8.png)
![每个任务之间隔着个时间片](/img/posts/xxy/4-16.png)
在使用的计数信号量的过程中发现，创建了信号量之后开始运行代码，直接就有10个接收信号，就像已经有是个被释放的信号量了。
![每个任务之间隔着个时间片](/img/posts/xxy/4-9.png)
追溯函数就会发现，V1版本使用的osSemaphorCreate()这个创建函数存在将**信号量最大计数值**和**初始值**，初始化为同一个值了，因此你设置的最大计数值就会是你的初始值。二进制信号量也是一样的（二进制的最计数值就是1）。
![每个任务之间隔着个时间片](/img/posts/xxy/4-10.png)
![每个任务之间隔着个时间片](/img/posts/xxy/4-11.png)
![每个任务之间隔着个时间片](/img/posts/xxy/4-12.png)
![每个任务之间隔着个时间片](/img/posts/xxy/4-13.png)
那在CubeMx中设置的Initial Count将没有意义，算是V1版本的bug吧。主包发现这个的时候已经懵了好吧，666还有第二关，直接转V2版本好吧，出新版本有出新版本的道理。

---
