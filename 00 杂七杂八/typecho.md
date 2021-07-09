# Typecho 

### 一、安装

```sh
# yum install php php-devel php-fpm php-gd php-mbstring php-mysql
# systemctl start php-fpm
# systemctl enable php-fpm
# netstat -tln|grep 9000              #php-fpm默认使用9000端口，查看端口使用情况：
# yum install net-tools             #如果没有netstat命令则需要安装net-tools工具包
```



## 二、插件使用

### TsStroe

读取Github上维护的专用表格实现插件仓库各项功能