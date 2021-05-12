#### 版本控制

##### 定义

```
版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统，便于查看更改历史，备份以及回复以前的版本，进而保证多人协作不出问题。

失败的版本管理：例如我们写论文时，经常会写终、终终、终终终等命名
```

```
diff与patch是用于源码版本控制中最基本的两个概念
```

###### diff

```
diff用来比较连个文件或者目录之间的差异
```

![image-20210508173742092](C:\Users\a\AppData\Roaming\Typora\typora-user-images\image-20210508173742092.png)

###### patch

```
patch是diff的反向操作。通过差异化比较命令diff可获得差异结果文件，例diff.text。则可以diff.text文件从left.c推算出right.c或从right.c推算出left.c。
```

```shell
利用diff.text将left.c转化为right.c文件，命令结束后，left.c内容与right.c一致
$ patch left.c diff.text

利用diff.text将right.c转化为left.c文件，命令结束后，right.c内容与left.c一致。
-R是指反响操作，因为diff.text中，+-号代表了源文件与目标文件，故不可直接双向转换
$ patch -R right.c diff.text
```

##### RCS

```
    RCS(Revision Control System)是最早期的本地版本控制工具。RCS将diff产生的结果组成集合，以
    RCS独有的格式    保存到磁盘中(diff -n left.c right.c阐述RCS格式的diff内容)，能通过这些diff集合，重新回到文件修改的任何历史版本。
    
    RCS的缺陷在于，其只适合于本地的版本控制，多人协作时，无法保证多人修改。
```

##### CVS&SVN

```
    CVS(Concurrent Versions System) 第一个被大规模使用的版本控制工具（放在公共电脑，多人可同时使用）
    CVS的缺陷在于，其不支持原子化提交，即多人同时上传时可能会导致数据不完整（一个上传被另一个打断）；且网络传输效率较低
```

```
    SVN(Subersion) 目的是创建更好用的版本控制系统以取代CVS。优化了内容的存储，实现了原子提交。
    SVN的缺陷在于，在其局域网之外查看日志或提交数据的延迟较大。
```

 

```
    上述集中式版本控制工具的缺陷:
    提交排队，不可同时修改
    本地代码提交到服务器之间缺失检查防护
    数据安全性差
```

##### GIT

```
    是一个版本控制工具，且为开源的分布式版本控制工具
    特点：
极快的速度、简单的设计、允许若干个并行开发的分支、完全分布式、可高效管理超大规模项目
```

###### 基本配置

```
1、系统配置（针对所有用户）
    存放在git的安装目录，即%Git%/etc/gitconfig，调用时输入：git config --system core.autocrlf
2、用户配置（针对该用户）
    存放在用户目录下，即~/.config调用时输入：git config --global user.name
3、仓库配置（针对当前项目）
   存放在仓库的配置文件，即工作目录中的.git/config文件，调用时使用：git config --local remote.origin.url
   
   需注意的是，若不同级别中存在同名变量，则每一级别的配置会覆盖上一级别的相同配置。即.git/config中的配置会覆盖%Git%/etc/gitconfig中的同名变量。
```

```
配置个人身份
    
    首次git设定
    git config --global user.name "Lewao"
    git config --global user.email 942633456@qq.com
    
    上述配置信息可在Git仓库中的修改信息中体现，但和Git服务器认证使用的密码或公钥密码无关。怎么理解?将个人身份配置当作是一个人，相应的配置的信息则为这个人的信息，那这个做的工作（仓库）自然可划分为这个人的领域。但Gut服务器包括了许多用户，故有一个单独的密码。
```

```
文本换行符配置

    需注意的是，在windows操作时，一行的结束符为crlf（回车和换行两个字符），而在mac和linux中，只使用换行字符。故容易造成跨平台的功能混乱现象。因此，引入core.autocrlf来实现crlf与lf的转换。
    1、若在windows系统上，则需设置为true，此时git可在提交时，自动将crlf转化为lf
       $ git config --global core.autocrlf true
       这样，从远程服务器或linux迁出代码时，lf会被自动转化为crlf
    2、若在linux或mac系统中本身就使用lf作为行结束符，那么无需Git进行转化，因此
   	   $ git config --global core.autocrlf input
    3、若在windows下进行代码编写，且此开发仅允许于windows，则
       $ git config --global core.autocrlf false
       此时，回车记录在库中
```

```
文本编码配置

    若提交与查看log时采用的编码不同，则易出现乱码情形，因此，建议将提交与查看均设置为utf-8：
    git config --global gui.encoding utf-8
    git config --global il8n.commitencoding utf8
    git config --global il8n.logoutputencoding utf8
```

```
与服务器认证配置
    
    常见两种认证
    
    1、http/https协议认证
    设置口令缓存：git config --global credential.helper store。可令服务器记住上次认证的结果，类似于保存密码
    添加https证书信任：git config --global http.sslVerify false。令服务端公钥变为可信结果。
    2、ssh协议使用公钥认证，无需输入密码，且提供加密传输，操作便利安全性高
```

```
SSH配置过程

    第一步：生成公钥。打开Git Bash，输入命令：$ ssh-keygen -t rsa -C "942633456 @qq.com"
    第二步：添加公钥到代码平台：登陆代码平台；进入profile setting；惦记左侧栏的SSH Keys；惦记Add SSH Keys，将生成的公钥内容复制到public key栏。
```

###### Git基本命令

```
Git版本控制下的三种工程区域&文件状态

    工程区域分三种：
    1、版本库（repository）：工作区域中有一隐藏目录.git，该文件夹即为Git的版本库，用来存放Git管理该工程的所有版本数据，故也称为本地仓库。
    2、工作区（working directory）：日常工作的代码文件或文档所在的文件夹。
    3、暂存区（stage）：一般存放在工程根目录。git/index下，因此也就做索引。
    
    文件状态也分为三种：
    1、已提交（committed）：该文件已被安全保存到本地数据库中。
    2、已修改（modified）：修改了某文件，但还没有被提交。
    3、已暂存（staged）：把已修改的文件放在下次提交时要保存的清单中。
    
    当我们使用 git add 命令时，会将文件添加到暂存区（也称索引区）。需要使用 git commit 命令才能将修改提交到对象库中，这里的 「提交」对应的英文也就是 commit。
```

  ![preview](https://pic1.zhimg.com/v2-cbe795a4dd1084045ed6419e23cbeab8_r.jpg)  

```
git init 项目名称：执行后，当前目录下生成一个.git目录，代表当前项目所在目录已纳入Git管理。
    
    git clone：用于将远端工程克隆到本次磁盘。
    1、确定自己Git账号账户拥有访问、下载该工程的权限；
    2、获取该工程的Git仓库URL；
    3、本地执行 git clone [URL] 或者 git lfs clone [URL]

    lfs插件的作用时便于对二进制文件进行克隆。由于二进制文件过大，直接克隆的速度较慢，且需要进行分割，导致工程内容不完整。
    因此，在克隆二进制文件时，先询问管理员是否支持lfs插件，若支持，则通过lfs进行克隆。
```



```
新增/删除/移动文件到暂存区

    git add：若文件已被git追踪，即曾被提交过，则对于老版本的git需要再次提交，新版本无需再提交。
    git rm：若一个文件被git rm后，进行了提交，则其将脱离git的跟踪，此文件不再受git工程的管理。同样，若直接在硬盘删除该文件，再git commit，此文件也不会受git管理。
    git mv：可用于移动文件，也可用于重命名文件。  
```

```
查看工作区

    git diff：用于比较项目中任意两版本的差异，也可用来比较当前索引和上次提交间的差异
    
    git status：用于显示工作目录和暂存区的状态。
```

![image-20210510201054352](C:\Users\a\AppData\Roaming\Typora\typora-user-images\image-20210510201054352.png)

```
提交更改的文件

    git：将暂存区的文件改动记录提交到本地的版本库， 不影响远端服务器。
    一半需要附带提交描述信息，故：git commit 文件名 -m “commit message”。
    提交成功后，git日志可查到此次提交的id和提交描述信息。
    若一次性提交所有暂存区中改动的文件，则git commit -am “commit message”
```

```
查看日志

    git log查看提交历史
    git log -1 只看第一个
    取回更新后，会返回一个FETCH_HEAD ，指的是某个branch在服务器上的最新状态，我们可以在本地通过它查看刚取回的更新信息git log -p FETCH_HEAD
```

```
推送至远端仓库

    git push：git commit是将自己的修改从暂存区提交到本地版本库，git push则将本地版本库中的分支推送到远程服务器上对应的分支。
    git push origin brach_name.branch_name决定了推送成功后，远端服务器上的分知名。
    git push origin branch_name:new_branch_name：本地分支名与远端不一致
    注意 pull origin 远端：本地
```

```
分支管理
 
    git branch 查看本地工程的所有git分支名称
    git branch -r 查看远端服务器的分支，返回的分支带origin前缀
    git branch -a 查看远端服务器和本地工程的所有分支
    
    需注意，若未提交任何节点，则直接用branch无法查看，需提交文件后才可。
    
    git branch 新建分支后，不会切换到新分支里
    git chechout -b新建分支后，则切换到新分支
    新建分支不会在目录中看出
    
    git branch -d/D 均可删除分支，D表示强制删除（删除分支时，当前分支不能停留在要删除的分支上，要切换到其他任意分支，再去删除目标分支）
    git -d -r branch_name:删除远端服务器上的分支
    git push origin :branch_name 删除后，还需推送到服务器才可
    
    git checkout 切换分支
    git check -f 强制切换
    
    git pull 从服务器获取某个分支的更新，再与本地指定的分支进行自动合并。
    git pull origin remote_branch:local_branch
    若远程与本地分支名一致，则直接执行，git pull origin remote_branch
    
    git fetch 则是从服务器获取某个分支的更新，不进行合并
    git fetch origin remote_branch:local_branch
    若远程与本地分支名一致，则直接执行，git fetch origin remote_branch
    
    git merge 将制定分支合并到当前分支。git会将指定分支与当前分支进行比较，找出而这最近的一个公共结点base之后将指定分支在base之后分离的节点合并到当前的分支上。
    git merge branch_name
    git rebase 
       
```

```
撤销操作

    git reset 撤销当前工作区中的某些git add/commit操作，可将工作区内容会退到历史提交节点。git reset commit_id
    
    git checkout . 回退本地所有修改而未提交的文件内容。相当于取消本地工作取得修改，直接用暂存区的文件覆盖本地文件。
    git checkout -filename 仅回退某个未提交的文件
    git checkout commit_id 回退到某个提交版本，可看做切换到一个临时分支
```

##### 实战

###### 克隆

```
报错，无法克隆，按照教程实现，但不清楚为啥
https://blog.csdn.net/dashi_lu/article/details/89641778
```

