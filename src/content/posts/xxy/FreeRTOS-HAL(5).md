---
title: 基于Cubemx的STM32HAL库实时操作系统FREERTOS学习——计数信号量
published: 2026-09-1
description: 基础知识
tags: [电子, 嵌入式, 教程, FreeRTOS]
category: 技术分享
author: "Miss Apple"
draft: false
---

## 一、什么是计数信号量

计数信号量就是在二进制信号量的基础上解开队列长度的限制，可以设置为任意长度。

当生产者释放一个信号时，待处理数量就会+1，消费者接收一个信号时，待处理数量就会-1。消费者在处理信号时，计数信号量就会记录待处理数量，不会像二进制信号量那样忽略掉中间的释放量。

![每个任务之间隔着个时间片](/img/posts/xxy/5-1.png)

#### Semaphore Name（信号量名称）

作用：仅仅是 CubeMX 图形界面中的标识/标签，方便你区分多个信号量。

2\. Max Count（最大计数值）⭐ 这是计数信号量的灵魂参数

作用：定义该信号量最多能累加到的上限值。计数值永远不会超过这个数字。

3\. Initial Count（初始计数值）

作用：系统启动时，该信号量的初始计数值。

4\. Allocation（内存分配方式）

作用：决定信号量的控制块从哪里分配内存。

Dynamic：从 FreeRTOS 的堆（Heap）中动态申请内存。最常用，灵活性高。

Static：使用用户预先定义的静态变量。需要额外填写 Control Block Name。

5\. Control Block Name（控制块名称）

作用：仅在 Allocation 选 Static 时有效，用于指定用户定义的控制块变量名。

实例：

创建计数信号量

/\* Definitions for SendSem \*/

osSemaphoreId_t SendSemHandle;

const osSemaphoreAttr_t SendSem_attributes = {

.name = "SendSem"

};

创建按键任务

配置按键

/\* USER CODE END Header_StartKeyTask \*/

void StartKeyTask(void const \* argument)

{

/\* USER CODE BEGIN StartKeyTask \*/

int count = 0;

char msg\[50\];

/\* Infinite loop \*/

for(;;)

{

if(HAL_GPIO_ReadPin(Key1_GPIO_Port, Key1_Pin) == GPIO_PIN_RESET)

{

osDelay(10);

if(HAL_GPIO_ReadPin(Key1_GPIO_Port, Key1_Pin) == GPIO_PIN_RESET)

{

count ++;

sprintf(msg, "Key pressed %d times", count);

HAL_UART_Transmit (&huart1, (uint8_t \*)msg, strlen(msg), 1000);

osSemaphoreRelease (SendSemHandle);

while(HAL_GPIO_ReadPin(Key1_GPIO_Port, Key1_Pin) == GPIO_PIN_RESET){}

}

}

osDelay(10);

}

/\* USER CODE END StartKeyTask \*/

}

创建串口发送任务

/\* USER CODE END Header_StartSendTask \*/

void StartSendTask(void const \* argument)

{

/\* USER CODE BEGIN StartSendTask \*/

int count = 0;

char msg\[50\];

/\* Infinite loop \*/

for(;;)

{

osSemaphoreWait (SendSemHandle, osWaitForever);

count ++;

if(count > 10)

{

sprintf(msg, "receive %d times", count-10);

HAL_UART_Transmit (&huart1, (uint8_t \*)msg, strlen(msg), 1000);

}

osDelay(2000);

}

/\* USER CODE END StartSendTask \*/

}

按键按一次就会释放一个信号量，并且计数信号量会记录信号量，在任务处理时，信号量就会“排队”，信号就不会丢失。