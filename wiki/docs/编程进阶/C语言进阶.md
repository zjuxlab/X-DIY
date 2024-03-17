# C语言进阶

[TOC]

## 写在前面

查看本文的前提是你已经有了C语言的语法基础，能够编写并在一些IDE（如Dev C++、Visual Studio、CLion等）中运行你的程序，但你并不清楚C语言的程序是如何编译的，以及你的IDE到底帮你做了哪些事情，当程序变成多文件编程时又该怎么处理。

## 如何运行一个C语言程序

### Dev C++等IDE帮我们做了哪些事情（点一个编译运行）？

- 预处理：删掉所有的注释、宏拓展、文件包含，处理结束后会产生一个后缀为.i的临时文件
- 编译：将.i文件转换成.s文件（汇编语言）
- 汇编：使用汇编程序将.s文件转换为机器码(.o文件)
- 连接：由.o文件生成可执行文件（.exe）

### 不用Dev C++怎么进行这个过程？

以hello world程序为例，C语言代码如下：

```c
#include <stdio.h>

int main() 
{
    printf("Hello World!");
}
```

接下来我们在命令行中编译运行这个程序：

```shell
gcc -save-temps demo.c -o demo
```

其中`gcc`是编译器的名称，`-save-temps`表示保留所有编译过程中产生的文件（因此在不需要保留这些文件的时候可以不加这句话），`demo.c`是你的文件名称，`-o demo`表示生成的可执行文件名称叫做`demo`。

之后我们再执行这个可执行文件，即在命令行中输入:

```shell
./demo.exe
```

.表示当前目录，`./demo.exe`即表示运行当前目录下的`demo.exe`这个文件，即可发现在终端中输出了`Hello World!`

## 在VSCode上配置C/C++

- 安装拓展C/C++
- 安装拓展Code Runner

Code Runner会在右上角提供一个小三角号，点击它即可发现在终端中多出这样一句话：

```shell
cd 你的当前目录 ; if ($?) { gcc demo.c -o demo } ; if ($?) { .\demo }
```

`cd`的作用是跳转到某一目录下，类似图形界面鼠标点击文件夹

`$?`是命令行中返回上一个函数返回值的特殊变量，若其为“真”，这说明上一条命令成功执行，因此下面的命令可以执行。所以不从安全性上考虑的话，这里的`if ($?)`不加问题也不大

`gcc demo.c -o demo`是上面我们提到的生成可执行文件的过程

`.\demo`是运行可执行文件的过程  

因此猜都不用猜我们就知道命令行窗口的下一行一定会出现Hello World！

至此，我们完成了在VSCode中一键运行C语言代码的配置

## 多文件的处理方式——Makefile

上面说了那么多，归根结底都是基于我们的程序只有一个`.c`文件的，但在实际工程开发中如果你的代码真的都塞到了一个文件里那我会第一个鲨了你(x

说回正题，相信大家的C大程课也都强调了多文件编程的重要性，那么在我们不依赖IDE的情况下，如何进行多文件编程呢？答案就是我们这里要讲的Makefile。

### Makefile的作用

Makefile用于处理复杂的文件包含关系并告诉编译器编译的顺序，说白了就是帮你一块写多个gcc的命令

这里我们举个简单的例子：

首先我们先写好多个文件

`print.h`文件

```C
#ifndef _PRINT_H
#define _PRINT_H

#include <stdio.h>

void print();

void print1();

#endif
```

`print.c`文件

```C
#include "print.h"

void print()
{
    printf("Hello World!");
}
```

`print1.c`文件

```C
#include "print.h"

void print1()
{
    printf("Hello XLab!");
}
```

`main.c`文件

```C
#include "print.h"

int main()
{
    print();
    print1();
}
```

如果我们直接手写gcc编译这个“工程”是这样的：

```SQL
gcc main.c print.c print1.c -o main
```

之后会在该目录下生成`main.exe`，之后按照先前的套路运行`./main`即可。

但是注意到这个时候我们只有3个`.c`文件，这么做还是很方便的，那当我们有很多`.c`文件的时候呢？难道要一个一个把它们加进去吗？显然这是低效的，Makefile可以帮助我们高效地实现这一过程，且在我们增删文件的时候不受影响。

> 这里有一个准备工作，如果你们的电脑里安装了Dev C++，那么就意味着你们已经安装好了MINGW（一个集成了`gcc.exe`,`g++.exe`以及我们将要用到的`make.exe`的东西），你们可以右键单击Dev C++的图标选择【打开文件所在位置】，之后点进去找到MINGW的文件夹，点开bin目录就可以看到很多`.exe`文件，一般情况下我们要用到的`make.exe`在这里叫做`mingw32-make.exe`

我们在原目录下新建Makefile文件：

```Makefile
main: main.c print.c print1.c
    gcc -o main main.c print.c print1.c
```

之后在终端中运行`mingw32-make`可以生成对应的`main.exe`文件

第一行的意思是，`main`依赖于后面这三个.c文件，第二行则表明生成`main`的方式，很显然的一点是我们直接运行第二行能得到相同的结果，因此我们实际上并没有进行优化，因此我们就要用到Makefile特定的语法。

### Makefile的基本语法

- 变量定义：在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点像C语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。
- 显式规则：显式规则说明了如何生成一个或多的的目标文件。这是由Makefile的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。 刚才写的全部都是显示规则。
- 隐晦规则：由于我们的make有自动推导的功能，所以隐晦的规则可以让我们比较粗糙地简略地书写Makefile，这是由make所支持的。
- 文件指示：其包括了三个部分: 一个是在一个Makefile中引用另一个Makefile, 就像C语言中的`include`一样; 另一个是指根据某些情况指定Makefile中的有效部分, 就像C语言中的预编译`#if`一样; 还有就是定义一个多行的命令.
- 注释：与python相同，#后面的均是注释内容

**一些预定义变量的介绍：**

- `$*` 　　不包含扩展名的目标文件名称。
- `$+` 　　所有的依赖文件，以空格分开，并以出现的先后为序，可能包含重复的依赖文件。
- `$<` 　　第一个依赖文件的名称。
- `$?` 　　所有的依赖文件，以空格分开，这些依赖文件的修改日期比目标的创建日期晚。
- `$@` 　  目标的完整名称。
- `$^` 　　所有的依赖文件，以空格分开，不包含重复的依赖文件。
- `$%`      如果目标是归档成员，则该变量表示目标的归档成员名称。

接下来，我们利用这些预定义的变量再加上上述的规则重新写一份Makefile：

```Makefile
CXX = gcc     # 确定编译器是gcc，先把它写在一个变量里，后面如果想要改编译器的话直接改CXX这个变量的value就可以
TARGET = main # 确定最后生成可执行文件的名称，也是方便后续更改所以放在一个变量里
SRC = $(wildcard *.c) # 开始抽象了，这里wildcard是通配符，表示“*”这个符号具有通配符的功能，*.c可以表示当前目录下的所有.c文件，$()表示括号内的内容是SRC所表示的部分
OBJ = $(patsubst %.c, %.o, $(SRC)) # 更抽象了，这里patsubst是一个把SRC中所有的.c文件替换成.o文件的函数

CXXFLAGS = -c -Wall # 这句话的意思是用CXXFLAGS这个变量去替换-c -Wall这两个命令，-c是编译compile，-Wall是显示所有的warning（例如变量未使用之类的）

$(TARGET): $(OBJ)   # 这两行的意思是，TARGET是依赖于OBJ的，也就是最后的.exe文件依赖于所有的.o文件，下面的$@表示目标文件，$^表示所有的依赖文件，%<表示第一个依赖文件，这两行翻译过来就是：gcc main.c print.c print1.c -c，也就是把所有的.c文件全都编译
    $(CXX) -o $@ $^
# 这两行翻译过来就是：gcc -c -Wall main.o print.o print1.o -o main
%.o: %.c         
    $(CXX) $(CXXFLAGS) $< -o $@
# .PHONY表示后面那个目标（这里就是clean）是个伪文件目标，也就是说它没有依赖文件，是一个只执行的命令，不会出现对应的clean文件
.PHONY: clean 
# 说明clean这个命令具体要干什么事情，下面那一行表示把TARGET下所有的.o文件都删除，方便下一次更改文件后重新编译
clean:
    rm -f *.o $(TARGET)
```

你会发现，在这个文件里，无论你的代码文件结构怎样改动，这个Makefile都不用改，一劳永逸。

至此，我们已经具备了告别IDE，实现用VSCode编写单文件/多文件的C语言工程的能力！

## 多文件的处理方式（进阶）——CMake

虽然在上面，我们已经成功做到了不用IDE进行多文件程序的编译运行，但如果一个工程的所有文件都放在一个文件夹下的话也是很丑的，也不利于后续的维护。并且，如果我们后续开发过程中遇到了不同系统不同编译器的问题，我们就要写多个Makefile，十分不便，因此我们就要引入更进阶一层的工具——CMake。CMake是一个将多个文件（如C、C++、python等）组合构建成一个大工程的语言，能够输出Makefile和project文件。

### CMake的安装

[这篇文章](https://blog.csdn.net/m0_55048235/article/details/122277696)写得很详细，不多赘述。

### CMake的基本语法

先给如下一个案例，我们在之前的文件里面删掉Makefile，加入一个`CMakeLists.txt`文件

```CMake
PROJECT (MAIN)
SET(SRC_LIST main.c print1.c print.c)
message(STATUS "This is BINARY dir" ${MAIN_BINARY_DIR})
message(STATUS "This is SOURCE dir" ${MAIN_SOURCE_DIR})
add_executable(main ${SRC_LIST})
```

#### PROJECT关键字

`PROJECT(main)`指定了工程的名字是main，并且支持所有语言。如果想要支持特定的语言可在后面加语言名称，如C CXX(C++)等。并且这个指令同时定义了两个变量`<projectname>_BINARY_DIR`以及`<projectname>_SOURCE_DIR`，这两个变量指向当前的工作目录，前者是二进制文件保存目录，后者是源代码的保存路径。

#### SET关键字

用来指定变量，例如`SET(SRC_LIST main.c print1.c print.c)`就指定SRC_LIST为后面那几个`.c文件

#### MESSAGE关键字

向终端输出用户自定义的信息，主要包含如下三种：

- `SEND_ERROR`:产生错误，生成过程被跳过
- `STATUS`:输出前缀为-的信息
- `FATAL_ERROR`:立即终止所有CMake进程

#### add_executable关键字

`add_executable(main ${SRC_LIST})`生成依赖于那几个`.c`文件的`main.exe`

#### aux_source_directory关键字

`aux_source_directory(dir,var)` 第一个参数`dir`是指定目录，第二个参数`var`是用于存放源文件列表的变量。

#### include_directories关键字

用来向工程添加多个指定头文件的搜索路径，路径之间用空格分隔。

#### 可能用到的预定义变量

- `EXECUTABLE_OUTPUT_PATH` ：目标二进制可执行文件的存放位置
- `PROJECT_SOURCE_DIR`：工程的根目录

### 正规的工程文件组织结构

- build文件夹：主要放置临时生成的文件

- bin文件夹：主要存放二进制文件

- lib文件夹：用来存放一些库文件

- main文件夹（一般还要细分成src和inc文件夹）：一个放源代码，一个放头文件

- 之后还要包括README、Makefile等文件

- `CMakeLists.txt`文件：

  ```CMake
  cmake_minimum_required (VERSION 2.8)
  
  project (main)
  
  set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
  
  aux_source_directory (src SRC_LIST)
  
  include_directories (include)
  
  add_executable (main ${SRC_LIST})
  ```

### 编译流程

- 进入到build目录下：`cd build`
- 运行cmake的命令：`cmake ..`（..是指上一级目录，也就是说，我们要在build目录下运行它上一级目录也就是我们的工程文件夹下的cmake，这样做的目的是把cmake生成的乱七八糟的文件都放在build目录下）

注意，如果你之前惯用dev c++，这里最好运行命令`cmake -G "MinGW Makefiles" ..`，这样可以指定MinGW中的gcc作为你的编译器，以免Makefile的生成出错（针对电脑中同样用Microsoft的c编译器）

- 这时已经可以看到`build`目录下生成了Makefile文件，之后我们去运行这个Makefile
- 最后我们即可发现在`bin`目录下生成了我们需要的`.exe`文件

> 不要让你的路径出现中文，不然你会像我一样调一晚上也不知道哪出了问题