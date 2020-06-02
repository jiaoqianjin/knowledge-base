@[toc]
### 关键字和保留字的使用
**关键字(keyword)的定义和特点** 
- 定义：被Java语言赋予了特殊含义，用做专门用途的字符串（单词） 
- 特点：关键字中所有字母都为小写 
- 官方地址： [关键字](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html) 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526110448386.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526110503149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)

**Java保留字**：
- 现有Java版本尚未使用，但以后版本可能会作为关键字使
用。自己命名标识符时要避免使用这些保留字 goto 、const

### 标识符
**标识符：**
- Java 对各种变量、方法和类等要素命名时使用的字符序列称为标识符
==技巧：凡是自己可以起名字的地方都叫标识符==

**定义合法标识符规则（如果不遵守以下规则，编译不通过）：**
- 由26个英文字母大小写，0-9 ，_或 $ 组成
- 数字不可以开头。
- 不可以使用关键字和保留字，但能包含关键字和保留字。
- Java中严格区分大小写，长度无限制。
- 标识符不能包含空格

 **标识符的命名规范（如果不遵守以下规范，编译可以通过）：**
- 包名：多单词组成时所有字母都小写：xxxyyyzzz
- 类名、接口名：多单词组成时，所有单词的首字母大写：XxxYyyZzz
- 变量名、方法名：多单词组成时，第一个单词首字母小写，第二个单词开始每个 单词首字母大写：xxxYyyZzz
- 常量名：所有字母都大写。多单词时每个单词用下划线连接：XXX_YYY_ZZZ

注意1：在起名字时，为了提高阅读性，要尽量有意义，“见名知意”。
注意2：java采用unicode字符集，因此标识符也可以使用汉字声明，但是不建议使用。

### 变量
**变量的概念：** 
- 内存中的一个存储区域 
- 该区域的数据可以在同一类型范围内不断变化 
- 变量是程序中最基本的存储单元。包含变量类型、变量名和存储的值 

**变量的作用：** 
- 用于在内存中保存数据 

**使用变量注意：** 
- Java中每个变量必须先声明，后使用 
- 使用变量名来访问这块区域的数据 
- 变量的作用域：其定义所在的一对{ }内 
- 变量只有在其作用域内才有效 
- 同一个作用域内，不能定义重名的变量

**声明变量**
-  语法：<数据类型> <变量名称>
-  例如：int var; 

**变量的赋值**
- 语法：<变量名称> =  <值>
-  例如：var = 10; 

**声明和赋值变量**
-  语法： <数据类型> <变量名> =  <初始化值>
-  例如：int var = 10;

**变量的分类**
**1. 按数据类型**
对于每一种数据都定义了明确的具体数据类型（强类型语言），在内存中分 配了不同大小的内存空间。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526112255826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
**2. 按声明的位置的不同**
 - 在方法体外，类体内声明的变量称为成员变量。 
 -  在方法体内部声明的变量称为局部变量。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526112534100.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
- 注意：二者在初始化值方面的异同: 
	- 同：都有生命周期 
	- 异：局部变量除形参外，需显式初始化。

**整数类型：byte、short、int、long**
- Java各整数类型有固定的表数范围和字段长度，不受具体OS的影响，以保 证java程序的可移植性。 
- java的整型常量默认为 int 型，声明long型常量须后加‘l’或‘L’ 
- java程序中变量通常声明为int型，除非不足以表示较大的数，才使用long
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052611275129.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
**浮点类型：float、double**
- 与整数类型类似，Java 浮点类型也有固定的表数范围和字段长度，不受具体操作 系统的影响。
- 浮点型常量有两种表示形式： 
	- 十进制数形式：如：5.12       512.0f        .512   (必须有小数点） 
	- 科学计数法形式:如：5.12e2      512E2     100E-2 
-  float:单精度，尾数可以精确到7位有效数字。很多情况下，精度很难满足需求。 double:双精度，精度是float的两倍。通常采用此类型。 
-  **Java 的浮点型常量默认为double型，声明float型常量，须后加‘f’或‘F**’
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526113133450.png)

**字符类型：char**
- char 型数据用来表示通常意义上“字符”(2字节) 
- Java中的所有字符都使用Unicode编码，故一个字符可以存储一个字 母，一个汉字，或其他书面语的一个字符。 
- 字符型变量的三种表现形式： 
	- 字符常量是用单引号(‘ ’)括起来的单个字符。例如：char c1 = 'a';   char c2 = '中'; char c3 =  '9'; 
	-  Java中还允许使用转义字符‘\’来将其后的字符转变为特殊字符型常量。 例如：char c3 = ‘\n’;  // '\n'表示换行符 
	- 直接使用 Unicode 值来表示字符型常量：‘\uXXXX’。其中，XXXX代表 一个十六进制整数。如：\u000a 表示 \n。 
- char类型是可以进行运算的。因为它都对应有Unicode码。

**布尔类型：boolean** 
- boolean 类型用来判断逻辑条件，一般用于程序流程控制： if条件控制语句；
	- while循环控制语句；
	- do-while循环控制语句；
	- for循环控制语句； 
- **boolean类型数据只允许取值true和false，无null**。 
	- 不可以使用0或非 0 的整数替代false和true，这点和C语言不同。
	- Java虚拟机中没有任何供boolean值专用的字节码指令，Java语言表达所操作的 boolean值，在编译之后都使用java虚拟机中的int数据类型来代替：true用1表示，false 用0表示。———《java虚拟机规范 8版》

**字符串类型：String**
String不是基本数据类型，属于引用数据类型 
-  使用方式与基本数据类型一致。例如：String str = “abcd”; 
-  一个字符串可以串接另一个字符串，也可以直接串接其他类型的数据。例如： 

```java
str = str + “xyz” ; 
int n = 100; 
str = str + n;
```
**基本数据类型转换**
- 自动类型转换：容量小的类型自动转换为容量大的数据类型。数据类型按容 量大小排序为：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200527082528881.png)
- 有多种类型的数据混合运算时，系统首先自动将所有数据转换成容量最大的 那种数据类型，然后再进行计算。 
- **byte,short,char之间不会相互转换，他们三者在计算时首先转换为int类型**。 
- **boolean类型不能与其它数据类型运算**。 
- **当把任何基本数据类型的值和字符串(String)进行连接运算时(+)，基本数据类 型的值将自动转化为字符串(String)类型**。


**强制数据类型转换**
- 自动类型转换的逆过程，将容量大的数据类型转换为容量小的数据类型。使 用时要加上强制转换符：()，但可能造成精度降低或溢出,格外要注意。
- 通常，**字符串不能直接转换为基本类型，但通过基本类型对应的包装类则可
以实现把字符串转换成基本类型**。
	- 如： String a = “43”; int i = Integer.parseInt(a);
	- boolean类型不可以转换为其它的数据类型。