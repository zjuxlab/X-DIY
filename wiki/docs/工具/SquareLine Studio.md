# SquareLine Studio

***SquareLine Studio*** 是一款 ***[LVGL (Light and Versatile Embedded Graphics Library)](https://lvgl.io/)*** 官方推出的图形化配置工具，用户可在 ***SquareLine Studio*** 中绘制好需要的UI界面后将驱动库和UI文件导出，配合单片机环境继续开发。

## 软件安装

进入[官网](https://squareline.io/downloads#lastRelease "SquareLine Studio Download Archive")下载即可。注意，最新版的仅支持30天的免费试用，最好是下载低于最新版一个版本的，可以一直免费使用。

安装好后使用邮箱注册账号并且登录，即可开始使用。

## 新建工程

点击弹出来的小窗顶栏中间的 ***Create*** ，开始新建工程。

<center class ='img'>
<img title="Create Project" src="SquareLineStudio.assets\create_project.png" width="45%">
</center>
***SquareLine Studio*** 提供了一些可选择的驱动库，比较常用的是 ***Arduino with TFT_eSPI*** ，适用于arduino开发和esp-arduino开发。以下均以使用 ***TFT_eSPI*** 驱动的 ***esp-arduino*** 开发为例子演示操作过程。

右下角可以设置项目的一些初始化参数，比较重要的是 ***Resolution*** 和 ***Color depth*** 两项。

<center class ='img'>
<img title="Project Settings" src="SquareLineStudio.assets\project_settings.png" width="45%">
</center>

***Color depth*** 需要根据自己选择的屏幕参数来定，比如最经典的SPI电阻屏，像素色彩为RGB565格式，色深度是16，这里就填16。

新建成功后的界面大致如下：

<center class ='img'>
<img title="Project Init" src="SquareLineStudio.assets\project_init.png" width="45%">
</center>

左边有可以选择放置的控件，右边可以配置控件以及屏幕的相关参数。

## UI示例

读者可以自己研究一下样式，根据自己的使用需求做出好看的UI。以下举几个例子做演示。

### 按钮边界模糊化

参数设定与出来的效果：

<center class ='img'>
<img title="Button Settings" src="SquareLineStudio.assets\button_settings.png" width="20%">
<img title="Button Blur" src="SquareLineStudio.assets\button_blur.png" width="45%">
</center>

### 按钮回调函数

我们可以在 ***SquareLine Studio*** 中对控件添加回调函数，便于后续结合单片机进一步开发。

如下图，我将按钮的回调函数名称设为***I_AM_A_BUTTON***，触发方式为 ***RELEASED***。

<center class ='img'>
<img title="Call Function" src="SquareLineStudio.assets\call_function.png" width="45%">
</center>

导出后的回调函数会在 ***ui_events.c*** 文件中，会在后面进一步阐述。

### 屏幕跳转

***SquareLine Studio*** 做屏幕跳转逻辑非常方便。

<center class ='img'>
<img title="Change Screen" src="SquareLineStudio.assets\change_screen.png" width="45%">
</center>

与按钮回调函数一样，选择一个按钮，添加屏幕跳转事件。比较重要的是既要添加 ***CHANGE SCREEN*** 跳转到目标屏幕，也要添加 ***DELETE SCREEN*** 删除当前所在屏幕。

## 工程仿真

***SquareLine Studio*** 自带了仿真器，在界面设计的右上角，有一个播放型的按键。开始仿真后可以验证设计的屏幕逻辑与控件效果。

<center class ='img'>
<img title="Project Simulate" src="SquareLineStudio.assets\project_simulate.png" width="45%">
</center>

## 工程导出

设计好的项目可以通过两种方式导出：仅导出UI文件和导出整个工程模板。我一般使用导出整个工程文件，结构完整，在其中取出需要的文件使用相对清晰。

<center class ='img'>
<img title="Project Export" src="SquareLineStudio.assets\project_export.png" width="45%">
</center>

在 ***Arduino with TFT_eSPI*** 的驱动库下，导出的工程模板主文件为ino文件。整个导出文件夹的结构如下

<center class ='img'>
<img title="Export Files" src="SquareLineStudio.assets\export_files.png" width="45%">
</center>

其中， ***TFT_eSPI*** 包括的是底层的SPI驱动代码，可以在其中定义驱动引脚，屏幕驱动芯片型号等。 ***lvgl*** 包括了所有与lvgl的底层文件文件， ***UI*** 包括了所有UI相关的文件，包括控件的定义，回调函数的定义等。

## 工程使用

使用时只需要把 ***libraries*** 目录下的文件全拷贝到自己的库里，应用示例可参考 ***ui.ino*** 。后续若对UI有改动，重新导出了一次工程，只需要替换 ***ui*** 文件夹中的内容即可。

***TFT_eSPI*** 相关内容在外设库的TFTLCD下做了解释，可结合起来阅读，这里就不赘述了。

***UI*** 文件夹下的内容最重要，也是后续与单片机开发最紧密相关的。

<center class ='img'>
<img title="Button Function" src="SquareLineStudio.assets\button_function.png" width="45%">
</center>

回调函数定义在 ***ui_events.c*** 文件下，在库中引用代码会显示找不到路径，不能在这里添加功能代码。

最简单的解决办法是在 ***ui_events.c*** 下定义一个变量表示按钮值，然后在自己的代码头文件中声明这个变量作为外部变量。在回调函数中改变这个按钮值，自己的函数中若检测到这个按钮值发生改变，即可判定为按钮按下，进而执行功能代码。

## 优秀教学视频链接

[Create and Export Stunning UI with SquareLine Studio for ESP32 Display | LVGL Tutorial](https://www.youtube.com/watch?v=Ls0uLyeAgiw "Create and Export Stunning UI with SquareLine Studio for ESP32 Display | LVGL Tutorial")