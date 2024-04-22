---
title: 'Linux服务器conda环境配置'
date: 2023-11-13
permalink: /posts/research-journal-2023-11-13-linux-conda
tags:
  - research journal
---

Linux服务器conda环境配置

### 1.MobaXterm软件，创建账户，登录

Session->SSH->Basic SSH settings，填写服务器地址，用户名和服务器端口号，进入自己的操作页面。

```
remote host: 10.18.2.3
Usr: sqx
port: 6001
Pw: 123456
```

### 2.下载anaconda3最新版，上传到服务器，进行安装。

[下载地址](https://repo.anaconda.com/archive/)

安装脚本：

```sh
bash Anaconda3-2020.11-Linux-x86_64.sh
```

安装过程中，最后有选项，请选择将路径加入到bash中。

安装完毕后，测试，是否安装成功。

```sh
conda info -e，
```

看是否正常显示‘base’环境。

如果显示错误，vi ~/.basharc文件，将conda路径加进去。

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img1.png)

conda成功后，就进行虚拟环境创建

```sh
conda create -n <envs_name> python==3.8(或其他版本，自己选择)
```

会选择Y/N安装，选择Y，创建成功。

然后，启动刚才创建的虚拟环境，以envs_name为mag 为例，启动命令为：

```sh
conda activate mag
```

之后所有的操作都在此虚拟环境中，注意观察前缀是否是(mag)。

将代码上传到服务器，进入代码目录[MagNav](https://github.com/Naatyu/MagNav/tree/main)

替换下载源设置清华源

```sh
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

安装好所有的环境库

```sh
pip install -r requirements.txt
```

### 3.Pycharm调试程序

Pycharm文件：

```sh
"/home/disk/sqx/software/Pycharm_Professional_2021.2.1_Protable.zip"
```

pycharm打开项目目录文件夹

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img2.png)

File->settings->python interpreter->[setting]->add

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img3.png)

ssh interpreter里面输入自己的服务器地址，端口和用户名，然后确认，下一步输入密码。

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img4.png)

在右侧Remote host里选择home/sqx/anaconda3/envs/mag/bin/python作为解释器；

在sync folders中选择本地文件夹到服务器文件夹的映射，最好两个文件夹名字一样，本地的编辑会直接更新到服务器上。

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img5.png)

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img6.png)

tools->deployment->configuration...

选择默认，并测试连接；

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img7.png)

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img8.png)

tools->deployment->options...设置上传快捷键Ctrl+S

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img9.png)

### 4.Linux服务器搭建Jupyter notebook

安装jupyter notebook

```sh
conda install jupyter
```

生成jupyter配置文件

```sh
jupyter notebook --generate-config
```

修改jupyter配置文件

```sh
 vim /home/sqx/.jupyter/jupyter_notebook_config.py

c.NotebookApp.ip = '*'
c.NotebookApp.notebook_dir = '/mnt/data2/pfg/jupyter_notebook'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 8888
c.NotebookApp.quit_button = False
c.NotebookApp.allow_root = False
```

打开jupyter

```sh
# cd /mnt/data2/sqx/MagNav/notebooks # 到工作目录下
jupyter notebook
```

![img](http://sunqinxuan.github.io/images/posts-research-journal-2023-11-13-img10.png)



