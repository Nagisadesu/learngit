## Nagisa的Git学习笔记

### 1.Git版本库创建

> + `git init`
> + 添加文件到Git仓库，分两步：
>
> > 1. 使用命令`git add <file>` 可以反复使用，添加多个文件
> > 2. 使用命令`git commit -m <msg>`，完成

### 2.时光穿梭机

> 1. 版本回退
>
> > - `HEAD`指向的版本为当前版本。使用命令`git reset --hard commi_id`来回到之前的版本
> > - 使用`git log`与`git reflog`来确定回到哪个版本
>
> 2. 工作区与暂存区
>
> >- 工作区（Working Directory）就是在电脑里能看见的目录
> >- 版本库（Repository）
> >
> >> 工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库
> >>
> >> Git的版本库里最重要的式暂存区（`stage`）
> >>
> >> 前面讲了我们把文件往Git版本库里添加的时候，是分两步执行的：
> >>
> >> 第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；
> >>
> >> 第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。
>
> 3. 撤销修改
>
> >- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。
> >
> >- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
> >
> >- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考**版本回退**一节(2.1)，不过前提是没有推送到远程库。
>
> 4. 删除文件
>
> > - `rm`命令删除文件
> > - 如果要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`
> > - 另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：`git checkout -- <file>`

### 3.远程仓库

> 1. 创建SSH Key `ssh-keygen -t rsa "youremail@example.com"` `id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。
>
> 2. 第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面
>
>    然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容
>
> - 添加远程仓库
>
> > 要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`
> >
> > 关联一个远程库时必须给远程库指定一个名字，`origin`是默认习惯命名
> >
> > 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容
> >
> > 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改
>
> - 从远程库克隆
>
> > 要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。

### 4.分支管理

> 1. 创建于合并分支
>
> > - 查看分支：`git branch`
> >
> > - 创建分支：`git branch <name>`
> >
> > - 切换分支：`git checkout <name>`或者`git switch <name>`
> >
> > - 创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`
> >
> > * 合并某分支到当前分支：`git merge <name>`
> >
> > - 删除分支：`git branch -d <name>`
>
> 2. 解决冲突
>
> > 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
> >
> > 解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。
> >
> > 用`git log --graph`命令可以看到分支合并图。
>
> 3. 分支管理策略
>
> > 合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。
>
> 4. Bug分支
>
> > - 修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
> >
> > - 当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；
> >
> > - 在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick <commit_id>`命令，把bug提交的修改“复制”到当前分支，避免重复劳动。
>
> 5. 多人协作
>
> > - 查看远程库信息，使用`git remote -v`；
> > - 本地新建的分支如果不推送到远程，对其他人就是不可见的；
> > - 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
> > - 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
> > - 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
> > - 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

### 5.标签管理

> 1. 创建标签
>
> > - 命令`git tag <tagname> [commit_id]`用于新建一个标签，默认为`HEAD`，也可以指定一个commit id；
> > - 命令`git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
> > - 命令`git tag`可以查看所有标签。
>
> 2. 管理标签
>
> > - 命令`git push origin <tagname>`可以推送一个本地标签；
> > - 命令`git push origin --tags`可以推送全部未推送过的本地标签；
> > - 命令`git tag -d <tagname>`可以删除一个本地标签；
> > - 命令`git push origin :refs/tags/<tagname>`可以删除一个远程标签。

### 自定义Git

> 配置别名
>
> > `git config --global alias.<xx> CommandName`
> >
> > 每个仓库的Git配置文件都放在`.git/config`文件中

_____

### 总结

​	[Git Cheat Sheet](https://liaoxuefeng.gitee.io/resource.liaoxuefeng.com/git/git-cheat-sheet.pdf)

*****

##### 致谢

+ 以上所有Git学习内容总结全是来自于廖雪峰老师的网站，对此抱有万分的感激之情
+ [Git教程](https://www.liaoxuefeng.com/wiki/896043488029600 "廖雪峰的Git教程")



​	