# 例子：ADC采样-DMA缓存-UART输出

这个例子用STM32F103ZET6的片上ADC读取模拟电压，通过DMA暂存到RAM，然后仍以DMA输出到UART接口。

- [例子：ADC采样-DMA缓存-UART输出](#例子adc采样-dma缓存-uart输出)
  - [配置CubeMX](#配置cubemx)
    - [时钟](#时钟)
    - [ADC](#adc)
    - [UART](#uart)
  - [代码](#代码)
    - [1. 定义缓冲区](#1-定义缓冲区)
    - [2. 启动ADC](#2-启动adc)
    - [3. UART输出](#3-uart输出)
  - [实验](#实验)

## 配置CubeMX

### 时钟

高速时钟用外部晶振源，主频用PLL倍频到72MHz。
![](ADC-DMA-UART.assets\cubemx1.png)
![](ADC-DMA-UART.assets\cubemx2.png)

### ADC
- 用ADC1的IN1通道（用别的无所谓，从引脚占用考虑）。

- Data Alignment用右对齐。

- Scan Conversion禁用，因为只用了一个通道。

- Continuous Conversion启用，连续采样

![](ADC-DMA-UART.assets\cubemx3.png)

顺便给ADC配置一下DMA。新建一个DMA Request，Channel和Direction是自动填的，Priority可以选Medium。注意左下角的Mode要选Circular，等于在内存里划分一个环形存储器。ADC精度是12bit，Data Width应该已经自动设为Half Word（2byte）。
![](ADC-DMA-UART.assets\cubemx4.png)

### UART

USART1设置为异步模式，波特率115200。

![](ADC-DMA-UART.assets\cubemx5.png)

顺便也给UART设置一下DMA。新建Request之后把Request源改成TX，Priority这里也选Medium了。

![](ADC-DMA-UART.assets\cubemx6.png)

## 代码

### 1. 定义缓冲区
    /* USER CODE BEGIN PV */
    uint16_t AdcBuf[72];
    /* USER CODE END PV */

### 2. 启动ADC

在CubeMX生成的初始化程序后加入：

      HAL_ADCEx_Calibration_Start(&hadc1);
      HAL_ADC_Start_DMA(&hadc1, (uint32_t *)AdcBuf, 72);

HAL_ADCEx_Calibration_Start()用于校准ADC：
![](ADC-DMA-UART.assets\ref1.png)

HAL_ADC_Start_DMA()启动ADC和DMA：
![](ADC-DMA-UART.assets\ref2.png)

### 3. UART输出

    int startAddr = 0;
    void HAL_ADC_ConvHalfCpltCallback(ADC_HandleTypeDef* hadc)
    {
      HAL_UART_Transmit_DMA(&huart1, (uint8_t*)AdcBuf + startAddr, (uint16_t)36);
      if (startAddr == 36)
        startAddr = 0;
      else
        startAddr = 36;
    }

HAL_ADC_ConvHalfCpltCallback()是HAL库用弱定义提供的回调函数，在ADC缓冲区半满时调用。前半缓冲区填满后，上述片段将前半缓冲区发送到UART，同时ADC继续填充（覆写）后半缓冲区；后半填满后则通过UART发送后半。

HAL_UART_Transmit_DMA()将缓冲区中的数据通过DMA传送到UART控制器：

![](ADC-DMA-UART.assets\ref3.png)

## 实验

PA6随便接点东西就好，这里用一个Buck Converter当信号源。忽略多余的杜邦线，都是控制Buck用的。

![](ADC-DMA-UART.assets\exp.jpg)

串口输出：

![](ADC-DMA-UART.assets\exp2.png)
