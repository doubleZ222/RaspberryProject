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

 [3] 编译错误清理：sudo make distclean

 ###  **4. 内核裁剪** ###

 **a)	Loadable module support**  

Loadable module support即引导模块支持，该选项包括加载模块、卸载模块、模块校验、自动加载模块等引导模块配置相关子选项

[1] Enable loadable module support 

打开可加载模块支持，如果打开它则必须通过make modules_install把内核模块安装在/lib/modules/中。模块是一小段代码，编译后可在系统内核运行时动态地加入内核，从而为内核 加一些特性或是对某种硬件进行支持。一般一些不常用到的驱动或特性可以编译为模块以减少内核的体积。在运行时可以使用modprobe命令来加载它到内核中去。一些特性是否编译为模块的原则有不常使用的，或是在系统启动时不需要的驱动可以将其编译为模块，如果是一些在系统启动时就要用到的驱动，比如说文件系统，系统总线的支持就不要编为模块，否则无法启动系统。在启动时不用到的功能编成模块是最有效的方式。

[2]Module unloading 

允许卸载已经加载的模块。如果选择N，将不能卸载任何模块（有些模块一旦加载就不能卸载，不管是否选择了这个选项）。
其中，Forced module unloading子选项允许强制卸载正在使用中的模块，即使内核认为这不安全，内核也将会立即移除模块，而不管是否有人在使用它（用rmmod -f命令）。


 **b)	 General setup**  

这里是对最普通的一些属性进行设置。这部分内容非常多，一般使用缺省设置就可以了。

Networking support：网络支持。必须，没有网卡也建议你选上。

PCI support：PCI支持。如果使用了PCI的卡，当然必选。

PCI access mode：PCI存取模式。可供选择的有BIOS、Direct和Any，选Any吧。

Support for hot-pluggabel devices：热插拔设备支持。支持的不是太好，可不选。

PCMCIA/CardBus support：PCMCIA/CardBus支持。有PCMCIA就必选了。

System V IPC

BSD Process Accounting

Sysctl support：以上三项是有关进程处理/IPC调用的，主要就是System V和BSD两种风格。如果你不是使用BSD，就按照缺省吧。

Power Management support：电源管理支持。

Advanced Power Management BIOS support：高级电源管理BIOS支持。

 **c)	Device Drivers**  

Device Drivers即设备驱动，该选项包括内核所支持的各类硬件设备的配置信息

[1]Input device support

用于选择输入设备的驱动支持

[2]I2C support

I2C是Philips极力推动的微控制应用中使用的低速串行总线协议

[3]Sound

声卡驱动支持


 **d)	File systems**  

文件系统,在缺省选项的基础上进行修改。介绍以下几项：

Quota support：Quota可以限制每个用户可以使用的硬盘空间的上限，在多用户共同使用一台主机的情况中十分有效。

DOS FAT fs support：DOS FAT文件格式的支持，可以支持FAT16、FAT32。

ISO 9660 CD-ROM file system support：光盘使用的就是ISO 9660的文件格式。

NTFS file system support：ntfs是NT使用的文件格式。

 ###  **5. 构建新内核** ###

完成内核配置后便开始编译，执行命令： make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j8 其中 j8 代表用多少个CPU核来进行编译。

不裁剪内核大小

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-4.png)


裁剪内核大小

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-5.png)


 ###  **6. 将新内核安装到镜像文件系统中** ###

[1]挂载img镜像文件

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-1.png)


sudo fdisk -l 2019-04-08-raspbian-stretch-lite.img

sudo mount -o loop,offset=50331648 2019-04-08-raspbian-stretch-lite.img /mnt

sudo mount -o loop,offset=4194304,sizelimit=44979712 2019-04-08-raspbian-stretch-lite.img /mnt/boot

[2]替换原来的内核

在编译生成的目录下把刚刚生成的内核文件Image拷贝到/mnt/boot/下 sudo cp ./arch/arm64/boot/Image /mnt/boot/kernel8.img


[3]然后再拷贝系统启动的dtb文

sudo cp ./arch/arm64/boot/dts/broadcom/bcm2710-rpi-3-b.dtb /mnt/boot/

sudo cp ./arch/arm64/boot/dts/broadcom/bcm2837-rpi-3-b.dtb /mnt/boot/


[4]安装内核模块并配置内核

sudo make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=/mnt modules_install

[4]配置新的镜像：sudo echo kernel=kernel8.img >> /mnt/boot/config.txt

 ###  **7. 烧写到SD卡** ###

[1]使用lsblk命令查看SD卡分区name，我的SD卡在/dev/mmcblk0


[2]挂载SD卡分区：sudo mount /dev/mmcblk0  /mnt


[3]使用dd命令烧录镜像到SD卡中

sudo dd bs=4M if=2019-04-08-raspbian-stretch-lite.img of=/dev/sdb4

bs代表一次写入多大的块，是blocksize的缩写，4M一般都没问题，如果不行，试试改成1M，if参数为下载的镜像的路径，of后参数为设备地址。

sudo sync 确保烧写无误。
![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-2.png)

烧写结束后df -hl 查看sd卡大小：
![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-3.png)



[4]卸载分区

sudo umount mnt/

sudo umount mnt/boot


 ###  **8. 安装后的配置及文件系统** ###
 **a)	   无裁剪的系统**  

[1]查看内核版本及初始分区情况

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-6.png)

[2]内存扩充情况

使用sudo fdisk /dev/mmcblk0 新建分区
p查看已有分区
n 新建分区，完成后w保存退出，sudo reboot 重启，开机 sudo resize2fs /dev/mmcblk0p3
df -h 查看新建情况。

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-9.png)


 **b)	   裁剪的系统**  

[1]查看内核版本

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-8.png)

[2]查看初始分区情况

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-7.png)


[3]模块卸载

卸载i2c_bcm2708并查看安装的模块信息，看是否卸载成功。

sudo modprobe -r i2c_bcm2708
pi@raspberrypi:~ $ lsmod

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-10.png)


[4]模块加载


加载i2c_bcm2708并查看安装的模块信息，看是否加载功。

 sudo modprobe i2c-bcm2708 
pi@raspberrypi:~ $ lsmod

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/3-11.png)

sudo fdisk /dev/mmcblk0

## 第四章 实现方法

### 4.1方案概述

#### 4.1.1系统架构概览

​	本文研究对象是嵌入式智能环境监控和设备控制系统，智能家居这个议题已经讨论了许久，在这方面也有许多的应用与产品，但至今仍无法普及于生活之中，主要是因为各家厂商间没有一个通用的标准，并且难以兼容现有的设备，以及操作的复杂性，故我们目标为运用现阶段较成熟且广泛应用的技术来设计与实现会话式交互智能家居系统。系统架构图如下：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-1.png)

​	系统架构图包含了MQTT Broker、MQTT客户端、SQLite3数据库、Django Web应用、Flask API 应用、以及所使用的云服务技术（百度Unit和百度语音识别服务）、Arduino物联网结点等。其中虚线左侧为树莓派服务门户，右侧为物联网结点。物联网节点使用WIFI协议与树莓派服务门户连接，通过MQTT与树莓派门户进行通信。

#### 4.1.2系统主要流程

​	本系统可以分别通过语音和Web端来控制智能家居系统。系统的主流流程图如下：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-2.png)

**语音控制时的基本流程如下：**

- 先通过语音识别将语音转化为文本

- 调用unit将文本解析为意图和词槽

- 将意图和词槽作为参数调用FlaskAPI，FlaskAPI会返回给调用者回复文本（1），同时，FlaskAPI会将意图和词槽写入Sqlite数据库（2）。

- 1）回复文本调用语音合成接口来将回复信息反馈给用户。

  2.1）树莓派MQTT客户端（发布者）会定期扫描数据库，当有新的数据写入时会将新插入的意图和词槽发布出去。

  2.2) Django服务器也会定期扫描数据库，当有新的控制意图写入时会将相应的设备状态更新到UI显示界面上。

- 控制中枢根据意图和词槽执行相应控制命令。

**Web端控制流程：**

- 在Web端通过点击等操作执行某命令

- 将该控制命令对应的的意图和词槽写入Sqlite数据库

  1）树莓派MQTT客户端（发布者）会定期扫描数据库，当有新的数据写入时会将新插入的意图和词槽发布出去。

  2）Django服务器也会定期扫描数据库，当有新的控制意图写入时会将相应 的设备状态更新到UI显示界面上。

- 控制中枢根据意图和词槽执行相应控制命令。

### 4.2 语音部分

​	会话式交互智能家居系统主要是通过语音命令+web来实现调度和控制，其中语音指令是系统的输入源，也是系统最主要、最常用的输入信号。系统采用语音信号作为家居调度和控制策略的输入源，通过解析语音信号获知用户意图，进而实现控制，系统完成控制操作后，返回控制结果，并通过智能语音形式反馈给用户。从系统运行流程来看，语音部分整体包括语音识别和语音合成两个主要环节，每条语音指令通过输入、输出两个部分与用户进行会话式交互。

​	![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-3.png)

#### 4.2.1 输入方向(语音识别)

​	当用户想对家居系统进行调度时，通过麦克风阵列录入语音指令，语音输入部分主要完成语音识别和语义理解两个任务。首先，通过解析输入的语音信号，获得用户指令的文本信息。这一部分主要采用百度语音识别接口，可以识别60s以内的长语音信息，准确率超过90%，在室内安静空间内准确率超过95%。输入过程的流程图表示如下：

#### 4.2.1.1 语音识别

​	在这个模块中，语音识别调用了百度语音识别平台的接口，百度语音识别为开发者提供业界优质且免费的语音服务，通过场景识别优化，为车载导航，智能家居和社交聊天等行业提供语音解决方案，语音识别支持中文，英文识别，方言支持粤语，四川话，准确率达到90%以上，可以让沟通交流更加准确快捷。

 	百度语音识别通过 REST API 的方式给开发者提供一个通用的 HTTP 接口，上传需要完整的录音文件，录音文件时长不超过60s，格式支持：pcm（不压缩）、wav（不压缩，pcm编码）、amr（压缩格式），为了更高的识别率和更快的识别速度，官方推荐pcm 采样率 ：16000 固定值，且编码：16bit 位深的单声道。百度服务端会将非pcm格式，转为pcm格式，因此使用wav、amr会有额外的转换耗时。

#### 4.2.1.2 语义理解(unit)

- **训练unit技能**

  ​	百度理解与交互技术平台（UNIT），开放了百度多年积累的自然语言理解与交互技术、深度学习、大数据等核心能力，帮助开发者大幅降低对话系统研发门槛，可以精确适配业务需求，训练对话系统。

  ​	训练unit技能首先需要的考虑的是：怎么构建交互更加自然的技能。初步打算控制的智能家居有灯和空调。对于灯的交互设计比较简单，只用包含灯的开关。对于空调的交互设计则比较复杂，主要包含：开关、温度调节、模式调节、风速控制、定时设置等。由于unit2.0推出了对话模板的新功能，应尽量避免少使用unit1.0的对话样本集功能。否则，容易出现“把空调打开”在样本集中，unit能够识别意图，“空调打开”不在样本集中，unit不能识别意图。一字之差，效果却完全不同，使交互体验非常的差。而最新的对话模板功能，则能够完全避免类似的问题。

- **调用unit技能**

  ​	调用unit的技能，最后确定使用python2.7实现。根据百度官方文档介绍，只要能够对百度unit发出http请求的任何平台任何操作系统都能调用unit。目前在windows和Linux操作系统下测试发现都能够实现。根据百度官方给出的参考文档和源码,在进行了细微调整后，已经能够成功调用unit技能，源码上传到了github仓库。代码中关键的几个点：首先是需要从unit平台的技能发布页面中，获取到APIKey和Secret Key。将源码中的Key改为我们自己训练技能的Key,然后获取到技能对应的Token（源码中需要手动获取，通过修改源码，已经能够实现自动根据Key获取Token）。结合语音识别部分，就能实现完整的语音模块所有功能：用户发出命令，将语音识别为文本，利用文本调用unit识别出意图，最后返回json格式的意图，解析json为python 列表形式传给其他模块。

#### 4.2.2 输出方向(语音合成)

​	经过预训练的unit会话技能可以根据输入的指令识别用户意图，同时返回相对应的回复语。等控制命令完成后，通过解析unit完成返回的json值，调用百度语音合成接口，将结果反馈以语音形式反馈到用户，通过与用户会话式的语音交互，实现更自然的沟通。

​	输出方向的流程图如下：

​	![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-4.png)

#### 4.2.3 语音识别延迟优化

1.语音采样使用16k采样率，录音文件以pcm格式本地保存，录音程序默认的wav格式可以使用工具本地转为pcm格式上传

2.为保证上传速率和识别率，语音命令长度保留在10s内，最长不超过20s。

3.采用专用的麦克风阵列录制语音，使用前应保持网络通畅。

### 4.3 FlaskAPI的主要功能

#### 4.3.1 FlaskAPI设计方案

​	Flask是一个使用 Python 编写的轻量级 Web 应用框架。我们在这里使用Flask框架编写一个API应用，用来处理unit识别出的意图和词槽。

​	这个Flask API的主要功能如下图：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-5.png)

#### 4.3.2 FlaskAPI功能介绍

​	FlaskAPI的主要功能是将语音部分接入系统，使得用户可以通过语音来控制家用设备，使用方法是将意图和词槽作为参数，调用FlaskAPI接口，该接口会将传来的意图和词槽写入Sqlite数据库，同时会针对意图和词槽返回相应的的回复文本。功能总结如下：

功能1：针对意图和词槽返回相应的的回复文本

功能2：将传来的意图和词槽写入Sqlite数据库

#### 4.3.3 FlaskAPI规范

接口名：callback

请求方式：GET/POST

| 参数 | 变量名 | 类型   | 说明                             |
| ---- | ------ | ------ | -------------------------------- |
| 意图 | intent | string | 此处的意图是调用unit时返回的意图 |
| 词槽 | slots  | string | 此处的词槽是调用unit时返回的词槽 |

返回结果说明：

| 参数     | 变量名 | 类型   | 说明                                                         |
| -------- | ------ | ------ | ------------------------------------------------------------ |
| 结果     | status | string | 调用接口执行状态:   successful   表示调用成功；   failed   表示调用失败 |
| 回复消息 | res    | string | 若status= successful，返回正常回复消息；   若status=failed，返回failed！ |

### 4.4设备控制

#### 4.4.1设备控制方案设计

​	设备控制整体方案为：arduino采集传感器数据并利用mqtt发布，树莓派订阅传感器数据，并做出控制决策，然后利用串口控制红外模块发射相关的控制指令控制相应的设备。

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-6.png)

#### 4.4.2 MQTT通信

​	MQTT（Message Queuing Telemetry Transport，消息队列遥测传输协议），是一种基于发布/订阅（publish/subscribe）模式的“轻量级”通讯协议，该协议构建于TCP/IP协议上，由IBM在1999年发布。在本设计中，利用mqtt采集传感器数据以及发布设备控制命令。

#### 4.4.2.1 树莓派MQTT客户端订阅系统
​	Raspberry Pi MQTT客户端订阅系统主要是运行在树莓派上的MQTT客户端，主要任务为订阅MQTT broker的主题，以及将订阅到的数据存储到Sqlite3数据库里，程序设计流程如下图所示：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-7.png)

树莓派订阅实际测试效果：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-8.png)

#### 4.4.2.2 树莓派MQTT客户端发送系统

​	Raspberry Pi MQTT客户端发送系统主要是运行在树莓派上的MQTT客户端，主要任务为读取Sqlite3数据库里新的控制命令，并将控制命令发布到MQTT broker的主题上，程序设计流程如下图所示：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-9.png)

树莓派发布客户端实际测试：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-10.png)

#### 4.4.2.3 Arduino MQTT客户端发送系统

​	节点Arduino MQTT客户端发送系统主要是运行在Arduino上的MQTT客户端，主要任务为传感器侦测到环境参数之后，将数据发布到MQTT broker的主题上，程序设计流程如下图所示：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-11.png)

传感节点数据发布测试：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-12.png)

#### 4.4.3 设备控制

#### 4.4.3.1设备控制方案及设备选型

​	由于arduino内部资源限制，运行mqtt订阅客户端的时候，需要消耗大量的资源，程序运行极不稳定，导致接受数据异常，因此拟采用如下方案：采用arduino采集传感器数据，通过mqtt发布温湿度等环境数据，树莓派订阅环境数据，并作为空调、投影仪等设备的控制器。

#### 4.4.3.2 树莓派串口控制外设

​	空调、投影仪等设备通过红外信号进行控制，在此选择树莓派通过串口控制红外发射管进行相应控制码的收发。红外发射模块如下所示，将该模块与树莓派串口rxd/txd交叉连接，即可进行红外信号的收发操作。经测试，红外发射模块，发射距离6-8米左右最佳，由于空调一般位于较高位置，因而红外模块45度左右对准空调等设备最佳。

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-13.png)

树莓派只有一个实际可以利用的串口ttyAMA0，默认作为串口终端调试使用，因而串口不能正常使用，如要正常使用串口则需要修改树莓派设置，下面将修改有关设置，重新启用串口。

修改配置文件：

 sudo nano /boot/cmdline.txt

删掉配置文件里面的 console=serial1,115200 和 kgdboc=serial1,115200 ，执行如下命令进入树莓派配置

Sudo nano /boot/config.txt

找到如下配置语句使能串口，如果没有，可添加在文件末尾添加下面的命令：enable_uart=1。

关闭串口终端调试功能：

执行如下命令进入设置界面：

sudo raspi-config

选择 Interfacing Options  ->Serial ->no -> yes 关闭串口调试功能, 打开串口。

通过树莓派串口控制空调:

执行如下命令安装python的串口库：sudo apt-get install python-serial。

将红外指令以十六进制的形式发出，红外发射模块将会将该编码转化为空调可以接受的形式。

空调控制测试效果：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-14.png)

### 4.5 WEB UI方案设计

​	首先在树莓派上搭建Django Web开发框架，建立后台数据库db.sqlite。语音模块将发送控制命令到数据库的myhone_commands表中，并可从数据库的myhome_nodedata表中获取节点数据和状态；网关节点将节点数据传输到数据库的myhome_nodedata表中，并从myhone_commands表中获取控制指令去控制相应的节点；Web前端界面将实时获取后台数据库的数据，并呈现相应的状态和数据，还利用Highchart技术将历史数据做相应处理可视化到界面。以下是系统设计方案框图：

![](https://github.com/doubleZYan/RaspberryProject/blob/master/pictures/4-15.png)

