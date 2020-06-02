@[TOC]
### Java语言概述
- 是SUN(Stanford University Network，斯坦福大学网络公司 ) 1995年推出的一 门高级编程语言。
- 是一种面向Internet的编程语言。Java一开始富有吸引力是因为Java程序可以 在Web浏览器中运行。这些Java程序被称为Java小程序（applet）。applet使 用现代的图形用户界面与Web用户进行交互。 applet内嵌在HTML代码中。
- 随着Java技术在web方面的不断成熟，已经成为Web应用程序的首选开发语言。

### Java技术体系平台
1. Java SE(Java Standard Edition)标准版
	支持面向桌面级应用（如Windows下的应用程序）的Java平台，提供了完整的Java核 心API，此版本以前称为J2SE
2. Java EE(Java Enterprise Edition)企业版 
	是为开发企业环境下的应用程序提供的一套解决方案。该技术体系中包含的技术如 :Servlet 、Jsp等，主要针对于Web应用程序开发。版本以前称为J2EE 。
3. Java ME(Java Micro Edition)小型版 
	支持Java程序运行在移动终端（手机、PDA）上的平台，对Java API有所精简，并加 入了针对移动终端的支持，此版本以前称为J2ME。
4. Java Card 
	支持一些Java小程序（Applets）运行在小内存设备（如智能卡）上的平台

### Java主要特性
1. **Java语言是易学的**。Java语言的语法与C语言和C++语言很接近，使得大多数程序员 很容易学习和使用Java。 
2. **Java语言是强制面向对象的**。Java语言提供类、接口和继承等原语，为了简单起见， 只支持类之间的单继承，但支持接口之间的多继承，并支持类与接口之间的实现机制 （关键字为implements）、
3.  **Java语言是分布式的**。Java语言支持Internet应用的开发，在基本的Java应用编 程接口中有一个网络应用编程接口（java net），它提供了用于网络应用编程的类 库，包括URL、URLConnection、Socket、ServerSocket等。Java的RMI（远程 方法激活）机制也是开发分布式应用的重要手段。 
4. **Java语言是健壮的**。Java的强类型机制、异常处理、垃圾的自动收集等是Java程序 健壮性的重要保证。对指针的丢弃是Java的明智选择。
5. **Java语言是安全的**。Java通常被用在网络环境中，为此，Java提供了一个安全机 制以防恶意代码的攻击。如：安全防范机制（类ClassLoader），如分配不同的 名字空间以防替代本地的同名类、字节代码检查。 
6. **Java语言是体系结构中立的**。Java程序（后缀为java的文件）在Java平台上被 编译为体系结构中立的字节码格式（后缀为class的文件），然后可以在实现这个 Java平台的任何系统中运行。
7. **Java语言是解释型的**。如前所述，Java程序在Java平台上被编译为字节码格式， 然后可以在实现这个Java平台的任何系统的解释器中运行。
8. **Java是性能略高的**。与那些解释型的高级脚本语言相比，Java的性能还是较优的。 • Java语言是原生支持多线程的。在Java语言中，线程是一种特殊的对象，它必须 由Thread类或其子（孙）类来创建。

###  Java运行机制及运行过程
#### Java语言的特点
**特点一：面向对象** 
- 两个基本概念：类、对象 
- 三大特性：封装、继承、多态

**特点二：健壮性** 
- 吸收了C/C++语言的优点，但去掉了其影响程序健壮性的部分（如指针、内存的申请与 释放等），提供了一个相对安全的内存管理和访问机制 

**特点三：跨平台性** 
- 跨平台性：通过Java语言编写的应用程序在不同的系统平台上都可以运行。“Write once , Run Anywhere” 
- 原理：只要在需要运行 java 应用程序的操作系统上，先安装一个Java虚拟机 (JVM Java Virtual Machine) 即可。由JVM来负责Java程序在该系统中的运行。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525210943206.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
因为有了JVM，同一个Java 程序在三个不同的操作系统中都可以执行。这 样就实现了Java 程序的跨平台性。

#### Java两种核心机制
**1. 核心机制—— Java虚拟机 (Java Virtal Machine)**
- **JVM是一个虚拟的计算机，具有指令集并使用不同的存储区域。负责执行指 令，管理数据、内存、寄存器。**
- 对于不同的平台，有不同的虚拟机。
- 只有某平台提供了对应的java虚拟机，java程序才可在此平台运行 
- Java虚拟机机制屏蔽了底层运行平台的差别，实现了“一次编译，到处运行”
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525211429494.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525211514791.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)

**2. 核心机制——垃圾回收**
- 不再使用的内存空间应回收—— 垃圾回收。 
	- 在C/C++等语言中，由程序员负责回收无用内存。 
	- Java 语言消除了程序员回收无用内存空间的责任：它提供一种系统级线程跟踪存储空 间的分配情况。并在JVM空闲时，检查并释放那些可被释放的存储空间。 
- 垃圾回收在Java程序运行过程中自动进行，程序员无法精确控制和干预

### Java语言的环境搭建
####  什么是 JDK、JRE?
- JDK(Java Development Kit    Java开发工具包) 

	- JDK是提供给Java开发人员使用的，其中包含了java的开发工具，也包括了 JRE。所以安装了JDK，就不用在单独安装JRE了。	
	- 其中的开发工具：编译工具(javac.exe)  打包工具(jar.exe)等

- JRE(Java Runtime Environment    Java运行环境)
	-  包括Java虚拟机(JVM Java Virtual Machine)和Java程序所需的核心类库等， 如果想要运行一个开发好的Java程序，计算机中只需要安装JRE即可。

**简单而言，使用JDK的开发工具完成的java程序，交给JRE去运行。**

####  JDK、JRE、JVM关系
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525212653897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
- JDK = JRE + 开发工具集（例如Javac编译工具等） 
- JRE = JVM + Java SE标准类库

####  下载并安装JDK
**官方网址**：
- www.oracle.com
- java.sun.com

**安装JDK**
- 傻瓜式安装，下一步即可。
- 建议：安装路径不要有中文或者空格等特殊符号。
- 如果操作系统是64位的，软件尽量选择支持64位的（除非软件本身不区分）。
- 当提示安装 JRE 时，正常在JDK安装时已经装过了，但是为了后续使用Eclipse等开发 工具不报错，建议也根据提示安装JRE

####  配置环境变量 path
每次执行 java 的工具都要进入到bin目录下，是非常麻烦的。可不可以在任何目 录下都可以执行java的工具呢？
- 根据windows系统在查找可执行程序的原理，可以将java工具所在路径定义到 path 环境变量中，让系统帮我们去找运行执行的程序。

**配置方法：** 
- 我的电脑--属性--高级系统设置--环境变量 编辑 path 环境变量，在变量值开始处加上java工具所在目录，后面用 “ ; ”和其他值分隔 开即可。 
- 打开DOS命令行，任意目录下敲入javac。如果出现javac 的参数信息，配置成功。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525213132174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200525213150708.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)