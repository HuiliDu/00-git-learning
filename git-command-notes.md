# Git学习笔记

## Git的四个区域

本地：
工作区：当前磁盘上的某个被git跟踪记录的文件夹
暂存区：记录该文件夹内文件的删除、更改以及创建等记录
仓库区：保存并记录暂存区的更改，每一次更改会产生一个哈希值来存放本次更改所做的操作
远程：
仓库区：与本地仓库同步，便于不同设备、不同开发人员共同参与仓库的维护

## Git常见命令及用法

`git add <file1 file2 ...>` 将file1 file2等文件的更改提交到暂存区

`git commit -m "message notes"` 将暂存区的更改提交到本地仓库，并写上注释记录本次更改主要做了哪些改动

`git branch bugfix` 创建bugfix分支，当前仍停留在main分支

`git checkout main/bugfix/C1` 切换到main分支/bugfix分支/C1这个提交

`git checkout -b bugfix` 创建bugfix分支并切换到bugfix分支

`git switch main/bugfix` 切换到main/bugfix分支，仅用于分支之间的切换，不能用来切换到某一次commit(git checkout可以)

`git branch -f bugfix C2` 将bugfix的分支指针指向C2这个提交，且当前不能处于bugfix分支上

`git checkout HEAD^/main^/bugfix^` 向回移动一下HEAD指针，此时HEAD会与分支处于分离状态

`git checkout HEAD~3` 向回移动3下HEAD指针

`git reset` 撤销本地仓库的更改，--soft将更改撤销到暂存区，--mixed 将更改撤销到工作区，--hard 完全删除更改

`git revert` 消除远程仓库的更改，不能直接reset消除，但可以新建一个和历史某次提交后一样的状态来抵消之前的更改

`git merge bugfix` 在bugfix上完成新分支的修改后，切换回main分支，然后执行该命令，可以将bugfix的更改合并到main分支上，且会创建一个新的commit记录此次合并操作

`git rebase main` 在bugfix上完成新分支的修改后，执行该命令，会将bugfix分支上的修改复制到main分支上，然后切换回main分支，再执行`git rebase bugfix`，将main分支的HEAD指针与bugfix的最新提交对齐

`git rebase main bugfix` 等价于 `git checkout bugfix` + `git rebase main` 两步合并为一步

`git cherry-pick C2 C3` 将C2 C3两次修改的内容复制到当前分支上

`git rebase -i` 在命令行窗口显示可被复制操作的历史提交记录以及对应的哈希值，方便根据需要进行cherry-pick

`git restore <file1 file2 ...>` 彻底丢弃暂存区里file1 file2等文件的更改

`git restore --staged <file1 file2 ...> 将file1 file2等文件的更改从暂存区移动到工作区，并未完全彻底丢弃

`git commit --amend` 修改最新的一次提交，例如message

`git tag v1.0.0` 将当前HEAD所在的提交标记为v1.0.0

`git fetch` 抓取远程的main分支相较于上一次提交后的本地main分支的更改，并将这些更改在本地保存到origin/main分支上，本地的main分支仍保持原样，并为合并远程的更改

`git pull` 抓取远程分支上的更新并合并到本地分支，功能上相当于`git fetch`+`git merge origin/main`

`git pull --rebase` 抓取远程分支上的更新并合并到本地分支，功能上相当于`git fetch`+`git rebase origin/main`

`git push` 将本地的更改同步到远程

## 举个例子

将一个远程仓库repo克隆到本地，然后在本地新建一个feature分支并在该分支上做了一些更改，于此同时，其他人在远程仓库上提交了一些新的更改。此时如果想把本地feature分支上的更改推送到远程仓库，应该怎么办？

方法一：
`git checkout main` 切换到本地main分支
`git pull` 抓取远程仓库的最新更改并合并到本地main分支
`git merge feature` 将feature分支的更改合并到本地main分支
`git push` 将最新的本地main分支推送到远程的main分支

方法二：
`git fetch` 抓取远程仓库main分支上的更改，复制到本地仓库，本地的origin/main分支会复制一份最新的更改
`git rebase origin/main feature` 将origin/main上的更改复制到feature分支上
`git rebase feature main` 将feature分支上的所有更改复制到本地main分支上
`git push` 将最新的本地main分支推送到远程的main分支

以上两种方法在功能上是等价的，都是先将远程的更新同步到本地，然后把本地的feature分支上的更改合并到main分支，然后再把main分支推送到远程。但是方法一会产生合并的过程示意轨迹，明确知道合并的过程，方法二是线性合并，视觉上更清爽简洁，但是会丢失合并的过程。
