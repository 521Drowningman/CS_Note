# git

git commit   提交，类似于复制整个文件夹，但是提交时代码库不会盲目复制，它会比较和以前提交的不同之处

git branch <分支名>  用于创建新的分支

git checkout <分支名>  用于切换分支到想要的分支上,指向该分支

git merge   <分支名>     当在某分支上开发新功能完成后，想要将其合并到主分支上时就要使用该行命令,执行改行命令是将<分支名> 合并到当前的分支上。

git rebase <分支名> 是另外一个将两个分支合并的方法，该方法会更线性一点

^ 是向上移动

~ 一次后退很多步

git branch -f <分支名> <后退> 让<分支名>强制指向后退的那个提交



撤销

本地分支用 git reset <位置>  进行撤销,位置指要撤销到哪一个父提交

远程分支用 git revert 进行撤销



## 远程仓库

git clone 从远程仓库中克隆一个远程仓库的副本

![image-20240117113153279](../../../AppData/Roaming/Typora/typora-user-images/image-20240117113153279.png)

git fetch  从远程仓库中获取数据，单纯的下载操作，不会改变本地仓库的状态

> + 完成了从远程仓库下载本地仓库中缺失的提交记录
> + 更新远程分支指针
> + 实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态

git pull  实际上是git fetch和git merge的缩写

git push 将自己的变更上传到指定的远程仓库，并在远程仓库上合并你的新提交记录，提交后远程仓库的状态会进行更新

git push –rebase   是fetch和rebase 的简写



## 新创建库之后如何推送代码

> + `git init`   进行本地git的初始化



> ```bash
> git remote add origin 远程仓库的URL
> ```
>
> 将本地仓库和远程仓库关联起来

查看关联的远程仓库的URL

```
git remote -v
```

添加

```
git add .
```

提交

``` 
git commit -m "首次提交项目
```

推送到远程仓库

```
git push -u origin master
git push <远程主机名> <本地分支名>:<远程分支名>
```

```bash
# 查看当前仓库是否有该分支
git branch -a
# 或者查看远程分支：
git branch -r
 
# 切换到本地的main分支
git checkout main

# 与远程origin/main同步   先同步再推送
git pull origin main

删除远程分支
git push origin --delete [branch_name]

删除本地分支
 git branch -d 本地分支名
 
 git pull origin main --allow-unrelated-histories
```

![image-20240806102632982](../../../AppData/Roaming/Typora/typora-user-images/image-20240806102632982.png)

````bash
git config   #可以用来配置信息,如用户名,邮件信息
git init     #进行初始化,告诉git这个文件需要版本控制
git status   #查看处于操作的那个状态
git add      #将工作区的文件添加到缓存区
git commit   #将缓存区的文件提交到本地仓库
git log      #查看项目版本
.gitignore   #将不想提交的文件的名字写入该文件中后,就可以避免某些文件被提交
git merge xx #将其他分支合并到现在所处的分支上来.
git remote add 远程仓库的别名 地址  #适用于先有本地仓库,而远程仓库是后来所建造
git branch -m xx  #将当前的分支名修改为xx
git remote -v #查看有关联的远程仓库
git config user.name  #可以查看当前git配置的用户名
git config user.email #可以查看当前git配置的邮箱
git config --global user.name 用户名 #进行用户名配置
git config --global user.email 邮箱 #进行邮箱配置
````

