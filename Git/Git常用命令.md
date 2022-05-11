### git 基本命令

- git help 

  帮助命令 如：git help clone 或 git clone --help
-  git remote
   本地仓库关联远程仓库
```
提交到远程仓库
$ git remote add origin https://gitee.com/BlusFlowerPants/canaless.git
$ git push -u origin master
```
- git clone


  克隆一个仓库到当前目录下

- git init

  创建一个空git仓库

- git add

  将文件改动添加到索引中
  
  git add -A 所有文件

- git commit

  把文件改动记录到本地仓库
  
  git commit -m '简写'

- git push

  推送本地改动记录到远程仓库
  
  git push origin master
  
  origin:远程仓库名称，master:远程仓库的分支名称

- git pull

  拉取远程仓库最新记录并合并到当前分支
  
  git pull origin master

- git log

  查看提交日志

- git branch

  列出、创建、删除分支
  
  列出分支：git branch 或列出所有分支包括远程 git branch -a
  
  创建分支：git branch 分支名
  
  删除分支 git branch -D 分支名

- git fetch

  拉取远程仓库更新记录，--all 拉取所有分支的更新记录

- git rebase
  
  变基 git rebase origin/master或git rebase 分支名

- git merge

  合并分支：git merge origin/master或git merge 分支名
  
- git checkout

  分支切换或还原工作树文件
 
-  git checkout -b 新分支名 
    创建一个新分支并切换到此分支上
- git reset --hard 06128a2365f5bb9f5fbc3fc3c613a5d232a7357f 
    然后再强推至远程
- git push origin HEAD --force
- git push origin --delete dev