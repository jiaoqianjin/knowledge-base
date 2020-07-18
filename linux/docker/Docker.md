### Docker

Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

#### Docker 概述

#####  1. Docker为什么出现 ？

一款产品:开发-上线两套环境!应用环境,应用配置!

开发--- 运维。问题:我在我的电脑上可以运行!版本更新,导致服务不可用!对于运维来说,考验就十分大?环境配置是十分的麻烦,每一个机器都要部署环境(集群Redis、ES、 Ha...... ! 费时费力。

发布一个项目(jar+ ( Redis MySQL jdk ES ) ),项目能不能都带上环境安装打包!

之前在服务器配置-个应用的环境 Redis MySQL jdk ES Hadoop , 配置超麻烦了,不能够跨平台。

Windows ,最后发布到Linux !

传统:开发jar，运维来做! |

现在:开发打包部署上线, -套流程做完!

java-apk-发布(应用商店)---张三使用apk--安装即可用!

java --- jar (环境) --- 打包项目带上环境(镜像) ---- ( Docker仓库:商店) ---下载我们发布的镜像---直接运行即可!

Docker给以上的问题,提出了解决方案!

##### 2.  Docker的历史

2010年,几个搞IT的年轻人,就在美国成立了-家公司dotc loud做一些pass的云计算服务! LXC 有关的容器技术!

他们将自己的技术(容器化技术)命名就是Docker !

Docker刚刚诞生的时候,没有引起行业的注意! dotCloud ,就活不下去!

开源

开发源代码!

2013年, Docker开源!

Docker越来越多的人发现了docker的优点!灯, Docker每个月都会更新-个版本 !2014年4月9日, Docker1.0发布!

Docker为什么这么火?十分的轻巧!

在容器技术出来之前,我们都是使用虚拟机技术!

虚拟机:在window中装一个Vmware ,通过这个软件我们可以虚拟出来一台或者多台电脑!笨重!虚拟机也是属于虚拟化技术, Docker容器技术,也是一种虛拟化技术!

vm :linux centos原生镜像(一个电脑! )隔离， 需要开启多个虚拟机!几个G几分钟

docker:隔离，镜像 (最核心的环境”4m + jdk + mysq1) 十分的小巧，运行镜像就可以了! 小巧!几个M KB 秒级启动 !

到现在,所有开发人员都必须要会Docker 

##### 3. Docker能干嘛

> 之前的虚拟机技术   

​        ![1590832932354](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223438.png)

**虚拟机技术缺点：**

1. 资源占用十分多
2. 冗余步骤多
3. 启动很慢

> 容器化技术

容器化技术不是模拟的一个完成的操作系统

![1590833214416](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223439.png)

**比较Docker与虚拟机技术的不同**

​            \- 传统虚拟机，虚拟出一条硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件

​            \- 容器内的应用直接运行在宿主机的内容，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便了

​            \- 每个容器是相互隔离，每个容器内部有一个属于自己的文件系统，互不影响。

> DevOps开发、运维)

**应用更快速的交付和部署**

传统: -堆帮助文档,安装程序	

Docker :打包镜像发布测试, -键运行

**更便捷的升级和扩缩容**

使用了Docker之后,我们部署应用就和搭积木一样!

项目打包为一个镜像,扩展服务器A!服务器B

更简单的系统运维

在容器化之后,我们的开发,测试环境都是高度一致的。

更高效的计算资源利用:

Docker是内核级别的虚拟化,可以再一个物理机 上可以运行很多的容器实例!服务器的性能可以被压榨到极致。

#### Docker安装

##### Docker的基本组成

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223440.png)

1. 镜像

​            docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat镜像===》run===》tomcat01容器（提供服务器），通过这个镜像就可以创建多个容器（最终服务运行或者项目运行就是在容器中的）

​            

​            docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的。

​            启动、停止、删除、基本命令！

​            目前就可以把这个容器理解为就是一个简易的linux系统

​            

​        3. 仓库（repository)

​            仓库就是存放镜像的地方

​            仓库分为公有仓库和私有仓库

​            Docker Hub(默认是国外的)

​    

##### 安装Docker

1、需要会一-点点的Linux的基础

2、CentOS 7

3、我们使用Xshell 连接远程服务器进行操作!

环境查看

```shell
#系统内核是3.10以上的
[root@kuangshen /]# uname - r
3.10.0-1062.12.1.e17.x86_ _64
[root@kuangshen /]# cat /etc/os -releaseNAME="Centos L inux"
VERSION="7 (Core) "
ID= "centos "
ID_ LIKE="rhe1 fedora"
VERSION_ _ID="7"
PRETTY_ _NAME="CentoS Linux 7 (Core)"ANSI_ _COLOR="0; 31"
CPE_ NAME="cpe: /o: centos :centos:7"
HOME_ URL="https :/ /www. centos . org/"
BUG_ _REPORT_ _URL= "https ://bugs. centos. org/"
CENTOS_ _MANTISBT_ PROJECT="CentOS-7"CENTOS_ _MANTISBT_ PROJECT_ VERSION="7"REDHAT_ SUPPORT_ PRODUCT="centos"
REDHAT_ SUPPORT_ PRODUCT_ _VERSION= "7"
```

​    帮助文档

```shell
# 1、卸载旧的版本
yum remove docker \
                    docker-client \
                    docker-client-1atest \docker-common \
                    docker- latest \
                    docker-latest-1ogrotate \docker-logrotate \
                    docker-engine

# 2、需要的安装包
yum insta11 -y yum-utils

# 3、设置镜像的仓库
# 默认是从国外的!
yum-config-manager \
    --add-repo \
    https ://down1oad. docker . com/linux/centos/docker-ce.repo 

# 推荐使用阿里云的，十分的快
yum-confi g-manager \
    --add-repo \
    http://mirrors. aliyun. com/docker-ce/linux/centos/docker-ce.repo 

#更新yum软件包索引
yum makecache fast

# 4、安装dokcer docker-ce 社区版 ee企业版
yum insta11 docker-ce docker-ce-cli containerd. io

# 5、启动docker
systemct1 start docker 

# 6、使用docker version 是否安装成功!|
docker version
```

##### run 的运行流程图 

##### ![1590834468452](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223441.png)  

   ##### 底层原理

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223442.png)

​        Docker为什么比VM快？

​            \1. Docker有着比虚拟机更少的抽象

​            \2. Docker利用的是宿主机的内核，vm需要的是 Guest OS

​            

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223443.png)

​            所以说，新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载Guest OS，分钟级别的，而docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级。

​        

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223444.png)

#### Docker常用命令

​    

​    1. 镜像命令

​        

```shell

    docker images  查看镜像文件
   
    docker version  查看版本
    docker rmi imgageid 删除镜像
    docker rmi -f imgageid imgageid imgageid 删除多个镜像
    docker rmi -f $(docker images -aq) 删除全部的镜像
    
```

​        

​    2. 容器命令

​    

```shell
        docker run [可选参数]image                
        # 参数说明        
        --name="Name" 容器名字，用来区分容器        
        -d  后台方式运行        
        -it 使用交互方式运行，进入容器查看内容        
        -p  指定容器的端口 -p 8080:8080            
        -p ip:主机端口:容器端口            
        -p 主机端口:容器端口(常用)            
        -p 容器端口            容器端口        
        -p  随机指定端口                
        docker ps  查看正在运行的容器        
        docker ps –a  查看所有的容器        
        docker container exec -it f0b1c8ab3633 /bin/bash  进入到容器        
        exit 退出        
        docker run -d -p 81:80 nginx 启动nginx容器  
        docker rm 容器id   # 删除容器
        docker attach 容器id  # 进入正在运行的容器        
        docker start 容器id  # 启动容器        
        docker restart 容器id  # 重启容器        
        docker stop 容器id  # 停止正在运行的容器        
        docker kill 容器id  # 强制停止当前容器 
```

​        

3. 其他命令

​    

4. docker安装 elasticsearch  

```shell
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2 
```



####  容器数据卷

​    \1. 什么是容器数据卷

​    

​    \2. 使用数据卷

   

​    方式一：直接使用命令 -v    docker run -it -v 主机目录：容器目录    # 测试    docker run -it -v /home/ceshi:/home centos /bin/bash    # 启动起来的时候我们可以通过 docker inspect 容器id  查看     

   \# docker inspect 

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223445.png)

测试文件的同步

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223446.png)

再来测试 ！

\1. 停止容器

exit 

\2.  宿主机修改文件

vim test.java 

\3. 启动容器

docker start 容器 id docker attach 容器 id  # 进入正在运行的容器 

\4. 容器上的数据依旧是同步的

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223447.png)

**好处：我们以后修改只需要在本地修改即可，容器内会自动同步！**

#### 实战:安装MySQL

思考: MySQL的数据持久化的问题!

```shell
#获取镜像 
[root@kuangshen home]# docker pull mysql:5.7 
#运行容器，需要做数据挂载! 
#安装启动mysq1，需要配置密码的，这是要注意点! 
#官方测试: docker run --name some -mysql -e MYSQL_ ROOT_ PASSWORD=my-secret-pw -d mysq1:tag 
#启动我们的 -d后台运行 p端口映射 -V卷挂载 -e环境配置 --name容器名字 
[root@kuangshen home]# docker run -p 3306:3306 --name mysql -v /data/mysql/conf:/etc/mysql/conf.d -v /data/mysql/logs:/logs -v /data/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7 
```

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223448.png)

具名和匿名挂载

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223449.png)

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223450.png)

![img](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20200602223451.png)

#### SpringBoot微服务打包Dcoker镜像

\1. 将 SpringBoot 项目打成 Jar 包

\2. 编写 DockerFiler 文件

```shell
###指定java8环境镜像 
FROM java:8 
###复制文件到容器
app-springboot COPY  *.jar /blog-system.jar 
CMD ["--SERVE.PORT=8090"] 
###声明启动端口号 
EXPOSE 8090 
###配置容器启动后执行的命令 
ENTRYPOINT ["java","-jar","blog-system.jar"] 
```

3. 构建镜像

docker build -t 镜像名 . 

\4. 发布运行

dcoker run -d -p 主机端口：容器端口 --name 容器名 镜像名 

#### 发布自己的镜像

DockerHub

1、地址https://hub.docker.com/注册自己的账号!

2、确定这个这账号可以登录

3、在我们服务器上提交自己的镜像

```shell
[root@kuangshen tomcat]# docker login --help 
Usage: docker login [OPTIONS] [SERVER] 
Log in to a Docker registry. 
If no server is specified, the default is defined by the daemon. Options:    
-p，--password string    Password         
--password-stdin    Take the password from stdin    
-u，--username string Username     ---- 
[root@kuangshen tomcat]# docker login -u kuangshen 
Password: 
WARNING! Your password wi1l be stored unencrypted in /root/ . docker/config. json.Configure a credential helper to remove this warning. See https ://docs . docker . com/engine/reference/ commandline/login/
#credentials-store Login Succeeded     
```

4、登录完毕后就可以提交镜像了,就是一步docker push

```shell
# push镜像的问题? 
[root@kuangshen tomcat] # docker push kuangshen/diytomcat:1. 0 
The push refers to repository [docker. io/kuangshen/diytomcat2] An image does not exist 1ocally with the tag: kuangshen/diytomcat2 
#解决，増加一个tag 
[root@kuangshen tomcat]# docker tag f8559daf1fc2 kuangshen/ tomcat:1.0 
# docker psuh上去即可!自己发布的镜像尽量带上版本号 
[root@kuangshen tomcat]# docker push kuangshen/tomcat:1. 0 
The push refers to repos itory [docker. io/kuangshen/tomcat]fcc7fccb8e04: Preparing b5577f344233: Preparing bdcb94365850: Preparing 1c5bd81521f5: Preparing 0683de282177: Preparing 
```

