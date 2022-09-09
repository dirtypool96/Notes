## Git

### 时光穿梭机

#### 命令

1. `git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，`readme.txt`被修改过了，但还没有准备提交的修改。

2. 记不清上次怎么修改的`readme.txt`，可以用`git diff`这个命令看看：

3. `git log`命令显示从最近到最远的提交日志，加上`--pretty=oneline`参数可以直接看版本号（从新到旧往下排）

4. `git reset --hard HEAD^`返回上一版本

   `git reset --hard 1094a`指定回到版本（包括之前），版本号没必要写全，前几位就可以了，Git会自动去找

5. `git reflog` 查看命令历史，以便确定要回到未来的哪个版本

6. `git diff filename`:比较工作区和暂存区

   `git diff HEAD -- filename`:比较工作区和版本库的最新版本

7. `git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销（包括删除），这里有两种情况：

   一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

   一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

#### 小结

1. 回退场景

- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD `，就回到了场景1，第二步按场景1操作。

- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，不过前提是没有推送到远程库。

### 分支管理

1. Git鼓励大量使用分支：

   查看分支：`git branch`

   创建分支：`git branch `

   切换分支：`git checkout `或者`git switch `

   创建+切换分支：`git checkout -b `或者`git switch -c `

   合并某分支到当前分支：`git merge `+某分支名

   删除分支：`git branch -d `

2. 分支冲突：文件存在冲突，必须手动解决冲突后再提交。`git status`查看冲突的文件，修改过后再提交

3. `Fast forward`模式下，删除分支后，会丢掉分支信息，如果禁用FF模式则：

   ```
   git merge --no-ff -m "merge with no-ff" dev
   //本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去
   ```

4. 团队和做master同来发布新版本，dev干活，个人在dev的分支上干活合并到dev

   ![分支策略](res/0.png)

#### BUG分支

`git stash`可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作

1. 假定需要在`master`分支上修复，就从`master`创建临时分支：

   `git checkout master`后

   `git checkout -b issue-101`

2. 修复完成后，加入暂存区，提交，切换至master，合并，返回dev。

   ```
   $ git add readme.txt 
   $ git commit -m "fix bug 101"
   $ git switch master
   $ git merge --no-ff -m "merged bug fix 101" issue-101
   $ git switch dev
   ```

3. `git stash list` 查看刚才的工作现场

   如果想要恢复：

   1. `git stash apply`恢复，`git stash drop`来删除
   2. `git stash pop`，恢复的同时把stash内容也删了：

4. `git cherry-pick `：在master分支上修复的bug，想要合并到当前dev分支（用的id为master中提交的issue-101的id）

5. 丢弃一个没有被合并过的分支，可以通过`git branch -D `强行删除。

#### 多人协作

1.  `git checkout -b dev origin/dev `：克隆后在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地

2. 如果恰好出现修改的内容已被别人修改并push后，先**链接（本地已有dev分支，没有的话先pull就不用链接了），pull，解决冲突，合并**：

   ```
   git branch --set-upstream-to=origin/dev dev
   git pull
   git commit -m "fix env conflict"
   git push origin dev
   ```

> 多人协作的工作模式通常是这样：
>
> 1. 首先，可以试图用`git push origin `推送自己的修改；
> 2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
> 3. 如果合并有冲突，则解决冲突，并在本地提交；
> 4. 没有冲突或者解决掉冲突后，再用`git push origin `推送就能成功！
>
> 如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to=origin/<branch-name> <branch-name> `。

#### Rebase

- rebase操作可以把本地未push的分叉提交历史整理成直线；
- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

步骤：

1. dev分支开发完毕后推送远程；

2. 切换master分支拉取远程；

3. 切换dev分支rebase到master分支