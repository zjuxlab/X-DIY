# TFTLCD

！！！还没写完 ！！！

[TOC]

***TFT-LCD*** 即薄膜晶体管液晶显示器。其英文全称为： ***Thin Film Transistor-Liquid Crystal Display*** 。TFT-LCD与无源TN-LCD、STN-LCD的简单矩阵不同，它在液晶显示屏的每一个像素上都设置有一个薄膜晶体管 ***(TFT)*** ，可有效地克服非选通时的串扰，使显示液晶屏的静态特性与扫描线数无关，

***TFTLCD*** 的通信方式对于不同的MCU有所不同， ***16bit*** 并口通信， ***SPI*** 通信， ***IIC*** 通信等。根据触摸的采样方式不同， ***TFTLCD*** 又可分为电阻屏与电容屏。本文将以 ***stm32*** 驱动 ***16bit*** 并口传输屏和 ***esp32*** 驱动 ***SPI*** 屏为例子讲解 ***TFTLCD*** 电阻屏的驱动过程，其中stm32的开发环境是 ***stm32cubemx*** ，esp32的开发环境是 ***esp-arduino*** 。

## 驱动原理

多数TFTLCD屏幕的驱动MCU自带显存，例如ILI9341，其显存总大小为172800（240×320×18/8），即18位模式（26万色）下的显存量。在16位模式下，ILI9341采用RGB565格式存储颜色数据（在更高级的32位RGB储存颜色中还有RGBA888，Linux开发板中较为常见），此时ILI9341的18位数据线与MCU的16位数据线以及LCD GRAM的对应关系如图所示：

<center class ='img'>
<img title="颜色数据与总线数据对应关系" src="TFTLCD_assets/LCD总线与颜色数据对应关系.png" width="90%">
</center>

意思是通过一种通信方式把这16bit数据往屏幕里送即可显示像素。另外还需要明确一些读写的驱动指令，这些底层驱动一般由官方/产家写好代码给出，我们只需要直接调用或者迁移即可，故本文放在代码里面一块阐释。

驱动参考，可见[正点原子LCD液晶屏](http://openedv.com/docs/modules/lcd/index.html "正点原子LCD液晶屏")，或读者购买的屏幕的淘宝商家，一般都可以要到对应的资料

## 16bit并口通信

本文以常用的 ***ILI9341*** 驱动的 ***2.8寸*** 屏为例。

CubeMX配置如下

<center class ='img'>
<img title="CubeMX配置" src="TFTLCD_assets/16bit并口屏cubemx配置.png" width="65%">
</center>

其中带TOUCH前缀的是触摸相关引脚。

一般驱动库都会带一个初始化屏幕参数的头文件，用注释的方式选择自己屏幕的驱动MCU，以及一些屏幕分辨率参数等。

找到用于显示屏幕的API函数

<center class ='img'>
<img title="CubeMX配置" src="TFTLCD_assets/16bit_displayAPI.png" width="65%">
</center>

// 这一小节我写不来，蹲一个人写得更详细一点






## SPI通信

本小节使用的库是经典的eTFT_SPI库，可以在arduino中安装



## 参考资料

[STM32学习记录0010——TFTLCD液晶显示屏](https://blog.csdn.net/qq_43441284/article/details/124413430 "STM32学习记录0010——TFTLCD液晶显示屏")
