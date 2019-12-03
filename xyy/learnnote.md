# 学习笔记（一）
----
### 2019.12.2
## git的基本操作
----
### git基本的命令
```
cd                      //转换目录
pwd                     //显示当前目录
git init                //将目录变成Git可以管理的仓库
(ps:生成的.git目录用来跟踪管理版本库，默认隐藏，用ls -ah可查看)
```
### 文件添加到版本库
```
git add <file>                     //文件添加到仓库
git commit -m "本次提交说明"        //文件提交到仓库
```
### 版本回退
```
git status                      //查看仓库当前状态
git diff <file>                 //查看修改内容
git log                         //查看提交历史，确定回退到哪个版本
git log --pretty=oneline        //简单显示历史记录（ps:有commit id版本号）
HEAD                            //当前版本
HEAD^                           //上一版本
HEAD^^                          //上上版本
HEAD~100                        //往上100个版本
git reset --hard commit-id      //版本间穿梭
cat <file>                      //查看文件内容
git reflog                      //查看命令历史，确定回到未来哪个版本
```
### 工作区和暂存区
* 工作区（working directory）
电脑中能看到的目录，如learngit文件夹
* 版本库（repository）
工作区中隐藏目录.git，是git的版本库
git版本库里有stage（index）暂存区和git为我们自动创建的第一个分支master，以及指向master的第一个指针head
* git add将文件提交到暂存区
* git commit将暂存区的所有内容提交到当前分支
---
以上是我最近一段时间的学习内容，主要是参照[廖雪峰的git教程](https://www.liaoxuefeng.com/wiki/896043488029600)进行学习，并简单了解了一下markdown文件的基本编写语法。