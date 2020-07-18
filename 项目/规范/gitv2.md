# 一、简介
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们常常会将自己的代码托管至一些代码管理平台进行托管，比如**Github**、**Gitee**、或是自己搭建的托管平台等，其中Git就是重要的传输控制工具。在一开始学习git的时候可能大多数小伙伴并没有**合作**和**分支**相关的概念，只是熟悉git最基本的**pull**、**commit**和**push**。如下图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200702211411147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjgyMjQ4NA==,size_16,color_FFFFFF,t_70)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Remote**是远程仓库，**Repository**是本地仓库，**workspace**是自己项目的工作区间，这些基本的概念和基本操作就不多详细解释了，接下来进入重点，我们要加入**分支管理**和**合并**的概念。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本次示例使用的托管平台为自行部署的平台——**Gitlab**，全程使用命令方式，不推荐使用任何有关git的GUI工具（**tortoise**）或是编辑器（**idea**等）里的git集成工具。
# 二、GitHub flow
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;==我们在工作中推荐使用GitHub flow 协作方式==，即不在主仓库的分支上开发，而是 **fork** 到自己 **workspace** 下。每次开发，需要先**checkout** 一个新的分支，**commit **之后推送到自己的仓库，再向主仓库提**Merge Request**。所以开发过程中对于大多数项目来说，都会经过如下步骤：

 1. ==Fork(Once)==
 2. ==Create a branch==
 3. ==Add commits==
 4. ==Open a Merge Request==
 5. ==Code review==
 6. ==Merge==
 7. ==Deploy==
# 三、流程详解
### （0）添加SSH公钥
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;为了代码和仓库的安全，我们通常使用**ssh**方式对代码仓库进行操作，这个操作比较简单，而且网上给出的方法很多，网友们可自行参考。
### （1）Fork
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一般由项目组长建立代码的主仓库，剩余小组开发人员==首先要对其仓库进行Fork==，就是从主仓库将项目复制一份到自己的仓库，这个仓库只第一次需要操作。之后再进行clone处理，不要每次看见仓库就直接clone。
     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200702212724647.png)
### （2）Clone
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将项目组长建立的主仓库Fork之后，从自己的仓库进行clone，一般来说一个仓库会有两个分支，一个develop/dev，另一个是master，==我们一般都在develop分支上进行开发，所以fork之后选择develop分支，再选择ssh的方式进行克隆：==
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703204207293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjgyMjQ4NA==,size_16,color_FFFFFF,t_70)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在你自己的电脑上，选择好你的**Workspace**工作区间（项目目录），右键**Git Bash here**打开git命令窗口（前提是你安装过git）。输入：
```shell
git clone ssh://你的地址
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该操作一般也只执行一次。
### （3）添加远程仓库
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在我们把项目clone下来之后，我们在项目的根目录再次打开git bash，首先第一行显示的是一些基本信息：
```shell
DELL@DESKTOP-24CUUK5 MINGW64 /d/project/xxx_api (check)
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一般常常关注的是最后括号内的单词，代表当前的分支。（之后详细说明分支）
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先我们需要把远程主仓库给添加进来，方便以后push和pull，==我们先输入git remote -v查看现有的仓库==：
```shell
$ git remote -v
origin  ssh://git@gitlab.xxx.cn:8022/aaa/xxx.git (fetch)
origin  ssh://git@gitlab.xxx.cn:8022/aaa/xxx.git (push)
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;项目刚拉下来的时候一般只有两个两个远程仓库的地址，一个用来fetch，一个用来push，并且这两个一般都一样，都是自己的远程源仓库origin，==我们需要做的就是将项目组长所建立的主仓库添加进来：（upstream是远程仓库的代称，可随意名命）==
```shell
$ git remote add upstream ssh://主仓库地址

$ git remote -v
origin  ssh://git@gitlab.xxx.cn:8022/aaa/xxx.git (fetch)   #自己的
origin  ssh://git@gitlab.xxx.cn:8022/aaa/xxx.git (push)    #自己的
upstream        ssh://git@gitlab.xxx.cn:8022/aaa/xxx.git (fetch)    #项目组长的
upstream        ssh://git@gitlab.xxx.cn:8022/aaa/xxx.git (push)     #项目组长的
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们可以看到成功将主仓库的地址，之后可以直接通过一下的方式push和pull代码：（**切记不要直接push主仓库，原因后面会说，一般来说你也没有权限，记着push自己的仓库origin**）
```shell
git push origin HEAD:分支
git pull upstream develop
```

### （4）查看分支和状态
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从第四步向后的所有操作，就是你每天都要重复的步骤了。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们可以通过输入**git branch**查看当前工作区间存在的分支：（如下我只有develop一个分支，*代表当前所在的分支，一般情况下刚clone下来就一个develop分支）
```shell
$ git branch
* develop
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;或是通过 **git branch -a** 查看本地和远程的全部分支：(remotes的为你的远程仓库origin下的分支)
```shell
$ git branch -a
* develop
  remotes/origin/HEAD -> origin/develop
  remotes/origin/develop
  remotes/origin/master
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们也可以通过输入**git status**来查看当前分支和分支状态：
```shell
$ git status
On branch develop
Your branch is up to date with 'origin/develop'.

nothing to commit, working tree clean
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这个也会显示咱们所处的分支，已经该分支状态，目前就是告诉咱们分支已经是最新的了，没有什么要提交的。以上几个基础命令都是用的比较多的，要熟练掌握。
### （5）新建分支
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当我要开始工作了，也就是要开始动工敲代码了，==我们先不要一股脑的在develop分支上直接开敲，先打开git bash ，先pull一下主仓库保证代码最新，之后先新建一个分支==，**git checkout -b** 分支名称：
```shell
$ git checkout -b check
Switched to a new branch 'check'

$ git branch
* check
  develop
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;可以看到我们成功新建了一个check分支并切换到了该分支上面，接下来你就可以进行你的代码工作了。你也可以通过**git checkout develop**再切换到develop分支上面：
```shell
$ git checkout develop
Switched to branch 'develop'
Your branch is up to date with 'origin/develop'.
```
### （6）stash
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当你开发完功能之后，或者这一天结束了，想要提交代码了，这个时候你要明白一件事，你们是合作开发，主仓库的更新速度是要比你的快的，也就是说很多情况主仓库的代码都是要比你的新，所以你要==先pull 主仓库的代码==。但是直接pull代码可能会直接带来冲突，所以在此之前你==需要提交或者将代码放入暂存区stash==，这里我推荐放到暂存区里面保管：
```shell
git stash save "save message"
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将代码放入暂存区之后，直接

```shell
git pull upstream develop
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这样是不会产生冲突的，这时我们将暂存区的内容拿出来（==暂存区是一个栈==，后放入的内容排在前面）：
```shell
git stash list   #查看stash了哪些存储
git stash show   #显示做了哪些改动
git stash apply  #将某个暂存取出，git stash apply stash@{$num}
git stash drop stash@{$num}  #丢弃stash@{$num}存储，从列表中删除这个存储
git stash clear #删除所有缓存的stash
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一般使用pop：==git stash pop = git stash apply stash@{0} + git stash drop stash@{0}==。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从暂存区取出后，根据个人情况再解决冲突，这里就不详细说明。
### （7）commmit & push
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;接下来要将代码提交至本地仓库，==首先要查看代码的状态==：
```shell
git diff #可以查看这次修改了什么，那些妥当，那些不妥当
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在查看了状态之后，自行选择需要提交的文件：
```shell
git add .  #这里的点是全部修改内容，或者自行选中单个文件
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里的add通俗易懂就是选中的意思，选中你要commit的内容。之后执行：
```shell
git commit -m "feat:add UserManagement"
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Commit Message 每次提交，commit message 应该清晰明了，说明本次提交的改动和目的，禁止使用无意义的乱码或单个词语。示例：

 - ==feat==：新功能（feature）
 - ==fix==：修补bug
 - ==docs==：文档（documentation）
 - ==Style==： 格式（不影响代码运行的变动）
 - ==Refactor==：重构（即不是新增功能，也不是修改bug的代码变动）
 - ==Test==：增加测试
 - ==Chore==：构建过程或辅助工具的变动
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;禁止使用 **git push -f** 。还要根据自己的项目内容合理配置好**.gitignore**文件（忽略上传某些文件），这个大家可以再去查阅相关资料。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;commit完成之后就可以push到==自己的远程仓库==了，
```shell
git push origin HEAD:分支名称
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里的==HEAD指当前分支，冒号后面指要push的分支，如果远程仓库没有，会自动在远程仓库创建该分支==。（虽然HEAD:分支名称可以省掉，用默认的，但还是建议加上）
### （8）提PR
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们到自己的远程仓库里，向主仓库提交分支合并请求，一般是自己这次工作创建的分支合并到主仓库的develop分支。以gitlab为例：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703184611483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjgyMjQ4NA==,size_16,color_FFFFFF,t_70)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;左边的是自己的仓库以及分支，右边是目标仓库和目标分支，一般就是主仓库和develop分支。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703185042644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjgyMjQ4NA==,size_16,color_FFFFFF,t_70)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;选择好分支后，可以描述这次的合并请求，加**title**和**description**，在合并选项里可以选择在和并请求同意后删除这个分支，这个看个人情况，选或是不选，一般自己新建的工作分支可以选择勾上。之后就可以submit提交了，等待项目负责人的回应。这里也可以选择右上角的Assign——>Edit，来选择管理人员，效果相当于“@提醒一下”。
### （9）Code Review
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;之后就是在项目负责人那边查看这个合并请求，进行Code Review，主要是：
* ==发现错误==：人都会不可避免的出现一些纰漏，而这些纰漏在另一个人眼中也许显而易见。
* ==健壮性检查==：代码是否健壮，是否有潜在安全、性能风险。代码是否可以回滚。
* ==质量保证==：在一般情况下，新提交的代码一定需要写测试，测试不只可以保证你的提交符合预期，还可以在后人改你的代码时多一层保障。
* ==统一风格==：对于整个团队来说，代码风格的统一很重要。
* ==完善注释==：包括 commit message、代码中复杂实现是否有解释性的注释、紧急 hack 是否明确标注等。
* ==互相学习==
* 哪些人需要参与 Review
		1. 项目的维护者_Tech leader_Mentor
		2. 上下游影响的相关人员

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Reviewer人员还可以对相关的代码做评论，点赞等等，再所有人觉得代码没有问题之后，便可以接收这次合并请求，你的分支就合并到了主仓库的主分支上，你的代码就算是加上去了。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;当然，主仓库管理人也可以对你的合并请求提出问题，做出相关评论，暂时不合并你的分支，甚至取消这个请求，这时你就需要查看评论和问题，修改代码，再次push你自己的这个分支，另一边也能及时看到变化。如果你的合并请求被取消了，只能重新再提交请求。
### （10）END
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在分支合并请求通过后，你这次的任务就算是完成了，你可以将本地的刚新建的工作分支合并到develop分支上，再删除该分支，例如这次工作新建的分支为check：
```shell
git checkout develop #切换到develop分支
git merge check # 将check分支合并到当前分支
git branch -d check #删除check分支
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;或者你也可以直接删除chek分支，pull主仓库来更新代码，也别忘了同步更新你自己的仓库。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;以上10步就是这次使用流程的全部内容，==4~10步会是你每天都要重复的工作==，下面放一张整体流程图：&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703202005532.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjgyMjQ4NA==,size_16,color_FFFFFF,t_70)
### （11）注意事项

 - 在每次新建分支之前和push之前，都要**pull**一下主仓库，保证代码最新，避免产生冲突。
 - 任何关于项目的私密信息，例如一些**账号密码**等，不要放到开源平台上！也不要随意把项目源码在未经负责人的同意下私自开源。
 - 在每次提交了合并请求之后不要立马删除该分支，并且写新的代码之前也要再新建一个分支，因为分支合并请求提交之后需要时间，如果合并请求不通过还要在该分支上进行修改。
 - 如果有项目伙伴因需求更改了项目文件名称或是一些文件夹的名称，需要告知所有在工作中的伙伴停下手中的工作，等待文件名称修改后，push到仓库后，其他伙伴再拉新的代码，再进行工作。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里推荐git的学习地址：[Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600)

