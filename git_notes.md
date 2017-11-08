# 1. 安装git

* Linux
    * sudo apt-get install git
* Windows
    * 下载git安装， 然后配置
    ```buildoutcfg
    $ git config --global user.name "Your Name"
    $ git config --global user.email "email@example.com"
    ```
    * git config命令的 **--global** 参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。
    
# 2. 创建新版本
1. 选择一个合适的地方，创建一个空目录
    ```buildoutcfg
    $ mkdir learngit
    $ cd learngit
    $ pwd
    /Users/michael/learngit
    ```
1. 通过**git init**命令把这个目录变成Git可以管理的仓库：
    ```buildoutcfg
    $ git init
     Initialized empty Git repository in /Users/michael/learngit/.git/
     ```
1. 用命令git add告诉Git，把文件添加到仓库
    ```buildoutcfg
    $ git add readme.txt
    ```
1. 用命令git commit告诉Git，把文件提交到仓库
    ```buildoutcfg
    $ git commit -m "wrote a readme file"
    [master (root-commit) cb926e7] wrote a readme file
     1 file changed, 2 insertions(+)
     create mode 100644 readme.txt
    ```
  #### 小结：
    初始化一个Git仓库，使用git init命令。
    
    添加文件到Git仓库，分两步：
    
    第一步，使用命令git add <file>，注意，可反复多次使用，添加多个文件；
    
    第二步，使用命令git commit，完成。
    
# 3. 时光机穿梭
* 要随时掌握工作区的状态，使用**git status**命令
* 如果git status告诉你有文件被修改过，用**git diff**可以查看修改内容
### 3.1 版本回退
* **git log** 来查看git的历史记录，显示从最近到最远的提交日志
* git log加上 **--pretty=oneline** 参数,可以简化输出信息
    ```buildoutcfg
    $ git log --pretty=oneline
    3628164fb26d48395383f8f31179f24e0882e1e0 append GPL
    ea34578d5496d7dd233c827ed32a8cd576c5ee85 add distributed
    cb926e7ea50ad11b8f9e909c05226233bf755030 wrote a readme file
    ```
* 在Git中，用HEAD表示当前版本。上一个版本就是**HEAD^**，上上一个版本就是**HEAD^^**，当然往上100个版本写成**HEAD~100**。
* 回退到上一版本使用**git reset**命令, 也可以将HEAD^改成对应的commit ID的前几位，也可以切换到对应的版本上。
    ```
    $ git reset --hard HEAD^
    HEAD is now at ea34578 add distributed
    ```
* Git提供了一个命令**git reflog**用来记录你的每一次命令：
    ```buildoutcfg
    $ git reflog
    ea34578 HEAD@{0}: reset: moving to HEAD^
    3628164 HEAD@{1}: commit: append GPL
    ea34578 HEAD@{2}: commit: add distributed
    cb926e7 HEAD@{3}: commit (initial): wrote a readme file
    ```
#### 小结：
    HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。

    穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。

    要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

### 3.2 工作区和暂存区
* 工作区（Working Directory）
    * 就是你在电脑里能看到的目录，比如我的learngit文件夹就是一个工作区
* 版本库（Repository）
    * 工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。
    * Git的版本库里存了很多东西，其中最重要的就是称为**stage（或者叫index）的暂存区**，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。
    
### 3.3 管理修改
* 第一次修改 -> git add -> 第二次修改 -> git commit
    * Git管理的是修改，当你用git add命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，git commit只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。
* 提交后，用**git diff HEAD -- readme.txt**命令可以查看工作区和版本库里面最新版本的区别：
    ```buildoutcfg
    $ git diff HEAD -- readme.txt 
    diff --git a/readme.txt b/readme.txt
    index 76d770f..a9c5755 100644
    --- a/readme.txt
    +++ b/readme.txt
    @@ -1,4 +1,4 @@
     Git is a distributed version control system.
     Git is free software distributed under the GPL.
     Git has a mutable index called stage.
    -Git tracks changes.
    +Git tracks changes of files.
    ```
* 那怎么提交第二次修改呢？你可以继续git add再git commit，也可以别着急提交第一次修改，先git add第二次修改，再git commit，就相当于把两次修改合并后一块提交了：
    * 第一次修改 -> git add -> 第二次修改 -> git add -> git commit

#### 小结：
    每次修改，如果不add到暂存区，那就不会加入到commit中。

### 3.4 撤销修改
* **git checkout -- readme.txt**可以丢弃工作区的修改
* 用命令**git reset HEAD readme.txt**可以把暂存区的修改撤销掉
#### 小结：
    场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
    
    场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。
    
    场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，git reset --hard HEAD^，不过前提是没有推送到远程库。

### 3.5 删除文件
* 当在工作区删除一个文件后，如果确实要从版本库中删除该文件，那就用命令**git rm**删掉，并且再**git commit**
    ```buildoutcfg
    $ git rm test.txt
    rm 'test.txt'
    $ git commit -m "remove test.txt"
    [master d17efd8] remove test.txt
     1 file changed, 1 deletion(-)
     delete mode 100644 test.txt
    ```
* 如果是删错了，因为版本库里还有，可以用**git checkout -- test.txt**把误删的文件恢复到最新版。

# 4.远程仓库






































