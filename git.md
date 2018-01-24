# Git

## Git是什么

Git是一个`分布式版本控制`软件

`分布式版本控制`：是一种版本控制的方式，它允许软件开发者可以共同参与一个软件开发专案，但是不必在共同的网络系统下工作。不需要服务器端软件就可以运作版本控制。

## 数据结构

![](/assets/Git_operations.svg.png)

Git中的数据流和存储级别

## Git常用命令

**创建（Init）**
```
git init //创建git仓库
```

**状态**
```
git status //查看仓库状态（当前所在分支、内容更改和提交状态）
```

**添加（Adding）**
添加更改到Staging Area，此时改动还没有提交到仓库。
提交到仓库之前，改动还可以从Stage中移除，可以指定文件，也可以使用通配符\*指定一个类型的文件

```
git add <file> #添加单个文件
git add '*.txt' #添加一类文件
git add . #添加所有文件
```

**提交（Committing）**

```
git commit -m "修改记录" //提交修改，并添加修改日志
```

**历史（History）**

```
git log #打印提交日志
```

**远程仓库（Remote Repositories）**

```
git remote add origin 远程仓库地址
```

**推送（Pushing Remotely）**
推送本地修改到远程仓库
```
git push -u origin master
git push #自动推送到当前所在分支的远程分支
```

**拉取（Pulling Remotely）**
检查远程仓库的修改并拉取

```
git pull origin master
```

**差异（Differences）**

```
git diff HEAD     #
git diff --staged #
```

**重设Stage（Resetting the Stage）**
把文件从Stage中移除

```
git reset 文件名 #
```

**回滚（Undo）**

```
git checkout -- <target> #将target文件的修改回滚到上次提交
```

**分支（Branches）**

```
git branch clean_up #本地新建名为clean_up的分支
git branch #查看本地的所有分支
git checkout clean_up #切换到clean_up分支
git checkout master #切回到master分支
```

**删除（Removing All The Things）**
git rm：移除本地文件并add文件的删除到Stage

```
git rm <要删除的文件> #删除文件
```

**合并（Merge）**


```
git merge clean_up #合并clean_up分支到master分支（在master分支下执行命令）
```

**清除（Clen）**


```
git branch -d <branch name> #删除分支
```



**移除版本控制**

将文件或文件夹移除版本控制

```
git rm -r -n --cached "bin/"     //-n：加上这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的bin/文件夹下的文件列表预览。
git rm -r --cached  "bin/"     //最终执行命令. 删除bin/文件夹
git commit -m "remove bin folder all file out of control"    //提交
git push    //提交到远程服务器
```



