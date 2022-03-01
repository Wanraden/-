# git-cmd-operation  

## 一、基本要求  

*1.保证本地库与远程库一致的情况下,进行开发和操作*  

+ git pull,拉取/合并最新的远程库【建议采用git fetch拉取到本地,通过git merge手动合并】 

*2.开发过程务必新建branch,保证master分支整洁*  

> **内核开发管理**
>
> * 本地创建库git init  
>   git remote add origin url(自己建立的github的远程库)
> * 添加内核远程库地址  
>   git remote add linux  url(内核远程链接)
> * 把内核远程库代码下载到本地  
>   git fetch linux(在master分支上,这个操作不会带来合并)
> * 基于内核源码库创建linux-4.0分支,并切换到linux-4.0分支上  
>   git  chechout -b linux-4.0 linux/master
> * 下载linux 4.0内核(包含linux 4.0所有提交信息)  
>   git reset v4.0 --hard (基于标签进行回滚)
> * 合并linux-4.0分支到主分支上  
>   git checkout master  
>   git merge linux-4.0  --allow-unrelated-histories
> * 查看日志  
>   git log --oneline (包含了所有的linux 4.0的提交日志)
> * 推送到自己的远程仓库  
>   git push origin master  
> * 创建dev分支进行内核开发  
>   git checkout -b dev  

*3. 常用远程仓库(代码源)*

> * github
> * gitlab
> * gitee
> * bitbucket  

*4. [常用图形化管理界面工具](https://git-scm.com/downloads/guis)*  

> * Gitkraken  
> * SourceTree(windows、mac)
> * Giggle(linux)
> * Git(windows)  
> * TortoiseGit(windows)  
> * Git Extensions(windows、mac、linux)
> * SmartGit(windows、mac、linux)  
> * GitEye(windows、mac、linux)  
> * gitg(windows、linux)  
> * ungit(windows、mac、linux)  
> * git-cola(windows、mac、linux)  
> * Tower(mac)
> * Gitbox(mac)  
> * GitUp(mac)

*5. 下载*  

> * [官网下载](https://git-scm.com/)，下载较慢
> * [镜像网站下载](https://npm.taobao.org/mirrors/git-for-windows) ，下载较快

*6. 学习及参考资料*  

> * [在线学习网站](https://learngitbranching.js.org/?locale=zh_CN)

## 二、git版本库架构  

* workspace：工作区
* staging area：暂存区/缓存区
* local repository：本地仓库
* remote repository：远程仓库

```
         git add     git commit     git push  

    工作区  ->  暂存区  ->  本地仓库  ->  远程仓库  
           <-          <-           <-  
      git checkout   git reset    git pull/fetch
```

## 三、git常用命令  

### 1.git config

* 配置用户名  
  git config --global user.name "wanters"
* 配置邮箱  
  git config --global user.email "ztf_mail@163.com"
* 配置暂存账号和密码  
  git config --global credential.helper cache
* 查看当前配置  
  git config --list

### 2.git init

* 创建仓库
  git init [--bare]

### 3.git add

* 添加指定文件到暂存区  
  git add file
* 添加所有修改(新建)文件到暂存区(空文件夹不会添加)  
  git add .
* 只将暂存区已有的修改的文件添加到暂存区  
  git add -u  
* 同一个文件多处变化，实现分次提交  
  git add -p  

### 4.git commit

* 将暂存区文件提交到本地仓库  
  git commit
* 直接带comment提交  
  git commit -m 'comment'
* 追加提交(在不增加commitid的情况下提交)  
  git commit --amend
* 添加署名(Developer Certificate of Origin)   
  git commit -s

### 5.git status

* 查看仓库状态  
  git status [-s]  

### 6.git format-patch

* 生成1个patch
  git format-patch HEAD^
* 生成2个patch  
  git format-patch HEAD^^  
* 生成两个commit之间的patch(包括两个commit)  
  git format-patch  commitid1..commitid2  
* 生成从根到指定commit之间的patch  
  git format-patch --root commitid  

### 7.git apply

* 查看patch  
  git apply --stat file.patch
* 检查是否可以正确应用patch  
  git apply --check file.patch

### 8.git am

* 应用patch  
  git am file.patch
* 撤销到未打patch之前(多个patch有失败时)  
  git am --abort
* 解决patch冲突  
  git am --resolved

> git apply 与 git am 区别
> git am: 将补丁提交并在本地创建一个commit,无需git add 和 git commit  
> git apply: 仅对本地文件打补丁,不创建commit  

### 9.git diff

* 格式
  git diff [from-file] [to-file]  
  a：前面文件 b：后面文件
* 比较工作区和暂存区之间的区别(暂存区不为空)  
  git diff  
* 比较暂存区和本地仓库的区别  
  git diff --cached 或 git diff --staged
* 比较工作区[+暂存区]与本地仓库的区别  
  git diff HEAD
* 比较两个分支最新的commit区别  
  git diff branch1 branch2
* 比较自两个分支分开以来的区别  
  git diff branch1..master
* 比较两个commit  
  git diff commitid1 commitid2
* 查看简单的diff结果  
  git diff --stat  

### 10.git log

* 默认输出(commit hash,author,date,commit message)  
  git log
* 仅输出commit hash 前几个字符串和commit message  
  git log --oneline  
* git log 的基础上输出文件增删改的统计数据  
  git log --stat  
* 详细输出(diff的形式)  
  git log -p [file]  
* 输出汇总以作者进行分类的commit message  
  git shortlog  
* 统计每个作者的commit数量  
  git shortlog -s  
* 输出完整commit hash和commit message  
  git log --pretty=oneline  
* 过滤输出  
  git log --author="wanters": 指定作者  
  git log -2：指定输出条数  
  git log --merges：输出合并commit
* 显示指定文件是什么人在什么时间修改过  
  git blame [file]  

### 11.git show

* 类似git log -p,但只显示一个commit,默认HEAD

### 12.git reset

* 默认,移动HEAD指针,改变暂存区内容,但不改变工作区内容  
  git reset [--mixed]
* 移动HEAD指针,不会改变工作区和暂存区内容  
  git reset --soft  
* 移动HEAD指针,改变工作区和暂存区内容  
  git reset --hard  

> 回滚文件
>
> * 修改完,还未执行 git add  
>   git checkout -- .使用暂存区的文件覆盖工作区
> * git add 添加到暂存区,还未 git commit  
>   **第一种**  
>   git reset HEAD：先用HEAD指针覆盖当前的暂存区内容
>   git checkout -- .：在用暂存区内容覆盖工作区内容  
>    **第二种**  
>   git reset --hard HEAD
> * git push后  
>   参见 git revert  
> * 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变  
>   git reset [file]  

### 13.git reflog

* 查看本地引用记录(远程无法查看)  
  git reflog  

> * git log 是显示当前的HEAD和它的祖先的,递归是沿着当前指针的父亲,父亲的父亲,……,这样的原则。  
> * git reflog 根本不遍历HEAD的祖先。它是HEAD所指向的一个顺序的提>交列表：它的undo历史。reflog并不是repo(仓库)的一部分,它单独存>储,而且不包含在pushes,fetches或者clones里面,它纯属是本地的。  
> * git reflog可以很好地帮助你恢复你误操作的数据,例如你错误地reset了一个旧的提交或者rebase,这个时候你可以使用reflog去查看在误操作之前的信息,并且使用git reset --hard 去恢复之前的状态。  

### 14.git revert

* 作用  
  git revert: 用于反转提交,执行命令时要求工作树必须是干净的,用一个新提交来消除一个历史提交所做的任何修改,revert 之后你的本地代码会回滚到指定的历史版本,这时你再 git push 既可以把线上的代码更新.(这里不会像reset造成冲突的问题)
* git reset 和 git revert 区别 

> * **第一**  
>   reset 删除指定commit以后,你git push可能导致一大堆冲突.但是revert 并不会  
> * **第二**  
>   如果在日后现有分支和历史分支需要合并的时候,reset 恢复部分的代码依然会出现在历史分支里.但是revert 方向提交的commit 并不会出现在历史分支里
> * **第三**  
>   reset 是在正常的commit历史中,删除了指定的commit,这时 HEAD 是向后移动了,而 revert 是在正常的commit历史中再commit一次,只不过是反向提交,他的 HEAD 是一直向前的

### 15.git rm

```c
rm: 直接物理删除,可通过git checkout -- .恢复
git rm: 等于rm + git add
git commit -am 'comment': 等于 git add + git commit
git rm --cached [file]:停止追踪指定文件，但该文件会保留再工作区
git mv [file-origin] [file-renamed]:修改文件名，并且将这个改名文件放入暂存区
```

### 15.git branch

* 列出本地所有分支(*代表所在分支)  
  git branch  
* 查看远程分支  
  git branch -r
* 查看所有分支  
  git branch -a
* 创建分支  
  git branch branch_name [基于分支]  
* 删除分支  
  git branch -d branch_name  
  git branch -D branch_name  
* 查看本地分支与远程分支对应关系  
  git branch -vv  
* 重命名分支  
  git branch -m oldName newName
* 删除远程分支  

>    **第一种**  
>    git branch -d -r branch_name  
>    git push origin :branch_name  
>    **第二种**  
>    git push origin --delete branch_name  
>    **第三种**  
>    git push origin -d branch_name  

* 本地分支与远程对应

> 远程有,本地没有  
> git checkout --track origin/remote_branch  
> 本地有,远程没有  
> git push --set-upstream origin local_branch  
> 本地和远程都有  
> git branch --set-upstream local_branch origin/remote_branch  
>
> * 选择一个commit，合并进当前分支  
>   git cherry-pick [commit]  

### 16.git checkout

* 切换分支  
  git checkout branch_name  
* 切换到上一个分支  
  git checkout -
* 创建并切换分支  
  git checkout -b branch_name
* 回退暂存区文件到工作区  
  git checkout -- file  
* 恢复暂存区的指定文件到工作区  
  git checkout [file]  
* 恢复某个commit的指定文件到暂存区和工作区  
  git checkout [commit] [file]  
* 恢复暂存区所有文件到工作区  
  git checkout .  

### 17.git merge

* 合并分支会同步到工作区、暂存区和本地仓库,不会同步到远程仓库  
* 带merge记录(信息更全) ,删除分支后,分支的commit记录会合并在一起  
  git merge --no-ff
* 不带merge记录[默认],删除分支后,分支的commit记录会合并在一起  
  git merge --ff
* 不带merge记录,也不带commit记录,合并到暂存区,需要一次额外的commit 才能完成最终合并  
  git merge --squash  

### 18.git rebase

> **场景一**  
> 本地与远端同意分支提交历史不一致
>
> * 修改方式一  
>   git pull(通过git log --oneline --graph查看,发现commit分叉了)  
>   通过git rebase解决(通过git log --oneline --graph查看,发现没有分叉了)  
>   通过git push 推送到远端  
> * 修改方式二  
>   git pull --rebase  
>   git push  
>
> **场景二**  
> 不同分支之间的合并  
>
> * 例子  
>   *git checkout -b branchname*  
>   *git rebase master ：在branchname分支上,以master为基础,将branchname分支上的修改增加到master分支上,并生成新的版本*  
>   *如果失败,修改冲突*
>   *git add .*  
>   *git rebase --continue(通过git log --oneline --graph查看,发现没有分叉了)*  
>   *git checkout master：切换回master*  
>   *git merge  branchname(通过git log --oneline --graph查看,发现没有分叉了)*

> **rebase、squash和merge区别**  
> git rebase: 可以尽可能保持 master 分支干净整洁,并且易于识别 author  
> git merge --squash: 也可以保持 master 分支干净,但是 master 中 author 都是 maintainer,而不是原 owner  
> git merge [--ff]:  不能保持 master 分支干净,但是保持了所有的 commit history,大多数情况下都是不好的,个别情况挺好  

### 19.git clone

* 通过https协议  
  git clone https://github.com/wanters/git-cmd-operation.git  
* 通过ssh协议  
  git clone git@github.com:wanters/git-cmd-operation.git  

### 20.git remote

* 列出远程分支  
  git remote
* 列出远程分支url  
  git remote -v  
* 为本地仓库添加远程库  
  git remote add origin url  
* 修改本地仓库对应的远程仓库地址(应用在工程迁移过程中)  
  git remote set-url origin url  
* 查看本地分支与远程分支的对应关系(比git branch -vv 更详细)  
  git remote show origin 
* 移除远程仓库(只是移除本地的远程仓库连接,不会删除远程端的仓库)  
  git remote rm repo_name  
* 变更远程仓库分支(将远程头部切换origin到分支develop)  
  git remote set-head <远程主机名> <远程分支名>  
  git remote set-head origin develop

### 21.git push

* 格式  
  git push <远程仓库名/主机名> <本地分支> [远程分支]  
* 将本地master分支推送到origin主机的远程分支  
  git push origin master  
* 删除远程分支  
  git push origin --delete master
* 将当前分支推送到origin主机的对应分支(存在追踪关系)  
  git push origin  
* 当前分支只有一个远程分支,可忽略主机名和分支名  
  git push  
* 与多个主机(远程仓库)存在追踪关系,使用-u指定默认主机  
  git push -u origin master  

> **git push不带参数推送有两种方式**  
>
> * simple方式,默认只推送当前分支  
>   git config --global push.default simple
> * matching方式,会推送所有有对应的远程分支的本地分支  
>   git config --global push.default matching
> * 本地的所有分支都推送到远程主机(无论是否存在对应的远程分支)  
>   git push --all origin
> * 强推到远程(慎用)  
>   git push --force origin  master  
>   git push -f  origin master
>
> ### 22.git pull  
>
> **git pull 和 git fetch区别**  
>  git fetch: 是仅仅获取远程仓库的更新内容,并不会自动做合并。  
> git pull: 在获取远程仓库的内容后,会自动做合并,可以看成 git fetch 之后 git merge。
>
> * 格式  
>   git pull <远程主机名> <远程分支名>:<本地分支名>  
> * 将远程主机origin的master分支拉取过来,与本地的brantest分支合并  
>   git pull origin master:local_branch  
>   **例子**
>   拉取远程仓库与本地进行合并  
> * 方式一    
>   git fetch orgin master //将远程仓库的master分支下载到本地当前branch中  
>   git log -p master  ..origin/master //比较本地的master分支和origin/master分支的差别  
>   git merge origin/master //进行合并
> * 方式二  
>   git fetch origin master:tmp //从远程仓库master分支获取最新,在本地建立tmp分支  
>   git diff tmp //将当前分支与tmp分支对比  
>   git merge tmp //合并tmp分支到当前分支
> * 方式三  
>   git pull origin master  

### 23.git tag

* 查看本地标签  
  git tag  
* 查看指定标签  
  git show tag_name  
* 删除本地标签  
  git tag -d tag_name  
* 创建标签  
  git tag -a tag_name 'commitid' -m 'comment'  
  -a: 指定标签名  
  -m: 添加comment message  
  commitid: 指定打标签的提交  
* 删除远程标签  

> **第一种**  
> git push origin :refs/tags/tag_name  
> **第二种**  
> git push origin --delete tag_name  
> **第三种**  
> git push origin :tag_name  
> *可与删除远程分支方法对比,基本一致*  
>
> * 推送标签到远程  
>   git push origin tag_name: 推送单个标签  
>   git push origin --tags: 推送所有标签  

### 24.git stash

* 保存本地仓库中的临时修改  
  git stash  
* 查看所有临时修改  
  git stash list  
* 应用某个临时修改(存储),不删除stash,默认是应用第一个  
  git stash apply stash@{$num}  
* 应用某个临时修改(存储),并删除  
  git stash pop stash@{$num}  
* 丢弃指定临时修改(存储)  
  git stash drop stash@{$num}  
* 清空所有临时修改(存储)  
  git stash clear  

### 25.git archive  

* 基本用法  

```vim
  # 作用：从代码库中导出一份干净的代码
  $ git archive --format tar.gz --output "./output.tar.gz" master
  # 将master分支打包为output.tar.gz
  # --format指明打包格式，若不指明此项，则根据--output中的文件名推断文件格式。所以你也可以将上述命令简化为
  $ git archive --output "./output.tar.gz" master
  # 使用-l参数获得支持的文件格式列表
  $ git archive -l
  # --output指明输出包名
```

* 打包不同的分支或commit

```vim
  # 打包指定分支
  $ git archive --format tar.gz --output "./output.tar.gz" branch_name
  # 打包指定commit
  $ git archive --format tar.gz --output "./output.tar.gz" commitid
```

* 打包某个目录

```
  # 如果想打包master下的dir_1 dir_2目录
  $ git archive --format tar.gz --output "./output.tar.gz" master dir_1 dir_2  
```

* 注意事项  

```
  打包建议在代码库的根目录下进行，不然会碰到各种问题
```

### 25.git ls-tree

* 查看已追踪文件  

```
   $ git ls-tree -r master --name-only
```

* 查看未追踪文件  

```
   $ git status
```

