windows如何查看端口占用情况，并杀死进程释放端口

```sh
netstat -ano | findstr "9001"

taskkill /f /t /im "27104"
```

![image-20210726200718801](https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/20210726200719.png)