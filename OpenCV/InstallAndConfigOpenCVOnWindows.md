# 在Windows中安装并配置OpenCV

## OpenCV

[OpenCV](https://github.com/opencv/opencv)（Open Source Computer Vision Library)是一个计算机视觉领域赫赫有名的开源库，即它的所有源代码（除了那些涉及到他人知识产权的部分）都是向公众开放的。它是跨平台的，也就是说它支持不同的操作系统：Windows，Linux，MacOS， Android，iOS，不同平台上支持的特性并不完全一致，性能也并不一致。它是跨语言的，这意味着你可以通过C++, Python, Java等语言调用它的库。

其代码托管于[Github](https://github.com/opencv/opencv)。 Github是一个代码托管网站，很多程序员将自己的开源程序的源代码托管在这里。

截至本文完成时（2019.12.12），OpenCV最新的Release版本是4.1.2, OpenCV4和OpenCV3有一定的差别，现在网上的资料多是OpenCV3版本的。

鼓励大家尝试新版本，我们将以4.1.2版本为例介绍安装与配置，OpenCV3的安装配置与OpenCV4并无差别。

OpenCV可以从源码进行编译（较复杂，可以提供更多更复杂的功能），也可以用预编译版本安装，我们将从预编译版本安装。

## 安装OpenCV

### 1、获取并安装OpenCV
[点击此链接获取4.1.2的安装包](https://sourceforge.net/projects/opencvlibrary/files/4.1.2/opencv-4.1.2-vc14_vc15.exe/download)

你也可以在[这里](https://opencv.org/releases/)或者[Github上](https://github.com/opencv/opencv/releases)获取其他版本的安装包。

双击安装包，按照提示选择安装位置（一般设在D盘或者其他非系统盘）
![extract](extract.PNG)

然后点击Extract.

其实这个安装包做的工作只是为你解压了一下文件而已，现在OpenCV已经安装好了。

### 2、获取并安装CMake

在[官网](https://cmake.org/download/)获取安装包并安装。

### 3、配置环境变量

如图所示，点击配置环境变量
![path](path.PNG)

1、双击Path并新建两个变量

2、在创建一个名为OpenCV_DIR的用户变量，值为opencv安装位置下的build文件夹的路径（图中未画出）

## 编写CMakeLists.txt
cmake 通过CMakeLists.txt中的信息来配置项目，我们要做的便是在CMakeLists.txt中指定项目的配置要求，以下是一个CMakeLists.txt的例子

    # 以#号开头的行是注释，#在cmake中被理解为注释符号
    # 指定cmake的最小版本，小于这个版本的将无法编译
    cmake_minimum_required(VERSION 2.8) 
    # 创建一个名字叫做 LearningOpenCV3的cmake项目（对应VS中的解决方案）
    project( LearningOpenCV3 )

    # 找到Opencv包，也就是OpenCVConfig.cmake所在的路径
    # 如果不能自动找到，则要稍后手动指定
    find_package( OpenCV REQUIRED )
    # 引入包含目录，创建后的VS项目将可以从这个路径寻找Opencv相关的头文件
    include_directories( ${OpenCV_INCLUDE_DIRS} )

    # COMPILE EXAMPLES
    # 创建了三个可执行文件（对应VS中的项目）
    # 第一个参数是项目名称，之后的参数数目不限，是所用文件的名称
    add_executable( video video.cpp )
    add_executable( mat mat.cpp)
    add_executable( draw draw.cpp)
    ################
    ###   LINK   ###
    ################
    # 为可执行文件配置静态链接库
    target_link_libraries( video ${OpenCV_LIBS} )
    target_link_libraries( mat ${OpenCV_LIBS} )
    target_link_libraries( draw ${OpenCV_LIBS} )

## 配置
打开cmake



![](cmake.PNG)


0. 将source code位置指定为你的CMakeLists.txt所在位置. where to build the binaries是生成的解决方案所在位置，一般设为source code文件夹下的build文件夹
0. 确认CMakeLists.txt中用到的文件已经被创建, 否则会报错
1. 点击config, 如果有弹框,  选择你的vs版本和平台(一般是x64)
2. 如果OpenCV_DIR显示未找到，则手动指定为你的opencv安装位置下OpenCVConfig.cmake文件所在路径。
3. 再次config
4. 点击generate
5. 解决方案以及成功生成，可以点击open project直接打开，也可以到build文件夹下打开