# Git

## Git是什么

Git是一个`分布式版本控制`软件

`分布式版本控制`：是一种版本控制的方式，它允许软件开发者可以共同参与一个软件开发专案，但是不必在共同的网络系统下工作。不需要服务器端软件就可以运作版本控制。

## 数据结构

![](/assets/Git_operations.svg.png)

Git中的数据流和存储级别





## Git常用命令

```
git init //创建Git仓库
git status //查看当前所在分支、内容更改和提交状态
```

`添加（Adding）`
添加更改到Staging Area，此时改动该没有提交到仓库。提交到仓库之前，改动还可以从Stage中移除，可以指定文件，也可以使用通配符*指定一个类型的文件，或者直接
```
git add octocat.txt     #添加单个文件
git add '*.txt'           #添加一类文件
git add .               #添加所有文件 
```



`提交（Committing）`

```
git commit -m "修改记录" //提交修改，并记录修改内容
```

`历史（History）`

```
git log #打印提交日志
```

`远程仓库（Remote Repositories）`

```
git remote add origin 远程仓库地址
```

`推送（Pushing Remotely）`
推送本地修改到远程仓库
```
git push -u origin master
```


`拉取（Pulling Remotely）`
检查远程仓库的修改并拉取
```
git pull origin master
```

`差异（Differences）`


```
git diff HEAD     #
git diff --staged #
```

`重设Stage（Resetting the Stage）`
把文件从Stage中移除
```
git reset 文件名 #
```









### 分支操作

```
git status //查看当前所在分支和更改内容

```



将文件或文件夹移除版本控制

```
git rm -r -n --cached "bin/"     //-n：加上这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的bin/文件夹下的文件列表预览。
git rm -r --cached  "bin/"     //最终执行命令. 删除bin/文件夹
git commit -m "remove bin folder all file out of control"    //提交
git push    //提交到远程服务器
```



