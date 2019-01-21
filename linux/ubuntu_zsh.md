
# ubuntu 安装Oh-my-zsh

```
sudo apt install zsh
# 切换到zsh
chsh -s /bin/zsh 
```

```
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```
安装插件highlight，高亮语法
```
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```
在Oh-my-zsh的配置文件中~/.zshrc中添加插件
```
plugins=( [plugins...] zsh-syntax-highlighting)
```
最后使配置生效
```
source ~/.zshrc
```
这里需要重新登入一下，否则zsh只会在当前终端中应用。