# 《嵌入式软件开发》未名team项目——****系统

---

小组成员：闫 妍  陈宇翔 张 岩 耿泽浩 李伟 黄顺伟  
指导老师：郭文海教授  
日期：2019年3月

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

