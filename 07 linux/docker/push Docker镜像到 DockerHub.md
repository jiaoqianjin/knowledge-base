### Docker 发布项目的优点
项目 + 环境打包成镜像，发布到Docker Hub ,	使用时只需要下载我们发布的镜像，然后一键运行。
使应用更快速的交付和部署，优化了传统的交付部署需要一堆帮助文档,安装程序的操作。
### 操作流程
**1. 首先要有一个 Docker Hub 账号**
	没有账号，先到 [Docker Hub 官网](https://hub.docker.com) 进行注册

**2. 在 服务器 登录**

```java
# username dockerhub 账户名 
docker login -u username
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020053010201667.png)
**3. 规范 tag 镜像名称**
镜像使用的是 SpringBoot项目打包成的镜像 [SpringBoot项目打包Dcoker镜像 居然可以这么简单](https://blog.csdn.net/weixin_44491927/article/details/106432780)
```java
docker tag 镜像id jiaoqianjin/blog:1.0
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200530102505424.png)
**4. 推送镜像到Docker Hub**

```java
# docker push 注册用户名/镜像名:版本号
docker push jiaoqianjin/blog:1.0
# docker psuh上去即可!自己发布的镜像尽量带上版本号
```
注：虚拟机上传应该会超级超级慢，利用服务器上传速度还可以

**5. 在Dokcer Hub 查看**
直接搜索，可以查到上传上的镜像
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200530103120844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
在自己的项目里也可以找到
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200530103309574.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
因为我们已经发布自己的镜像，以后都可以直接使用docker pull命令拉取使用镜像：

```java
docker pull jiaoqianjin/blog:1.0
```
拉下来之后便可以直接运行

```java
dcoker run -d -p 主机端口：容器端口 --name 容器名 镜像名
```

```nginx
server {
    listen       80;
    server_name  jiaoqianjin.cn;
    rewrite ^(.*) https://jiaoqianjin.cn$1 permanent;
}
server {
     listen 443 ssl; 
     #填写绑定证书的域名
     server_name jiaoqianjin.cn; 
     #证书文件名称
     ssl_certificate /etc/nginx/key/1_jiaoqianjin.cn_bundle.crt; 
     #私钥文件名称
     ssl_certificate_key  /etc/nginx/key/2_jiaoqianjin.cn.key; 
     ssl_session_timeout 5m;
     #请按照以下协议配置
     ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
     #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
     ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; 
     ssl_prefer_server_ciphers on;
    
     root   /usr/share/nginx/html;
     #charset koi8-r;
     #access_log  /var/log/nginx/log/host.access.log  main;
     #
     location / {
           index index.php index.html index.htm;
     }
     #error_page  404              /404.html;
     #redirect server error pages to the static page /50x.html
     #
     error_page   500 502 503 504 /50x.html;
     location = /50x.html {
       root   /usr/share/nginx/html;
     }
     #pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
     #
     location ~ .php$ {
       fastcgi_pass   127.0.0.1:9000;
       fastcgi_index  index.php;
       fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
       include        fastcgi_params;
     }
}
```

```nginx
 upstream php { 
    server 127.0.0.1:9000; 
} 
server { 
    server_name www.cxy.cc; 
    root html/build; 
    access_log logs/wcxy.access.log main; 
    error_log logs/wcxy.error.log; 
    index index.php list.php; 
    location = /favicon.ico { 
        log_not_found off; 
        access_log off; 
    } 
    location = /robots.txt { 
        allow all; 
        log_not_found off; 
        access_log off; 
    } 
    if ( !-e $request_filename ) { 
        rewrite ^(.*)$ /index.php$1 last; 
    } 
    location ~ .*.php(/.*)*$ { 
        fastcgi_index index.php; 
        include fastcgi.conf; 
        fastcgi_split_path_info ^((?U).+.php)(/?.+)$; 
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; 
        fastcgi_param PATH_INFO $fastcgi_path_info; 
        fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info; 
        fastcgi_intercept_errors on; 
        fastcgi_pass php; 
    } 
    location /status {
        #stub_status on; access_log off; 
    } 
    location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$ {
        expires 30d; 
    } 
    location ~ .*.(js|css)?$ { 
        expires 12h; 
    } 
} 
```

![logo](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210614224616.jpg)