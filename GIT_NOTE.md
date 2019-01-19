

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