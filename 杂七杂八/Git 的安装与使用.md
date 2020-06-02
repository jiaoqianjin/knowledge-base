### 0. 安装Git

https://git-scm.com/ 进入git官网

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\ffb4f5e120904fc2be48777d7c5b8737\clipboard.png)

下载后点击“傻瓜式”安装即可

打开windows 左上角出现标志即安装成功

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\0029711928d44cf6b0377b6514052739\clipboard.png)

### 1. Git 基本配置

点击Git Bash

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\15e451fe9e8d4f7d9b545d9dc39960d4\clipboard.png)

1）配置的是你个人的用户名称和电子邮件地址，后期提交时可以显示自己的信息

```bash
$ git config --global user.name "xxx" 
$ git config --global user.email "xxx@xxx.com" 
```



2）查看环境变量的设定

```bash
$ git config user.name 
```



### 2. 注册码云

注册好码云之后，需要在本地生成sshkey

输入命令

```bash
ssh-keygen -t rsa -C "xxxxx@xxxxx.com"  
```

按照提示完成三次回车，即可生成，通过

```bash
cat ~/.ssh/id_rsa.pub //查看公钥 
```



也可在本地c:user/dell/.ssh文件中查看

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\f1aecbbf9d8b4f88b6c57d157270b5b4\clipboard.png)

复制公钥 添加到码云ssh公钥中

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\5139f1def9714524abb27483fd5a4d29\clipboard.png)

标题可自动生成，默认为自己的信息，也可自行修改

### 3. 添加到本地

1）使用bash 到需要入库的文件夹下，初始化新仓库

```bash
$ git init 
```



初始化后，在当前目录下会出现一个名为.git 的文件夹

2）将文件添加到Git 暂存区

```bash
$ git add "readme.txt"  
```



3）查看仓库当前状态

```bash
$ git status -s 
```



4）从Git的暂存区提交版本到仓库

```bash
$ git commit -m "1.0.0" 
```



### 4.将本地库与远程库关联

首先在码云新建一个仓库

1）获得ssh地址

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\fd360e30ec2848619b4f7ec9a2696ccf\clipboard.png)

关联命令

```bash
# origin 为远程仓库别名  
$ git remote add origin git@gitee.com:jiao_qianjin/h2.git 
```



2）将本地仓库与远程仓库合并

```bash
$ git pull --rebase origin master 
```



此时本地文件所在文件夹会多出几个文件夹

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\dc86e38f436e46dd9818c89b4314b499\clipboard.png)

3）推送到远程仓库

```bash
# -u选项会指定一个默认主机，这样后面就可以不加任何参数使用git push。
$ git push -u origin master #第一次推送   
$ git push origin master 
$ git push -u origin master -f # 强制提交 
```



4）在码云仓库中刷新，即可看到自己提交的文件

![img](E:\有道笔记\qq8A56EC72AACBDFBB0BBA5D9AC8BA9598\c93494fc7a39490aa10a9c7260924cd8\clipboard.png)



### 使用时遇到的问题



**1. fatal: remote origin already exists.** 

```bash
# 先删除 
$ git remote rm origin
# 再次执行添加就可以了　 
```

**2. 【git】 修改已经push了的commit信息**

如题，本条仅适用于修改已经push过了的最近一次的commit注释信息，确保本地文件的最新的。 

step1：使用【git commit --amend】命令，会进入到vim编辑器。 

step2：输入【i】，即进入编辑模式，此时编辑提交信息。 

step3：编辑好之后，输入【:wq】，即保存和退出。 

step4：输入【git push -f】强制提交。 操作完之后，再看提交记录，即可看到修改的注释信息 