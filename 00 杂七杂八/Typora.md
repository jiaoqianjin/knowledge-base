### 为什么要用Typora

Typora是一款轻便简洁的Markdown编辑器，支持即时渲染技术，这也是与其他Markdown编辑器最显著的区别。即时渲染使得你写Markdown就想是写Word文档一样流畅自如，不像其他编辑器的有编辑栏和显示栏。

既见既所得的全新Markdown的协作体验征服了为数不少的程序员。

最最重要的，现在Typora还处于 beta 阶段，在macOS、Windows和Linux系统上都是免费。

### 存在的问题
**1. 云存储**：同时Typora 不支持云端存储，如果自己的电脑出现了问题，数据被清空，那么自己的心血统统都会付诸东流。

**2. 图片上传**：现在mac版可以通过 iPicUploader 进行图片上传，其他系统还不支持。如果上传到gitee,则插入的本地图片不能查看。

**3. 文档转移**：自己写的md文档插入的图片只能在本地看，如果将md文档发给别人或者同步到 csdn，图片就会查看不了，还是图片上传的问题。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020060218380214.png)

### 图片设为相对路径，发送文档携带图片文件夹
**如果只要云存储的功能（针对问题一，问题二），那么 Typora + Git 就可以搞定**
1. 图片上传
1）文件 --> 偏好设置 --> 图像
进行以下设置 
复制到指定路径
路径填写 ./images(可自定义)
这样上传的图片都存在文档同一级的 images 文件夹里
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200602184300407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
2）现在新建一个文件夹 <图片上传>，在里面新建一个文档 <测试图片上传>，
3）现在上传张图片，可以发现 <图片上传>下多出一个 < images>文件夹， 可以看到图片也存在了 < images>里面
![在这里插入图片描述](https://img-blog.csdnimg.cn/202006021852338.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)

2. 上传git （云笔记）

	1）在git 新建仓库
	关于git 的安装和使用可以参看  [一文搞定 Git 安装与使用](https://blog.csdn.net/weixin_44491927/article/details/106501848)
	
	2）将 <图片上传>上传到 Git
	
	3）在码云上查看
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020060219280775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
**文档中的图片可以随文档上传到码云，是因为文档中的图片使用的是相对路径，而相对路径对应的文件夹 < images>也上传了，所以能够找到图片，正常显示。**

4）存在的问题：
这样所以实现了手动云存储，不在怕本地数据丢失。但是如果想**把单个文档发给别人，或者 直接复制粘贴到 cdsn 写博客，图片都不能显示**，因为图片相对路径指向的 images 文件夹不存在。
### Typora + Gitee(码云) + PicGo 实现 云 markdown笔记
如果想要解决以上的问题，还是需要利用 PicGo 图床。

Typora 已经支持PicGo自动上传功能，能够自动上传拖进 markdown 文件的图片，还能将 markdown 文件中的图片一键上传。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603083346894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
1. 准备
1）安装 Typora
2）安装 PicGo
2. PicGo + Gitee(码云) 实现图床（图片上传后，会返回一个链接，只要有网就能访问图片）
1）打开下载好的 PicGo
点击左侧最下方 --> 插件设置。在右边输入框 输入：gitee，选择右边的插件，点击安装。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603160445832.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)

3. Gitee 建立图床库
1）新建仓库
2）设为公共
3）使用Readme文件初始化仓库
![G4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)](https://img-blog.csdnimg.cn/20200603161129165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
4. 配置PicGo
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603085404567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
1) *repo: 为 username + 仓库名，即
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603085344136.png)
2）branch: 分支，之前创建仓库的时候使用Readme文件初始化仓库的时候为我们创建了master 分支
3）*token:：私人令牌，获取方式
点击头像 --> 进入个人主页 --> 点击私人令牌  （私人令牌只出现一次，丢了需要重新创建）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603154741326.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
4）path: 图片存储路径，可以按照自己的设置，这里设置的是 img
5）剩下两个可以不填。提交信息，插件默认提交的是 **Upload 图片名 by picGo - 时间**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603155540190.png)
5. 保存配置，设为默认图床，完成图床设置
6. 测试上传
1）在上传区点击上传
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603155303877.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
2）在相册中查看自己上传的图片
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603155335165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
3）在Gitee查看上传的图片
可以看到仓库中，自动生成了img 文件夹
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603155849340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
7. Typora 图像设置
文件 --> 偏好设置
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603160245417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
8. 测试Typora 图片上传功能
1）截图 --> 粘贴，可以看到图片开始自动上传，上传成功后，图片路径由本地路径，变为  (https://gitee.com/jiao_qianjin/zhishiku/raw/master/img/xxxxxx)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603161735470.gif)
2）如果文档中存在本地图片也可以右键 -->上传图片，
还可以  格式 --> 图像 --> 上传所有图片。这时候你文档中的所有图片都可以一键上传。

9. 可能出现的错误
1） Failed to fetch
解决方法：打开PicGo，点击PicGo设置 -- > 设置Server选项
端口设为PicGo推荐的默认端口号：36677，保存
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603162640805.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)

2）{“success”,false}
错误原因就是文件名冲突，再次上传一张名字一样的图片就是出现这种错误
解决方法：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020060316282677.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ5MTkyNw==,size_16,color_FFFFFF,t_70)
打开时间戳重命名，删除图片的时候，图片的名字会重命名为时间戳
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603163237992.png)
这时候，你的Typora文档可以上传到Gitee中实现云存储，也可以之间将文档上传到csdn 博客，图片可以直接显示，也可以将你的文档发给别人，不用再考虑图片不显示的问题。**

