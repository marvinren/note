1. 清理残余的配置文件。
    * 在终端中执行 `sudo synaptic`，以便启动 Synaptic 包管理工具。
    * 点击 Synaptic 右下角的 Status 按钮进行切换。
    * 选择Synaptic 左边的 Not installed（residual config）即可在右边看到包含有残余配置文件的包，选择删除该包。

2. 清理下载的缓存包。
通过apt安装软件时下载的包都缓存在 /var/cache/apt/archives/ 目录中，如要清理掉这些缓存包，可以执行：
```
sudo apt-get autoclean（已卸载软件的安装包）
sudo apt-get clean（未卸载软件的安装包）
```

3. 清理系统不再需要的孤立的软件包。
```
sudo apt-get autoremove
```

4. 清理无用的语言文件。
sudo apt-get install localepurge（此软件请在看清说明的前提下谨慎使用。）
在对软件配置时，使用空格键可以选择需要保留的区域配置，其他的则会被删除。当以后在安装程序时，此工具也会自动执行，勿需再次配置。若需重新配置：
```
sudo dpkg-reconfigure localepurge
```

5. 清理无用的翻译内容。
安装trans-purge 这组小工具来清理 *.desktop、mime-database、gconf schema 中的无用翻译内容。

6. 清理孤立软件包。
sudo apt-get install gtkorphan，运行gtkorphan，第一个选项中的都可以删除。
```
sudo apt-get install gtkorphan
```

7. 删除孤立的库文件。
```
sudo apt-get install deborphan
```
sudo deborphan查看孤立（没有依赖关系）库文件。
运行下面命令删除它们：
```
sudo deborphan | xargs sudo apt-get -y remove --purge
```

8. 更新内核并删除旧内核。
* 查看系统存在的内核。
```
dpkg -l|grep linux
dpkg –get-selections|grep linux
```
* 显示当前的内核。
```
uname -a
```
* 升级内核
```
apt-get dist-upgrade
```
* 删除内核
```
sudo apt-get remove内核名
```
带有image的文件是需要删除的，一定要写全版本等字符。其他相关的文件会自动删除。
例如：
```
sudo apt-get remove –purge linux-image-2.6.24-19-generic
```
* 进入/boot/grub文件夹，查看内核和初始化镜像等文件，将较低版本的文件删除，这些文件包括
System.map， vmcoreinfo，vmlinuz，initrd.img等。
* 更新menu.list
```
sudo update-grub
```
先将menu.list备份，然后改名或删除。这样会自动重建文件。然后将原来的menu.list 中需要的东西拷贝到新的menu.list中。重建时，grub不会将其他系统的引导信息也添加进去。需要的从原来的文件中复制一下就行了。

9. 删除浏览器缓存文件。
opera firefox的缓存文件目录分别是：
~/.opera/cache4
~/.mozilla/firefox/*.default/Cache