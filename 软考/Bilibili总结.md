@[TOC](目录)
# 软件设计师总结

## 历年考试情况
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406203800219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
## 一、计算机组成与体系结构
### 1. 数据的表示
1. R进制转十进制
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406204418872.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
2. 十进制转R进制
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406204549424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
3. 二进制转八进制和十六进制
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406204854910.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
4. 编码方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406211150397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
5. 数值表示范围
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406211300332.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
6.浮点数运算
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406211835655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
### 2. 计算机结构
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210406212312347.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)

#### 1. 计算机体系结构分类-Flynn

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021040621270579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)

#### 2. CICC与RISC

![image-20210406212825873](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210406212826.png)

### 3. 流水线

#### 1. 概念

![image-20210406213847997](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210406213848.png)

#### 2. 流水线计算

![image-20210406214345178](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210406214345.png)

#### 3. 吞吐率计算

![image-20210406214601025](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210406214601.png)

#### 4. 流水线加速比

![image-20210406214646646](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210406214646.png)

#### 5. 流水线的效率

 ![image-20210406215204372](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210406215204.png)

### 4. 层次化存储结构

![image-20210413084630222](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413090250.png)

#### 1. Cache

1. 概念

 ![image-20210413085010534](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413085010.png)

#### 2. 局部性原理

![image-20210413090149757](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413090150.png)

### 5. 主存

#### 1. 分类

![image-20210413090451167](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413090451.png)

#### 2. 编址

![image-20210413091238993](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413091239.png)

### 6. 磁盘结构与参数

![image-20210413091527439](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413091527.png)

![image-20210413092841756](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413092842.png)

![image-20210413092759758](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413092759.png)

### 7. 总线

![image-20210413093310085](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210413093310.png)