**1. 将 SpringBoot 项目打成 Jar 包**
**2. 编写 DockerFiler 文件**

```java
###指定java8环境镜像
FROM java:8
###复制文件到容器
COPY  *.jar /blog-system.jar
CMD ["--SERVE.PORT=8090"]
###声明启动端口号
EXPOSE 8090
###配置容器启动后执行的命令
ENTRYPOINT ["java","-jar","blog-system.jar"]
```
需要将 SpringBoot 项目Jar 包 和 DockerFiler 文件 上传到 linux ，为了方便，将其放在统一目录下，如果不在同一目录下，DockerFile文件中的路径也要做相应更改
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200529211343154.png)
**3. 构建镜像**

```java
docker build -t 镜像名 .
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052921151097.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
**4. 发布运行**

```java
# -d 后台运行
# p 端口映射
# --name 容器名字
dcoker run -d -p 主机端口：容器端口 --name 容器名 镜像名
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200529211625444.png)
**服务器开启 8090 安全组，在浏览器输入url 能访问到数据，打包 Docker 镜像成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052921174352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)