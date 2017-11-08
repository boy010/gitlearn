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
* 第1步：创建SSH Key (注意使用默认值即可)
    ```buildoutcfg
    $ ssh-keygen -t rsa -C "youremail@example.com"
    ```
    * 目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。  
* 第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面。然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容，点“Add Key”

### 4.1 添加远程库
1. 登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库
1. 目前，在GitHub上的这个learngit仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。
1. 我们根据GitHub的提示，在本地的learngit仓库下运行命令：
    ```buildoutcfg
    $ git remote add origin git@github.com:boy010/learngit.git
    ```
1. 添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。
1. 下一步，就可以把本地库的所有内容推送到远程库上
    ```buildoutcfg
    $ git push -u origin master
    Counting objects: 19, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (19/19), done.
    Writing objects: 100% (19/19), 13.73 KiB, done.
    Total 23 (delta 6), reused 0 (delta 0)
    To git@github.com:michaelliao/learngit.git
     * [new branch]      master -> master
    Branch master set up to track remote branch master from origin.                             
    ```
1. 把本地库的内容推送到远程，用**git push**命令，实际上是把当前分支master推送到远程。由于远程库是空的，我们第一次推送master分支时，加上了 **-u参数**，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
1. 从现在起，只要本地作了提交，就可以通过命令：**git push origin master**把本地master分支的最新修改推送至GitHub

#### 小结：
    要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；
    
    关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
    
    此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

### 4.2 从远程库克隆
* 假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆
    * 我们勾选Initialize this repository with a README，这样GitHub会自动为我们创建一个README.md文件。创建完毕后，可以看到README.md文件
* 下一步是用命令**git clone**克隆一个本地库:
    ```buildoutcfg
    $ git clone git@github.com:boy010/gitskills.git
  
    Cloning into 'gitskills'...
    remote: Counting objects: 3, done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Receiving objects: 100% (3/3), done.
    
    $ cd gitskills
    $ ls
    README.md
    ```
* GitHub给出的地址不止一个，还可以用**https://github.com/michaelliao/gitskills.git**这样的地址

# 5.分支管理
### 5.1 创建与合并分支
1. 首先，我们创建dev分支，然后切换到dev分支：
    ```buildoutcfg
    $ git checkout -b dev
    Switched to a new branch 'dev'
    ```
    * **git checkout**命令加上 **-b**参数表示**创建并切换**，相当于以下两条命令：
        ```buildoutcfg
        $ git branch dev
        $ git checkout dev
        Switched to branch 'dev'
    ```
1. 然后，用**git branch**命令查看当前分支。git branch命令会列出所有分支，当前分支前面会标一个*号
1. dev分支的工作完成，我们就可以切换回master分支： **$ git checkout master**
1. 把dev分支的工作成果合并到master分支上：
    ```buildoutcfg
    $ git merge dev
    Updating d17efd8..fec145a
    Fast-forward
     readme.txt |    1 +
     1 file changed, 1 insertion(+)
    ```
    * 注意到上面的Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。
1. 合并完成后，就可以放心地删除dev分支了： **$ git branch -d dev**
1. 删除后，用**$ git branch**命令查看branch，就只剩下master分支了

#### 小结：
    Git鼓励大量使用分支：
    
    查看分支：git branch
    
    创建分支：git branch <name>
    
    切换分支：git checkout <name>
    
    创建+切换分支：git checkout -b <name>
    
    合并某分支到当前分支：git merge <name>
    
    删除分支：git branch -d <name>

### 5.2 解决冲突
* 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
* 用**git log --graph**命令可以看到分支合并图：
    ```buildoutcfg
    $ git log --graph --pretty=oneline --abbrev-commit
    *   59bc1cb conflict fixed
    |\
    | * 75a857c AND simple
    * | 400b400 & simple
    |/
    * fec145a branch test
    ...
    ```
* 最后，删除feature1分支：**$ git branch -d feature1**

### 5.3 分支管理策略
* 分之合并时，Git默认使用的是Fast forward模式，这种模式下，删除分支后，会丢掉分支信息。
* 合并分支时，加上--no-ff参数就可以用普通模式合并，**git merge --no-ff -m "merge with no-ff" dev**,合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。
    ```buildoutcfg
    $ git merge --no-ff -m "merge with no-ff" dev
    Merge made by the 'recursive' strategy.
     readme.txt |    1 +
     1 file changed, 1 insertion(+)
    ```
    * 因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。
* 合并后，我们用git log看看分支历史：
    ```buildoutcfg
    $ git log --graph --pretty=oneline --abbrev-commit
    *   7825a50 merge with no-ff
    |\
    | * 6224937 add merge
    |/
    *   59bc1cb conflict fixed
    ...
    ```

### 5.4 Bug分支
* Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
    ```buildoutcfg
    $ git stash
    Saved working directory and index state WIP on dev: 6224937 add merge
    HEAD is now at 6224937 add merge
    ```
* 此时，用git status查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。
    ```buildoutcfg
    $ git checkout master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 6 commits.
    $ git checkout -b issue-101
    Switched to a new branch 'issue-101'
    
    $ git add readme.txt 
    $ git commit -m "fix bug 101"
    [issue-101 cc17032] fix bug 101
     1 file changed, 1 insertion(+), 1 deletion(-)
     
     $ git checkout master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 2 commits.
    $ git merge --no-ff -m "merged bug fix 101" issue-101
    Merge made by the 'recursive' strategy.
     readme.txt |    2 +-
     1 file changed, 1 insertion(+), 1 deletion(-)
    $ git branch -d issue-101
    Deleted branch issue-101 (was cc17032).
    
    $ git checkout dev
    Switched to branch 'dev'
    $ git status
    # On branch dev
    nothing to commit (working directory clean)
    
    ```
* 工作区是干净的，刚才的工作现场存到哪去了？用git stash list命令看看：
    ```buildoutcfg
    $ git stash list
    stash@{0}: WIP on dev: 6224937 add merge
    ```
* 工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：
    * 一是用**git stash apply**恢复，但是恢复后，stash内容并不删除，你需要用**git stash drop**来删除
    * 另一种方式是用git stash pop，恢复的同时把stash内容也删了：
    ```buildoutcfg
    $ git stash pop
    # On branch dev
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #       new file:   hello.py
    #
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #       modified:   readme.txt
    #
    Dropped refs/stash@{0} (f624f8e5f082f2df2bed8a4e09c12fd2943bdd40)
    ```
 * 再用**git stash list**查看，就看不到任何stash内容了
 * 可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：
    ```buildoutcfg
    $ git stash apply stash@{0}
    ```
#### 小结：
    修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

    当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。

### 5.5 Feature分支
* 开发一个新feature，最好新建一个分支.
* 删除分支用命令： **git branch -d <name>**.
* 如果要丢弃一个没有被合并过的分支，可以通过 **git branch -D <name>** 强行删除.

### 5.6 多人协作
* 要查看远程库的信息，用**git remote** 
* 或者，用git remote -v显示更详细的信息：
    ```buildoutcfg
    $ git remote -v
    origin  git@github.com:michaelliao/learngit.git (fetch)
    origin  git@github.com:michaelliao/learngit.git (push)
    ```
#### 推送分支
* 推送分支命令**git push origin master**,就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

#### 抓取分支
* 模拟一个你的小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆：
    * **$ git clone git@github.com:michaelliao/learngit.git**
* 当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支
* 要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：
    * **$ git checkout -b dev origin/dev**
*  **git push origin dev**同时修改后推送有时会失败， Git已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送： 
    ```buildoutcfg
    $ git pull
    remote: Counting objects: 5, done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 3 (delta 0), reused 3 (delta 0)
    Unpacking objects: 100% (3/3), done.
    From github.com:michaelliao/learngit
       fc38031..291bea8  dev        -> origin/dev
    There is no tracking information for the current branch.
    Please specify which branch you want to merge with.
    See git-pull(1) for details
    
        git pull <remote> <branch>
    
    If you wish to set tracking information for this branch you can do so with:
    
        git branch --set-upstream dev origin/<branch>
    ```
* git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：
    ```buildoutcfg
    $ git branch --set-upstream dev origin/dev
    Branch dev set up to track remote branch dev from origin.
    ```
* 此时再pull就成功了。
* 因此，多人协作的工作模式通常是这样：
    

    首先，可以试图用git push origin branch-name推送自己的修改；

    如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

    如果合并有冲突，则解决冲突，并在本地提交；

    没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！

    如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。
    
#### 小结：


    查看远程库信息，使用git remote -v；

    本地新建的分支如果不推送到远程，对其他人就是不可见的；

    从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；

    在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；

    建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；

    从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。


# 6. 标签管理
### 6.1 创建标签
* 首先，切换到需要打标签的分支上，然后输入命令**git tag <name>** 就可以打一个新标签
* 可以用命令**git tag**查看所有标签
* 默认tag是创建到最新提交的commit上的。如果想打标签到之前的版本上，可以用**git log --pretty=oneline --abbrev-commit**来查看历史提交的commit ID,然后输入命令**git tag v0.9 6224937**
* 标签不是按时间顺序列出，而是按字母排序的。可以用**git show <tagname>** 查看标签信息
* 还可以创建带有说明的标签，**-a**指定标签名，**-m**指定说明文字：
    ```buildoutcfg
    $ git tag -a v0.1 -m "version 0.1 released" 3628164
    ```
* 还可以通过-s用私钥签名一个标签：**$ git tag -s v0.2 -m "signed version 0.2 released" fec145a**

#### 小结：

    1. 命令git tag <name>用于新建一个标签，默认为HEAD，也可以指定一个commit id；

    2. git tag -a <tagname> -m "blablabla..."可以指定标签信息；

    3. git tag -s <tagname> -m "blablabla..."可以用PGP签名标签；

    4. 命令git tag可以查看所有标签。

### 6.2 操作标签
* 如果标签打错了，也可以删除：**git tag -d v0.1**
* 因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除
* 如果要推送某个标签到远程，使用命令git push origin <tagname>：
    ```buildoutcfg
    $ git push origin v1.0
    Total 0 (delta 0), reused 0 (delta 0)
    To git@github.com:michaelliao/learngit.git
     * [new tag]         v1.0 -> v1.0
    ```
* 或者使用命令**$ git push origin --tags**，一次性推送全部尚未推送到远程的本地标签：
    ```buildoutcfg
    $ git push origin --tags
    Counting objects: 1, done.
    Writing objects: 100% (1/1), 554 bytes, done.
    Total 1 (delta 0), reused 0 (delta 0)
    To git@github.com:michaelliao/learngit.git
     * [new tag]         v0.2 -> v0.2
     * [new tag]         v0.9 -> v0.9
    ```
* 删除远程标签：
    1. 先从本地删除：**$ git tag -d v0.9**
    1. 然后，从远程删除。删除命令也是push，但是格式如下(**$ git push origin :refs/tags/v0.9**)：
    ```buildoutcfg
    $ git push origin :refs/tags/v0.9
    To git@github.com:michaelliao/learngit.git
     - [deleted]         v0.9
    ```
#### 小结：
    命令git push origin <tagname>可以推送一个本地标签；

    命令git push origin --tags可以推送全部未推送过的本地标签；

    命令git tag -d <tagname>可以删除一个本地标签；

    命令git push origin :refs/tags/<tagname>可以删除一个远程标签。

# 7. 自定义Git
* 让Git显示颜色，会让命令输出看起来更醒目。 **$ git config --global color.ui true**

### 7.1 忽略特殊文件
* 在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件
* 有些时候，你想添加一个文件到Git，但发现添加不了，原因是这个文件被.gitignore忽略了，可以用 **-f**强制添加到Git
* **$ git add -f App.class**
* 或者你发现，可能是.gitignore写得有问题，需要找出来到底哪个规则写错了，可以用git check-ignore命令检查：
    ```buildoutcfg
    $ git check-ignore -v App.class
    .gitignore:3:*.class    App.class
    ```
* 最后一步就是把.gitignore也提交到Git，就完成了！当然检验.gitignore的标准是git status命令是不是说working directory clean
   
#### 小结：
    1. 忽略某些文件时，需要编写.gitignore；

    2. .gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！

### 7.2 配置别名
* **$ git config --global alias.st status**
* **git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"**
* 每个仓库的Git配置文件都放在.git/config文件中,别名就在[alias]后面，要删除别名，直接把对应的行删掉即可



