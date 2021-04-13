# DEBUG

## 前言

大家在开发的过程肯定会遇到bug，那大家是怎么找出bug的呢，System.out.println()？呃，这种也是一种方式，但是如果我们想更便捷的开发，不得不了解Debug调试工具了。当你对各种可能出现的bug的数据进行输出时，会debug的童鞋已经拿到了运行期间所有的数据，并且能对数据进行操作，可以说不会Debug的程序猿不是一个合格的程序猿。

古人云：工欲善其事必先利其器，那下面就跟着小编来学习Debug吧。

## 1. 由来

> DEBUG是计算机排除故障的意思。马克2号（Harvard Mark II）编制程序的格蕾丝·霍珀（Grace Hopper）是一位美国海军准将及计算机科学家，同时也是世界最早的一批程序设计师之一。有一天，她在调试设备时出现故障，拆开继电器后，发现有只飞蛾被夹扁在触点中间，从而“卡”住了机器的运行。于是，霍珀诙谐地把程序故障统称为“臭虫（BUG）”，把排除程序故障叫DEBUG，那这个词也就这么来了.
>
> 那到了今天Debug究竟是什么意思呢？其实从本质上来说， **Debug是一种程序，一种调试工具，说白了就是供程序员检查修改问题的工具。**

## 2. 启动 debug 模式

### 1. 启动debug模式，方便随时调试代码

###### ![image-20210119145007410](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119145009.png)

### 2. 设置debug窗口

勾选 Show debug wndow on beakpint 之后，运行到第一个断点会直接激活debug窗口

![image-20210119145624095](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119145624.png)

## 3. 设置断点

在想要设置断点的所在行的左边行号栏点击左键

###### ![image-20210119145925723](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119145925.png)

或者在想要设置断点的所在行使用快捷键 Ctrl + F8 设置/取消断点

## 4. 调试按钮&服务按钮

### 1. 调试按钮

###### ![image-20210119150339452](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119150339.png)

①**Show Execution Point (Alt + F10)：**如果你的光标在其它行或其它页面，点击这个按钮可跳转到当前代码执行的行；

② **Step Over (F8)：**步过，一行一行地往下走，执行行存在方法也不跳入；

③  **Step Into (F7)：**步入，如果当前行有方法，可以进入方法内部，一般用于进入自定义方法内，不会进入官方类库的方法。

④ **Force Step Into (Alt + Shift + F7)：**强制步入，能进入任何方法，查看底层源码的时候可以用这个进入官方类库的方法。

⑤ **Step Out (Shift + F8)：**步出，从步入的方法内退出到方法调用处，此时方法已执行完毕，只是还没有完成赋值。

⑥ **Drop Frame（无快捷键）：**回退断点，有时候当我们步入方法体之后，还想回退到方法体外，点这个按钮后，断点重新回到方法体之外。在继续还是可以再次进到方法内

⑦ **Run to Cursor (Alt + F9)：**运行到光标处，你可以将光标定位到你需要查看的那一行，然后使用这个功能，代码会运行至光标行，而不需要打断点。前提是在光标定位和执行行之间没有断点

⑧ **Evaluate Expression (Alt + F8)：**计算表达式，可以计算想要查看代码的值。

 这个表达式不仅可以是一般变量或参数，也可以是方法，当你的一行代码中调用了几个方法时，就可以通过这种方式查看查看某个方法的返回值。

![image-20210119162839769](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119162839.png) 

### 2. 服务按钮

###### ![image-20210119155509131](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119155509.png)

① **Rerun ‘xxx’(Ctrl + F5)：** 重新运行程序，会关闭服务后重新启动程序

②

③ **Roggle auto-test：**切换自动化测试

④ **Resume Program (F9)：**遇到断点停下，没有断点则运行完整个流程；

⑤ **Pause Program**：暂停程序，启用Debug

⑥ **Stop 'xxx' (Ctrl + F2)：**连续按两下，关闭程序。

⑦ **View Breakpoints (Ctrl + Shift + F8)：**查看所有断点

⑧ **Mute Breakpoints：**哑的断点，选择这个后，所有断点变为灰色，失效。如果只想使某一个断点失效，可以在断点上右键取消Enabled，如图下图，则该行断点失效，断点失效后变为空心圆圈。

![image-20210119164435202](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119164435.png) 

## 5. 变量查询

### 1. 在需要查看的变量上，鼠标悬停 2s 左右

###### ![image-20210119161014112](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119161014.png)

### 2. Alter + 鼠标左键

点啥看啥，非常滴方便

###### ![image-20210119161301091](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119161301.png)

### 3. 在 Variables 中查看

Variables中显示已运行程序的所有变量

![image-20210119161538713](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119161538.png) 

### 4. 在 Watches 中查看

可以点击 加号，输入要查看的变量，也可以直接同 Variables 中拖入 Watches 中查看

点击 Variables 操作栏中的![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119162231.png)图标，调出Watches栏

![image-20210119162402664](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119162402.png) 

![image-20210119161950672](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119161950.png) 

### 5. 利用计算表达式查看

![image-20210119162632290](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119162632.png) 

## 6. 变量修改

在调试代码的时候，实时的修改运行状态的代码变量的值

在 variables 中选中需要修改的字段，右键 Set Value (F2)

![image-20210119163506167](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119163506.png) 

## 6. 断点条件设置

通过设置断点条件，在满足条件时，才停在断点处，否则直接运行

**场景举例：**如果我们遍历一个比较大的集合或者数组是，在循环体内部设置了一个断点，如果没有断点条件设置，我们就需要一个一个的查看变量的值，这样肯定不是我们想看到的。

这时候就可以用到断点条件设置，在断点上右键 在 **Conditions**下方的框中设置条件，设置之后，只有满足条件的情况下断点才会有效，否则直接跳过。

![image-20210119194645501](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210119194645.png) 



## 参考博客：

[在Intellij IDEA中使用Debug](https://blog.csdn.net/weixin_30784501/article/details/97872101?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control)

[工欲善其事，必先利其器！idea最详细的Debug技巧及方法，让你定位bug如探囊取物！](https://blog.csdn.net/qq_42121836/article/details/112307283)

[Intellij IDEA 2017 debug断点调试技巧与总结详解篇](https://blog.csdn.net/qq_27093465/article/details/64124330)

