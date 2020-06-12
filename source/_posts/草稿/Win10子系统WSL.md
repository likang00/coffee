---
title: Win10子系统WSL
date: 2018-12-31 22:41:05
tags: WSL
---

# WSL

介绍一下WIN10自带的一个非常强大的功能 -- **子系统** 
以及安装使用流程以及一些小坑，还有使用 **图形用户界面** 打开子系统中的Pycharm

![](wsl01.jpg)

``` shell
简单步骤速览：
1.启动开发者服务
(设置->更新->开发人员模式)
2.控制面板启用linux子系统
(控制面板->程序->启动或关闭Windows功能)
3.安装ubuntu(16.04/18.04)
(WIN10商店搜索下载)
4.设置用户名密码
5.安装python3.6(并指定版本)
6.安装pip(并指定版本)
7.安装虚拟环境及管理虚拟环境
```

重点说一下安装Python以及虚拟环境
安装python3.6

导入第三方软件库
sudo add-apt-repository ppa:jonathonf/python-3.6
更新软件源并安装
sudo apt-get update
sudo apt-get install python3.6

将默认的Python 链接指向Python3.6
sudo ln -s python3.6 /usr/bin/python
sudo rm python (如果有原本的python链接，需要把原本存在的Python链接删去，重新建立软链接至Python 3.6)

安装pip3.6
curl https://bootstrap.pypa.io/get-pip.py | sudo python3.6

安装virtualenv及virtualenvwrapper

sudo pip install virtualenv
sudo pip install virtualenvwrapper

添加环境变量，当前路径创建virtualenv文件夹

mkdir $HOME/.virtualenvs

执行命令，打开~/.bashrc

vim ~/.bashrc
``` vim
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
# (注意virtualenvwrapper.sh路径 可以whereis virtualenvwrapper.sh 寻找)
source /usr/local/bin/virtualenvwrapper.sh (ubuntu16.04)
source ~/.local/bin/virtualwrapper.sh(ubuntu18.04)
```
------------------------------

## ps：

1.创建.virtualenvs文件夹时不要使用sudo，会导致后续没有权限写入文件

删除非空目录：rm -rf /***
删除空目录 rmkdir /***

2.ubuntu16/18virtualenvwrapper.sh路径不同，见上文

3.可能会用到的安装

``` shell
# no module named "apt_pkg"
sudo find / -name "apt_pkg.cpython-35m-x86_64-linux-gnu.so"
cd /usr/lib/python3/dist-packages/
sudo cp apt_pkg.cpython-35m-x86_64-linux-gnu.so apt_pkg.cpython-36m-x86_64-linux-gnu.so
```

4.如果版本python环境混乱可能导致virtualenv找不到应该使用的python版本 这时修改virtualenvwrapper.sh文件

``` shell
sudo vim virtualenvwrapper.sh
if [ "$VIRTUALENVWRAPPER_PYTHON" = "" ] then
VIRTUALENVWRAPPER_PYTHON="$(command \which python3)"
fi
```

5.参考网址 感谢

ubuntu16.04上virtualenv和virtualenvwrapper安装及使用
玩转ubuntu18.04之virtualenv和virtualenvwrapper安装与使用

------------------------------

改天介绍如何安装XLaunch可视化界面和Ubuntu中的Pycharm

![](wsl02.png)

1.安装Xming 
2.安装完直接打开Xming即可
3.安装一个firefox测试
apt-get install firefox
4.运行(在程序指令前加上"DISPLAY=:0 ")
DISPLAY=:0 firefox
5.简化配置
每次运行程序都要输入DISPLAY=:0挺累的，执行下列指令后重启bash（输入bash，即可重启）即可省去这个步骤
echo "export DISPLAY=:0.0" >> ~/.bashrc
6.输入firefox，即可产生界面


 
 
 