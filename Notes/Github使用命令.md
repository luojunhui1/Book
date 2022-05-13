# github常用命令
## 壹 基础命令

`git init` 初始化仓库

`git status` 查看仓库状态

<!--more-->

`git add` 将文件提交到暂存区

`git rm –cached <file>...` 将文件从缓存区移除

`git commit` 将文件提交到仓库

`git log` 查看所有产生的commit记录

`git branch` 查看当前分支情况

**`git checkout` 切换到分支/版本/提交，撤销还没有 add 进暂存区的文件**

`git checkout -b <branch name>` 创建并切换到该分支

`git merge` 分支合并

`git branch -d <branch name>`  删除分支

`git branch -D <branch name>`  强制删除分支

`git tag <tag name>`  创建代码版本标签

**`git checkout <tag name>` 切换到标签版本代码**

**`git remote add <remote repository name> <remote repository address>`  本地仓库关联到远程仓库**

**`git push <remote repository name> <remote repository branch>` 将代码推送到远程仓库**

`git remote -v` 查看项目远程仓库

**`git config --blobal <config attribute>`  git的各种设置**

- `git config --blobal user.name `  设置用户名

- `git config --blobal user.email `  设置用户邮箱

- `git config --blobal alias.<abreviation> <operation name> `  设置操作缩写

  常用设置：

  ```
  git config --global alias.co checkout # 别名
  git config --global alias.ci commit
  git config --global alias.st status
  git config --global alias.br branch
  git config --global alias.psm 'push origin master'
  git config --global alias.plm 'pull origin master'
  git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%
  d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
  git config --global color.ui true
  git config --global core.quotepath false # 设置显示中文文件名
  ```

`git config -l` 列出git各设置

**`git diff <$id1> <$id2>`  比较两次提交之间的差异**

**`git diff <branch1>..<branch2>`  在两个分支之间比较**

`git diff --staged`  比较暂存区和版本库差异

**`git stash`  将当前分支所有没有commit的代码先暂存起来**

`git stash apply` 将当前分支的暂存代码还原

`git stash drop <stash_id>`  将当前分支的stash记录删除,不加<stash_id>删除上次记录

**`git stash pop`  将代码还原并删除stash记录**

`git stash clear `  清空stash记录

**`git merge <branch name>` 合并分支到当前分支（可知合并后代码源）**

**`git rebase<branch name>` 合并分支到当前分支（不可知合并后代码源）**

## 贰 分支操作

- 新建一个叫 develop 的分支

  `git branch develop`

- 切换到 develop 分支
  `git checkout develop`

- 如果把以上两步合并，即新建并且自动切换到 develop 分支：
  `git checkout -b develop`

- 把 develop 分支推送到远程仓库
  `git push origin develop`

- 如果你远程的分支想取名叫 develop2 ，那执行以下代码：
  `git push origin develop:develop2`

- 查看本地分支列表
  `git branch`

- 查看远程分支列表
  `git branch -r`

- 删除本地分支
  `git branch -d develop`
  `git branch -D develop (强制删除)`

- 删除远程分支
  `git push origin :develop`

- 如果远程分支有个 develop ，而本地没有，你想把远程的 develop 分支迁到本地：
  `git checkout develop origin/develop`

- 同样的把远程分支迁到本地顺便切换到该分支：
  `git checkout -b develop origin/develop`

## 叁 团队合作

一般而言，大部分项目会有两个分支master及develop，它们的职责分别是：

- master：永远处在即将发布(production-ready)状态
- develop：最新的开发状态

确切的说 master、develop 分支大部分情况下都会保持一致，只有在上线前的测试阶段develop 比 master 的代码要多，一旦测试没问题，准备发布了，这时候会将 develop 合并到master 上。

但在`GitFlow`上，除了这两个分支，还有以下辅助分支：

- feature: 开发新功能的分支, 基于 develop, 完成后 merge 回 develop
- release: 准备要发布版本的分支, 用来修复 bug，基于 develop，完成后 merge 回
  develop 和 master
- hotfix: 修复 master 上的问题, 等不及 release 版本就必须马上上线. 基于 master, 完成后
  merge 回 master 和 develop

具体的操作过程，关于每个分支间的关系有着一套完整的操作流程，可以参考以下链接：

[Git Flow 的正确使用姿势 - 简书 (jianshu.com)](https://www.jianshu.com/p/41910dc6ef29)

下图说明了几个分支间的派生和合并关系，非常经典：

![](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture/Windows/20210904203305.png)