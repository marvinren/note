

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

## git的提交
git要注意有暂存区，然后要从暂存区提交
```
git add <filename>
git commit -m "<commit message>"
```

## git的log&gitk
如下是一个常用的名称：
```
git log --oneline -n4 --graph --all
```
PS:
```
git branch -ae //查看所有的分支 
git reset -hard //可以吧暂存区的代码回退到未添加到暂存区
git checkout -b <branch_name> //创建分支并指向该分支
```

