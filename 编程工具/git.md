# git



## 1、基本介绍

*Git* 是一个开源的分布式版本控制系统,用于敏捷高效地处理任何或小或大的项目。



我为什么要用分布式版本控制系统？*svn* 不香吗？

​	分布式版本控制系统，分布式系统能够在离线没有网络的情况下还能继续提交代码、查看日志、分支操作等，举个例子：比如出差过程在飞机上还想继续写代码，写完一个功能应该要提交下，写错了应该能够回滚到之前的版本，下了飞机能合并小伙伴们的代码。



> "In many ways when I designed Git, it's
> the what would Jesus do? Except it's what would CVS
> never, ever do kind of approach to source control
> management  "   -- liuns 



## 2、基本概念

工作区（Working Area）：本地工作空间，一般就是我们的项目文件目录

暂存区（Stage/index）：数据暂时存放的区域，提交到本地仓库的一个缓冲区

本地仓库（Local Repository）：本地仓库是一个安全存放数据的区域，提交到本地仓库后版本库中就有日志记录。

远程仓库(Remote Repository)：目前比较热门的主要是github、gitlab等



![image-20200606201418336](http://cdn.mtmn.top/image-20200606201418336.png)

## 3、基础操作

1. #### 初始化本地库

   在当前目录下新建一个git本地库

   ```shell
   git init
   ```

   新建一个目录并初始化为git本地库

   ```shell
   git init {fileName}	
   ```

   或者从已经存在的远程仓库中获取(例如github上的项目)

   ```shell
   git clone {remoteRepositoryUrl}
   ```

2. #### 提交代码

   第一步：将本地修改提交到暂存区（stage/index）

   ```shell
   git add .
   ```

   第二步：将代码从暂存区提交到本地仓库

   ```shell
   git commit -m "{提交注释}"
   ```

   可以将上面两个步骤合并成一个

   ```shell
   git commit -am  "{提交注释}"
   ```

   第三部：将代码提交到远程仓库

   ```shell
   git push
   ```

3. #### 更新代码

   ```shell
   git pull
   ```

4. #### 版本回退

   ```shell
   git log
   git checkout {sha-1}
   ```

5. #### 分支操作

   看查分支

   ```shell
   git branch
   ```

   切换分支

   ```shell
   git checkout {branch}
   ```

   

6. #### 合并操作

   ```shell
   git merge
   ```

   

## 4、常用命令

- ### init

  创建一个普通仓库

  ```
  git init 
  ```

  创建一个裸库，无法像本地库一样可以直接进行修改和提交，一般用于git服务端仓库

  ```
  git init --bare
  ```

  

- ## add


    git add 将工作区的内容提交到暂存区

- ### commit


    git commit 将暂存区的内容提交到本地仓库
    
    ​	--amend 修改上一次的提交信息

- ### log


    git log 查看提交记录
    
    git log --oneline  以单行的形式展示提交记录
    
    git log --pretty=graph 以图形的形式展示提交记录

- ### reflog


    git reflog 查看仓库的操作记录

- ### checkout


    checkout 命令能用于切换分支，也能用于内容回退
    
    git checkout {branch} 切换到指定分支
    
    git checkout - 切换回上一个分支
    
    git checkout {hash}  回退到指定版本
    
    --soft
    
    ​--mixed
    
    ​--hard
    
    git checkout -b {branch} 创建并切换分支
    
    git checkout {localBranch} -b origin/{remoteBranch}   在本地创建远程分支
    
    git checkout --tranck origin/{remoteBranch} 在本地创建远程分支，分支名称与远程分支一致

- ### push

  git push 将本地仓库推送到远程仓库上

- ### fetch

  git fetch 可用于获取某个分支的远程信息，将远程分支上的数据下载到本地仓库，但不会对改变本地仓库，需要执行git merge将更新下来的分支内容合并到本地分支。

- ### pull

  git pull 实际上是两个命令合成了一个：git fetch 和 git merge。

  当我们从来源拉取修改时，我们首先是像 git fetch 那样取回所有数据，然后最新的修改会自动合并到本地分支中

- ### branch


    git branch 查看本地分支
    
    git branch -v 同时查看本地分支和远程分支

- ### merge

    git merge {branch}  合并指定分支到当前分支

    如果当前分支没有修改，目标分支有修改，git会执行fast forward合并，这类合并不会创建新的提交
    如果当前分支和目标分支都有修改提交，git会执行no fast forward 合并，这类合并会创建一个合并提交

- ### rebase

   rebase 会将当前分支的提交复制到指定的分支之上，主要用途是调整分支提交记录，可以用户合并多次提交记录，也可以将自己的几次提交重新理顺放在一起。

    git rebase -i HEAD ~2 压缩最近两次提交

- ### revert

  通过对特定的提交执行还原操作，我们会创建一个包含已还原修改的新提交。




## 5、目录及说明

```js
├── config               git配置信息，包括用户名，email，remote repository的地址，本地branch
|						 和remote branch的follow关系
├── description          git库的描述信息，如果使用了GitWeb的话，该描述信息将会被显示在该repo的页面上
|
├── HEAD                 工作目录当前状态对应的commit，一般来说是当前branch的head，HEAD也可以通
|						 过git checkout 命令被直接设置到一个特定的commit上，这种情况被称之为 
|  						 detached HEAD,内容格式为ref: refs/heads/master      
├── hooks                钩子脚本，可以被用于在执行git命令时自动执行一些特定操作，例如加入changeid
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── info                目录保存了一份不希望在 .gitignore 文件中管理的忽略模式 (ignored patterns) 
|						 的全局可执行文件
│   └── exclude
├── objects             保存git对象的目录，包括三类对象commit,tag, tree和blob
│   ├── info
│   └── pack
└── refs                保存branch、tag和stash对应的commit
    ├── heads           branch对应的commit
    ├── tags            tag对应的commit
    └── stash           stash对应的commit，只有用到git stash暂存数据的时候才有该文件

```



> objects 存放所有的数据，git会通过sha-1 计算文件的hash值，一般是40位hash值，前两位作用objects的下级目录，后38位作为对象文件名称。
>
> `git hash-object` 命令可以查看对象的sha-1值

> 通过git cat-file 可以查看objects对象的文件类型、大小、内容等信息
>
> git cat-file -t {sha-1} 查看对象的文件类型
>
> -s 查看文件的大小
>
> -p 查看文件的内容

## 6、存储原理

我们用一个例子来说明git的存储原理

首先我们初始化一个本地库

```shell
git init 
```

然后我们新建三个文件提交

```shell
$ touch a b c
$ git add .
$ git commit -m "first commit"
[master (root-commit) 192e80d] first commit
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 a
 create mode 100644 b
 create mode 100644 c
```

查看提交记录

```shell
$ git log --oneline
cb89161 (HEAD -> master) first commit
```

查看提交的内容

```shell
$ git cat-file -t cb89161
commit
$ git cat-file -p cb89161
tree 765b32c65d38f04c4f287abda055818ec0f26912
author xx <xx@test.com> 1591501678 +0800
committer xx <xx@test.com> 1591501678 +0800

first commit
```

可以看到我们的提交记录的sha-1值在objects里属于commit，这个commit里面包含一个tree对象，我们继续查看这个tree的内容

```shell
$ git cat-file -p 765b32
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    a
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    b
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    c
```

可以看到这个tree中包含了三个blob，也就是我们最开始新建的a b c三个文件，由于这三个文件内容都为空，他们指向的是同一个object。

> 相同内容的文件在git只会存储一份，这也是git比其他版本控制工具更加节省空间的原因之一

查看目前的存储对象（对象生成的sha-1值，前两位作为目录名称，后38位作为文件名称）

```shell
$ ls .git/objects
19/  76/  e6/  info/  pack/
$ ls .git/objects/19/
2e80d34bcc513b13ae899d1f8bb64542ca769a
$ ls .git/objects/76/
5b32c65d38f04c4f287abda055818ec0f26912
$ ls .git/objects/e6/
9de29bb2d1d6434b8b29ae775ad8c2e48c5391
```

目前的仓库的情况如下图所示，实际存储的对象只有三个

![image-20200607185648244](http://cdn.mtmn.top/image-20200607185648244.png)

现在我们将a文件的内容修改为content-a，并提交到仓库

```shell
//此处修改a的内容
$ vim a
$ git commit -am "change a"
warning: LF will be replaced by CRLF in a.
The file will have its original line endings in your working directory
[master 0bcc248] change a
 1 file changed, 1 insertion(+)
```

再次查看object目录

```shell
$ ls .git/objects
0b/  4b/  69/  76/  87/  cb/  e6/  info/  pack/
```

此时又多了三个object，根据我们之前的了解，可以推断出来应该是一个commit、一个tree、还有一个blob。我们进一步验证下

```shell
$ git log --oneline
0bcc248 (HEAD -> master) change a
cb89161 first commit
$ git cat-file -p 0bcc248
tree 69cf6709d9fad3ec1b09b81a9a9b2d49b9e86579
parent cb891615d550147ef4864675b037e046542136a8
author xx <xx@test.com> 1591515884 +0800
committer xx <xx@test.com> 1591515884 +0800

change a
$ git cat-file -t  69cf670
tree
$ git cat-file -p  69cf670
100644 blob 87d21b980caffef9221a97aadf7039d2dcfc47e2    a
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    b
100644 blob e69de29bb2d1d6434b8b29ae775ad8c2e48c5391    c
```

我们用一张图来展示下当前的情况

![image-20200607184748388](http://cdn.mtmn.top/image-20200607184748388.png)

到这里我们差不多已经知道了git的基本存储原理，现在大家知道为什么说git占用空间小、性能好了吧？commit 和tree 都只是存储少量的必要信息，只有blob是存储文件内容。在一些情况下，git本地库，占用空间甚至可能比工作空间更少的，因为相同文件git只存储了一份，git还有压缩功能例如`git gc`。git分支也仅是存储指向commit的sha-1值，所以创建分支对git来说开销是极小的。

## 7、搭建git服务器

1. 首先我们再服务器上下载安装git

   如果是centos

   ```shell
   yum install -y git
   ```

   如果是Ubuntu或debian

   ```shell
   sudo apt-get install git
   ```

2. 创建一个git用户用来运行git服务

   ```shell
   sudo adduser git
   ```

3. 初始化一个git裸库，进入到你想作为git仓库的文件目录，初始化一个git裸库，repoName这边设置你想设置的仓库名称

   ```shell
   cd /home/git/repo
   git init --bare {repoName}.git
   ```

4. 本地克隆远程仓库，这边假设我们服务器IP是192.168.1.100

   ```shell
   git clone git@192.168.1.100 /home/git/repo/{repoName}.git
   ```

5. 接下去你就可以欢快的进行push\fetch\pull了



## 8、相关问题

- #### git 中 stage 和 stash的区别

  stage是git中的一个区域，我们在基本概念中有介绍到，一般我们通过`git add`命令将工作区中的改变提交到暂存区，也就是stage区域

  stash 名词直译是藏匿处的意思，他是git中的一个命令，用于**临时存放**工作区中的改变。stash 不受分支影响，但是它必须是git 已经追踪到的文件。

  > 在工作中，有可能你在某个分支上写了一些东西，但是你突然觉得应该提交到其他分支更合适。例如你在master上写了一些东西，但是你突然觉得这个不应该在master上提交。这种场景用`git stash` 最为合适，因为git stash 不受分支的影响。你先用`git stash` 将工作区中的改变存放到暂存堆（stash），切换分支后用`git stash pop` 就可以将之前的修改重新取出。

- #### git 中merge 和rebase的区别