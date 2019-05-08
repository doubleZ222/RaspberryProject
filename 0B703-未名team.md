# 《嵌入式软件开发》未名team项目—基于树莓派的会话式智能家居系统

---

小组成员：闫 妍  陈宇翔 张 岩 耿泽浩 黄顺伟 李伟  
指导老师：郭文海教授  
日期：2019年4月

## 第一章	开发环境 ##

###  **1. 主机端开发环境** ###

**a)	硬件开发环境**  
	处理器：Intel Core i5-3210M CPU @ 2.5GHZ  
	内存：8.00GB  
	显卡：NVIDIA GeForce 610M   
	硬盘：INTEL 256GB SSD  
	网络：百兆网卡+百兆局域网  
**b)	软件开发环境**  
	操作系统：Windows10  
	虚拟机：VirtualBox + Ubuntu 14.04（64bit）  
**c)	编程环境**  
	C/C++：Visual Studio 2017  
	Python：python3



###  **2. 树莓派端开发环境**   ###
 **a)	硬件开发环境**  
树莓派是一款基于ARM的微型电脑主板，以SD/MicroSD卡位内存硬盘，卡片主板周围有USB接口和以太网接口，可连接键盘、鼠标和网线，同时拥有视频模拟信号的电视输出接口和HDMI高清视频输出接口。
ARM处理器是英国Acorn有限公司设计的低功耗成本的第一款RISC微处理器。全称为Acorn RISC Machine。ARM处理器本身是32位设计，但也配备16位指令集，一般来讲比等价32位代码节省达35%，却能保留32位系统的所有优势。
树莓派的结构框图如下：  
![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/1_1.png)  
普通的计算机主板都是依靠硬盘来存储数据，但是Raspberry Pi 来说使用SD 卡作为硬盘，也可以外接USB 硬盘。SD卡是基于半导体快闪记忆器的新一代记忆设备，它体积小、数据传输速度快、可热插拔，广泛应用于便携式装置。
	具体的硬件参数如下：  
	处理器：1.2GHz 四核 64位ARM Cortex-A53处理器  
	系统芯片：BCM2837  
	图形卡：Broadcom VideoCore IV，OPENGL ES2.0，1080p 30 h.264/MPEG-4 AVC高清解码器  
	内存：1GB RAM  
	视频输出：RCA视频接口输出，支持PAL和NTSC制式，支持HDMI。  
	音频输出：3.5mm插孔，HDMI  
	标准SD卡接口：Micro SD卡接口  
	网络：10/100以太网接口，Wi-Fi，蓝牙  
具体接口分布如图：  
![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/1_2.jpg)  
**b)	软件开发环境**  
	操作系统：支持Ubuntu Snappy Core，Raspbian，OpenELEC和RISC OS，Windows 10 ARM等多个操作系统。我们使用的是Raspberry Pi 3  
**c)	编程环境**  
	Python：树莓派3B采用的官方编程语言是Python，系统自身便已经自带了python编程环境  
	其他：除了Python语言，树莓派还支持其他多种语言，支持多种IDE，因此可以根据自己的需要自由的选择编程语言  

## 第二章	需求分析 ##
###  **1. 项目名称** ###
**基于树莓派的会话式智能家居系统**

###  **2. 项目架构** ###

下图为系统总体设计架构图，包含了MQTT broker、Sqlite3数据库、传感器节点以及MQTT通信概念，图上所标注的Arduino虚线框内为传感器节点，为原系统的网关与传感器节点，红色虚线匡则是代表运行平台为Raspberry Pi。

![架构图](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/2_1.jpg)

###  **3. 功能需求分析** ###
**a)	功能需求**  
	通过arduino等对家电进行控制  
	温度湿度等传感节点的数据采集 
	语音交互功能  
	web等终端交互功能  

**b)	非功能性需求**  
	数据精度调节
	命令响应时间

###  **4. 所需的软硬件条件** ###

**a)	软件条件**  
	软件应能够实现家电设备的控制，传感节点的mqtt通信，sqlite数据存储，语音识别、unit语义识别
**b)	硬件条件**  
   树莓派、arduino 、温湿度传感器、wifi模块、

## 第三章	构建目标系统 ##

###  **1. 配置编译raspberry内核需要的各种环境** ###

[1] 更新系统源：sudo apt-get update

[2] 安装一些必要的工具库：sudo apt-get install -y bc build-essential gcc-aarch64-linux-gnu git unzip

[3] 安装配置系统内核包：sudo apt-get install kernel-package 

[4] 安装配置内核menuconfig的辅助工具：sudo apt-get install libncurses5-dev

###  **2. 下载raspberry Linux内核源代码** ###

[1] 获取源代码：git clone --depth=1 -b rpi-4.8.y https://github.com/raspberrypi/linux.git


###  **3. 配置新内核功能** ###

 [1]不自己配置内核，将路径切到下载的linux源码路径下生成这个.config内核配置文件，执行命令：make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcmrpi3_defconfig
 [2] 自己配置内核：同上执行命令：make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- menuconfig 
 
 ###  **4. 构建新内核** ###

完成内核配置后便开始编译，执行命令： make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 其中 j8 代表用多少个CPU核来进行编译。
 
 ###  **5. 将新内核安装到镜像文件系统中** ###
[1]挂载img镜像文件
sudo fdisk -l 2019-04-08-raspbian-stretch-lite.img

sudo mount -o loop,offset=50331648 2019-04-08-raspbian-stretch-lite.img /mnt
sudo mount -o loop,offset=4194304,sizelimit=44979712 2019-04-08-raspbian-stretch-lite.img /mnt/boot

[2]替换原来的内核

在编译生成的目录下把刚刚生成的内核文件Image拷贝到/mnt/boot/下 sudo cp ./arch/arm64/boot/Image /mnt/boot/kernel8.img

然后再拷贝系统启动的dtb文
sudo cp ./arch/arm64/boot/dts/broadcom/bcm2710-rpi-3-b.dtb /mnt/boot/
sudo cp ./arch/arm64/boot/dts/broadcom/bcm2837-rpi-3-b.dtb /mnt/boot/

[3]安装内核模块并配置内核
sudo make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=/mnt modules_install

[4]配置新的镜像：sudo echo kernel=kernel8.img >> /mnt/boot/config.txt


