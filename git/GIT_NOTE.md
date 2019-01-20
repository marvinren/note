

# git入门

## git安装和配置
具体的git安装可以在官网上下载安装
```
git config --global list
git config --local list
git config --system list //几乎不用
```

一般要配置用户名和email
```
git config --global user.name <username>
git config --global user.email <email>
```

## git的初始化
一般git初始化有两种
```
cd project_path
git init
```
还有一种方式
```
git init project_path
cd project_path
```

## 提交代码：git的提交
git要注意有暂存区，然后要从暂存区提交
```
git add <filename>
git commit -m "<commit message>"
```

PS：
```
git mv <source_file>  <target_file>
git rm <filename>
```

## 查看git的日志：git的log&gitk
如下是一个常用的名称：
```
git log --oneline -n4 --graph --all
```
PS:
```
git branch -av //查看所有的分支 
git reset -hard //可以吧暂存区的代码回退到未添加到暂存区
git checkout -b <branch_name> //创建分支并指向该分支
git checkout <branch_name> //切换分支
```

### gitk是一个图形化的界面展示

* 首先要分清界面内容
* Patch&Tree的差别
* Author&Committer的差别
* 定制界面
* tag操作


## 解密git：理解.git目录

* HEAD：文件表明指针指向的分支
* config：配置信息（local）
* refs目录：表达了分支和tag的指针指向
* objects目录：保存具体的文件
PS:
```
git cat-file -t <commit-id> //type:commit tree blob tag
git cat-file -p <commit-id> //content
```

### 理解commit,tree&blob

略

```

### 理解commit,tree&blob

略
PS：
* 注意“分离头指针" detached head

## HEAD指针

HEAD是指向branch或者commit，指向commit的时候是分离头指针，但本质上指向branch也本质上指向了一个commit
HEAD可以用来替代一个commit，比方说`HEAD`  `HEAD^1` `HEAD~1`

PS:
```
git diff HEAD HEAD^
```

# git使用的常用场景
## 删除不需要的分支
```
gitk --all
git branch -d <branch_name>
git branche -D <branch_name> //强势删除
```

## 修改最近一次提交&之前的提交的message
```
git commit --amend
```

```
git rebase -i <commit_id>
```

PS: 这些是未提交到远程仓储里的提交的变更

## 合并多次提交
```
git rebase -s <commit_id>
git rebase --contiune //跨commit融合可能用这个
```

## 进行比较

### 暂存区和head进行比较
```
git diff --cached
```

### 工作区和暂存区的比较
```
git diff
git diff -- <filename>
```
