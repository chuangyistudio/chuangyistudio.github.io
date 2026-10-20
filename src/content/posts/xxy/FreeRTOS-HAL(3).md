---
title: 基于Cubemx的STM32HAL库实时操作系统FREERTOS学习——消息队列
published: 2026-08-11
description: 基础知识
tags: [电子, 嵌入式, 教程, FreeRTOS]
category: 技术分享
author: "Miss Apple"
draft: false
---


## 一、什么是队列

队列又称消息队列，是一种常用于任务间通信的数据结构，消息队列可以在任务与任务间、中断和任务间传递信息，实现了任务接收来自其他任务或中断的不固定长度的消息。

任务能够从队列里面读取消息，当队列中的消息是空时，读取消息的任务将被阻塞，不占用CPU的运行资源。

当队列中有新消息时，被阻塞的任务会被唤醒并处理新消息；当等待的时间超过了指定的阻塞时间，即使队列中尚无有效数据，任务也会自动从阻塞态转为就绪态。消息队列是一种异步的通信方式。

![每个任务之间隔着个时间片](/img/posts/xxy/3-1.png)

在裸机系统中，两个程序间需要共享某个资源通常使用全局变量来实现，但是全局变量就是“公共黑板”，谁都能随手涂改，数据被覆盖了都不知道；队列就是“快递柜”，包裹放进去不怕丢，哪怕收件人正在睡觉，快递员（系统）也会敲门把他叫醒取件。

---

## 二、CubeMx配置参数介绍
![每个任务之间隔着个时间片](/img/posts/xxy/3-3.png)
#### Queue Name（队列名称）
仅仅是 CubeMX 图形界面中给你的标识/标签，方便你区分多个队列。

#### Queue Size（队列长度）
定义该消息队列最多能同时存放多少条消息。这是一个“容量”概念。

#### Item Size（消息项大小）
定义每条消息占用的字节数。这里填的是数据类型，CubeMX 会自动计算大小。

#### Allocation（内存分配方式）
决定队列的存储区（消息缓冲区）从哪里分配内存。

Dynamic（动态分配）：从 FreeRTOS 的堆（Heap）中申请内存。最常用，灵活性高，不需要用户操心内存布局。队列创建时自动分配 Queue Size × Item Size 字节的缓冲区。

Static（静态分配）：使用用户预先定义的静态数组作为消息缓冲区。需要额外填写 Buffer Name 和 Control Block Name 字段。
![每个任务之间隔着个时间片](/img/posts/xxy/3-4.png)

#### Buffer Name（缓冲区名称）
仅在 Allocation 选择 Static 时有效。用于指定用户预先定义的消息缓冲区数组的变量名（如 uint16_t myQueue01Buffer\[16\];）。

#### Buffer Size（缓冲区大小）
显示该队列实际占用的内存大小（单位：字节）。CubeMX 会自动计算并显示为 Queue Size × Item Size，此处为 16 × 2 = 32 Bytes。

#### Control Block Name（控制块名称）
仅在 Allocation 选择 Static 时有效。用于指定用户预先定义的队列控制块（Queue Control Block） 的变量名。

---

## 三、  实例：串口控制小灯  
CubeMx创建FreeRTOS工程
![每个任务之间隔着个时间片](/img/posts/xxy/3-2.png)

---

创建LEDTask和CommandTask两个任务
![每个任务之间隔着个时间片](/img/posts/xxy/3-5.png)
![每个任务之间隔着个时间片](/img/posts/xxy/3-6.png)

任务创建代码
```bash
/\* definition and creation of LEDTask \*/
osThreadDef(LEDTask, StartLEDTask, osPriorityNormal, 0, 512);  
LEDTaskHandle = osThreadCreate(osThread(LEDTask), NULL);

/\* definition and creation of CommandTask \*/
osThreadDef(CommandTask, StartCommandTask, osPriorityHigh, 0, 512);  
CommandTaskHandle = osThreadCreate(osThread(CommandTask), NULL);

```

---

创建LEDQueue和CommandQueue两个队列
![每个任务之间隔着个时间片](/img/posts/xxy/3-7.png)
![每个任务之间隔着个时间片](/img/posts/xxy/3-8.png)

队列创建代码
```bash
/\* definition and creation of LEDQueue \*/

osMessageQDef(LEDQueue, 16, LEDMessage\*);

LEDQueueHandle = osMessageCreate(osMessageQ(LEDQueue), NULL);

/\* definition and creation of CommandQueue \*/

osMessageQDef(CommandQueue, 16, uint8_t);

CommandQueueHandle = osMessageCreate(osMessageQ(CommandQueue), NULL);

/\* USER CODE END Header_StartLEDTask \*/

```

---

打开串口

![每个任务之间隔着个时间片](/img/posts/xxy/3-13.png)
![每个任务之间隔着个时间片](/img/posts/xxy/3-14.png)

---

LEDTask任务接收队列数据，控制LED灯

```bash
void StartLEDTask(void const \* argument)
{
    /\* USER CODE BEGIN StartLEDTask \*/

    /\* Infinite loop \*/

    for(;;)
    {
        LEDMessage* message;
        osEvent event = osMessageGet(LEDQueueHandle, osWaitForever); 
        //从消息队列中接收数据
        if (event.status == osEventMessage)
        {
            message = (LEDMessage\*)event.value.v; // 这才是真正的消息数据

            // 或者指针形式：
            // void \*ptr = event.value.p;
        }

        switch (message->color) {
            case 0:
                HAL_GPIO_WritePin(RedLED_GPIO_Port, RedLED_Pin, message->state ? GPIO_PIN_SET : GPIO_PIN_RESET);
            break;
            case 1:
                HAL_GPIO_WritePin(GreenLED_GPIO_Port, GreenLED_Pin, message->state ? GPIO_PIN_SET : GPIO_PIN_RESET);
            break;
            case 2:
                HAL_GPIO_WritePin(BlueLED_GPIO_Port, BlueLED_Pin, message->state ? GPIO_PIN_SET : GPIO_PIN_RESET);
            break;
        }
        vPortFree(message);

    }
/\* USER CODE END StartLEDTask \*/
}
```
---

CommandTask任务接收串口信息并发送LEDQueueHandle队列数据
```bash
typedef enum {  
    STATE_WAIT_HEADER = 0, // 等待包头 AA  
    STATE_COLOR, // 读取颜色  
    STATE_STATE, // 读取状态  
    STATE_WAIT_TAIL // 等待包尾 FF  
} ParserState;

uint8_t receive = 0;  
ParserState state = STATE_WAIT_HEADER;  
uint8_t ledColor = 0;  
uint8_t ledState = 0;  

/\* USER CODE END Header_StartCommandTask \*/

void StartCommandTask(void const \* argument)
{
    /\* USER CODE BEGIN StartCommandTask \*/

    UART1_Receive_Start();

    /\* Infinite loop \*/

    for(;;)
    {
        osEvent event = osMessageGet(CommandQueueHandle, osWaitForever);
        //从消息队列中接收数据
        if (event.status == osEventMessage) {

        receive = event.value.v; // 这才是真正的消息数据

        // 或者指针形式：
        // void \*ptr = event.value.p;
        }

        switch (state)
        {
            case STATE_WAIT_HEADER:

            if (receive == 0xAA) {
                state = STATE_COLOR; // 找到包头，下一步读颜色
            }
            break;

            case STATE_COLOR:
            if (receive >= 0x01 && receive <= 0x03) {
                ledColor = receive;
                state = STATE_STATE; // 颜色合法，下一步读状态
            } else {
            state = STATE_WAIT_HEADER; // 非法数据，回去找包头
            }
            break;

            case STATE_STATE:
            if (receive == 0x00 || receive == 0x01) {
                ledState = receive;
                state = STATE_WAIT_TAIL; // 状态合法，下一步等包尾
                } else {
                state = STATE_WAIT_HEADER; // 非法数据，回去找包头
            }
            break;

            case STATE_WAIT_TAIL:
            if (receive == 0xFF) {

                // 完整一帧接收完毕，处理数据
                LEDMessage* message = pvPortMalloc(sizeof(LEDMessage));

                if (message != NULL) {
                    message->color = ledColor - 1; // 01→0(红), 02→1(绿), 03→2(蓝)
                    message->state = ledState; // 0=亮, 1=灭
                    osMessagePut(LEDQueueHandle, (uint32_t)message, 0);
                }
            }
            // 无论包尾对不对，都回去等下一个包头
            state = STATE_WAIT_HEADER;
            break;
        }
    }
/\* USER CODE END StartCommandTask \*/
}
```

---

串口接收数据并发送CommandQueueHandle队列信息
```bash
/\* USER CODE BEGIN 4 \*/

void UART1_Receive_Start(void)
{
    HAL_UART_Receive_IT (&huart1, &data, 1);
}
void HAL_UART_RxCpltCallback(UART_HandleTypeDef \*huart)
{
    if (huart->Instance == USART1)
    {
        HAL_UART_Receive_IT (&huart1, &data, 1);
        osMessagePut(CommandQueueHandle, (uint32_t)data, 0);
    }
}

/\* USER CODE END 4 \*/

```

---

实验现象：

串口发送蓝灯亮起信号
![每个任务之间隔着个时间片](/img/posts/xxy/3-9.png)
蓝灯亮起
![每个任务之间隔着个时间片](/img/posts/xxy/3-10.png)
串口发送蓝灯熄灭信号
![每个任务之间隔着个时间片](/img/posts/xxy/3-11.png)
蓝灯熄灭
![每个任务之间隔着个时间片](/img/posts/xxy/3-12.png)


---

## 四、V1版本和V2版本的API函数区别


**V2版本：**  
```bash
发送队列数据函数：
osMessageQueuePut(CommandQueueHandle, (uint32_t)data, 0);  

接收队列数据函数：
osMessageQueueGet(CommandQueueHandle, osWaitForever);  
```
**V1版本：**    
```bash
发送队列数据函数：
osMessagePut(CommandQueueHandle, (uint32_t)data, 0);

接收队列数据函数：  
osEvent event = osMessageGet(CommandQueueHandle, osWaitForever);  
if (event.status == osEventMessage)  
{  
    uint32_t received = event.value.v; // 这才是真正的消息数据  
    // 或者指针形式：  
    // void \*ptr = event.value.p;  
}
```

接收函数的数据传递方式不同：V1通过返回值，V2通过参数指针。

---
