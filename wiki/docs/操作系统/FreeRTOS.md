# FreeRTOS入门

FreeRTOS是一种市面上领先且流行的面向微控制器和小型处理器的实时操作系统。FreeRTOS通过MIT开源许可免费分发，包括一个内核和一组不断丰富的IoT库。

[TOC]

## 为什么要学FreeRTOS？

<img src=".\FreeRTOS\操作系统与裸片运行.png" alt="操作系统与裸片运行" style="zoom:45%;" />

FreeRTOS作为调度人机交互与硬件资源的中间层，可以很好地帮助用户完成一些复杂场景下的任务，最大化利用处理器的运行频率。实际生活中见到的大多嵌入式系统都是带有操作系统的。我们把不带操作系统的单片机称为裸片，裸片运行大多采用SuperLoop框架，如下图：

<img src=".\FreeRTOS\SuperLoop.png" alt="SuperLoop" style="zoom:45%;" />

即在setup中完成初始化操作，在loop中不断循环执行一个程序（另外可能还有硬件中断）。

这样的框架确实足以应对一些初学者的使用，但是我们看下面的例子。

- 传感器系统。在一块LCD上显示2种传感器的读数，要求实时更新示数。传感器1一次数据的处理需要10ms，传感器2一次数据的处理需要5s。

如果采用SuperLoop框架，即loop()中依次读取2个传感器的读数，LCD刷新一次显示，刷新频率为5s/次，在读取传感器2数据时无法在LCD上更新传感器1的示数。这样无法达到实时刷新的要求。（特别说明，本例子强调的是**数据的处理**，即在CPU中计算需要的时间，不能采用通过判断传感器是否数据发生变化进而利用中断的方法来通知LCD刷新。）

- 蓝牙通信。蓝牙主机和从机持续握手，保持通信状态。

在SuperLoop框架中，我们依次执行所有任务，一次循环中蓝牙主机和从机握一次手，保持通信状态。若其余任务耗时过长则很可能导致两次握手时长超过蓝牙设定参数，通信断开。

上述例子默认的单片机为单核，处理器在一个时间段内只能干一件事情。这导致了这些**阻塞**情况的发生。

因此，怎么在单核的单片机中解决这些问题呢？我们急需加入一个**能够指挥各个任务处理顺序，让多个任务轮流占用CPU**，防止阻塞发生的角色。

FreeRTOS就是这样一个角色。

## FreeRTOS运行机理

FreeRTOS的核心是**调度**。处理器将时间切片，调度器将运行时间有条理地、可控制地分给每个任务。调度器通过时钟中断，即滴答，来选择任务。如下图所示。

<img src=".\FreeRTOS\FreeRTOS调度机理.png" alt="FreeRTOS调度机理" style="zoom:50%;" />

所以其实，**操作系统  !=  同步进行**。在单个CPU中，实质只是多个任务轮流执行，每次执行都只占用了非常短的一部分时间，看起来像是同步执行而已。

同时，我们也要区分一些重要的概念：

- 进程：线程的合集。
- 线程：一个进程中处理任务的执行单元。

例如，打开任务管理器，能看到电脑运行的多个进程

<img src=".\FreeRTOS\进程与线程.png" alt="进程与线程" style="zoom:50%;" />

其中飞书是一个进程。那么飞书里多个聊天窗口同时运作，每个聊天窗口就是飞书一个线程。**进程 > 线程**！

在面对微控制器的FreeRTOS中，**任务 == 线程**，**进程==CPU/核/处理器**。

## FreeRTOS实操

接下来本文以**在esp32的esp-arduino框架上应用FreeRTOS**为例，讲解FreeRTOS的实际使用过程。

乐鑫的ESP32是一款双核的MCU，小核core0，又叫pro_cpu，用于处理上层通信协议等，如WIFI，蓝牙，固定在此核上运行；大核core1，又叫app_cpu，用于处理用户任务，如main.c中在loop里定义的任务。如果没有引入FreeRTOS，每个核是一个CPU，可单独运行，单独进行时间切片。

乐鑫公司针对原生FreeRTOS进行移植和改进，目前在ESP32工具链中引用的是IDF FreeRTOS，可针对双核运作。

### 创建任务

API：`xTaskCreatePinnedToCore()`

此函数会指定在哪个核上创建任务。未指定在哪个核上创建的任务将会被自由调度到任何一个可用的核上。

使用举例

```c++
void Task1code(void *pvParameters);
void Task2code(void *pvParameters);

TaskHandle_t Task1;
TaskHandle_t Task2;

// create a task that will be executed in the Task1code() function, with priority 1 and executed on core 0
xTaskCreatePinnedToCore(
    Task1code, /* Task function. */
    "Task1",   /* name of task. */
    10000,     /* Stack size of task */
    NULL,      /* parameter of the task */
    1,         /* priority of the task */
    &Task1,    /* Task handle to keep track of created task */
    0);        /* pin task to core 0 */
delay(500);

// create a task that will be executed in the Task2code() function, with priority 1 and executed on core 1
xTaskCreatePinnedToCore(
    Task2code, /* Task function. */
    "Task2",   /* name of task. */
    10000,     /* Stack size of task */
    NULL,      /* parameter of the task */
    1,         /* priority of the task */
    &Task2,    /* Task handle to keep track of created task */
    1);        /* pin task to core 1 */
delay(500);
```

这样会将任务1创建在核0上，任务2创建在核1上。API的第一个参数指明了任务的回调函数，即待会要编写的任务主函数；第二个参数是任务名称；第三个参数是分配给这个任务的栈空间；第四个参数是任务需要传入的参数；第五个参数是任务的优先级，这个会在后面的章节讲到；第六个参数是任务的句柄，用于后续阻塞、通知等，模仿着定义一个就好；最后一个参数是创建在哪个核上，esp32中可选的是0和1。

接下来我们需要编写任务主函数，主函数应当无限循环，不能返回。

```c++
// Task1code: blinks an LED every 1000 ms
void Task1code(void *pvParameters)
{
  // Serial.print("Task1 running on core ");
  // Serial.println(xPortGetCoreID());
  

  for (;;)
  {
    digitalWrite(led1, HIGH);
    delay(1000);
    digitalWrite(led1, LOW);
    delay(1000);
    ESP_LOGI(TAG, "Task1 running on core %d", xPortGetCoreID());
  }
}

// Task2code: blinks an LED every 700 ms
void Task2code(void *pvParameters)
{
  // Serial.print("Task2 running on core ");
  // Serial.println(xPortGetCoreID());
  

  for (;;)
  {
    digitalWrite(led2, HIGH);
    delay(3000);
    digitalWrite(led2, LOW);
    delay(3000);
    ESP_LOGI(TAG, "Task2 running on core %d", xPortGetCoreID());
  }
}
```

编写完后，运行代码，创建任务后两个核会分别执行这两个任务。**这是在两个核上，任务执行是同步的！**

若把任务2也创建在核0上，创建完成后，由于两个任务优先级相同，核0会轮流执行这两个任务。

### 任务状态

下图展示了任务可处于的四种状态，以及它们之间的切换过程。下图列的API都是最简单的，也是最常用的。

<img src=".\FreeRTOS\任务状态.png" alt="任务状态" style="zoom:50%;" />

调用API `xTaskNotifyWait` 可让当前任务进入阻塞状态。最简单的使用形式是

```
xTaskNotifyWait(0, 0, NULL, portMAX_DELAY);
```

在任务主函数及其调用的子函数中调用这个API，可让此任务**立刻**进入阻塞状态，并且持续时间是**永远**，直到**有其他任务通知它运行**。



调用API `xTaskNotifyGive` 可指定通知哪个任务，解除任务的阻塞状态。使用形式是

```c++
xTaskNotifyGive(Task handle);
```

参数是读者想通知的任务的句柄。通知后，任务回到“运行中”状态。

### 任务优先级

优先级是设置给调度器看的。每个任务在创建时都赋予了固定优先级，当核上的一个滴答中断到来时，调度器会执行具有最高优先级且处于就绪状态的任务。

调度器会采用轮转的方式，定期在具有相同优先级的就绪状态的任务间切换执行。时间分片由时钟中断控制。

在esp32的双核RTOS中，每个核都会独立地调度要运行的任务。两个核的滴答中断可能不同步，但是周期相同。

数字越大优先级越高。

可以通过ESP-IDF FreeRTOS的手册上的一个例子来更直观地理解ESP32双核调度、任务抢占的过程

<img src=".\FreeRTOS\任务抢占.png" alt="任务抢占" style="zoom:80%;" />

## 参考资料

[FreeRTOS (IDF) - ESP32 - — ESP-IDF 编程指南 latest 文档 (espressif.com)](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/api-reference/system/freertos_idf.html)
