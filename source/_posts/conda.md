---
title: Linux平台Conda使用指南
date: 2021/11/23 21:00:00
update: 2021/11/23 21:00:00
tags:
- linux
categories: Tool
excerpt: Conda 可不只是python环境管理工具。
---

# 什么是Conda

Conda 是一个开源的跨平台的软件包与开发环境管理系统。虽然它是为python程序创建的，但是这并不影响它为其他的语言提供环境支持。Conda官网[https://www.anaconda.com/](https://www.anaconda.com/)

本文主要分为三个部分：Conda的下载与安装；使用conda管理和安装python环境；使用conda安装其他系统软件。第三部分除了介绍如何使用conda安装其他系统软件和说明了为什么要这么做。

# 如何安装Conda

## 下载

可以在官网下载Conda，但是在国内的话下载速度一般会比较慢。可以使用国内的镜像源，这个链接是TUNA的源 [https://mirrors.tuna.tsinghua.edu.cn/anaconda/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/)。

可以在网站上找到两个Conda的两个版本，分别是Anaconda与Miniconda。这两个版本在环境与软件包管理上没有差异，Anaconda有一个GUI的环境管理工具，更加适合不喜欢终端命令的用户。

## 安装

对于linux平台下使用更加推荐使用Miniconda，下载Miniconda的安装文件之后就可以通过如下的命令来安装：

```bash
chmod +x Miniconda-latest-Linux-x86_64.sh
./Miniconda-latest-Linux-x86_64.sh

Welcome to Miniconda2 4.0.5 (by Continuum Analytics, Inc.)

In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
>>>
```

在出现提示之后一路回车就可以了，不过有一个问题需要注意,最后安装程序会问是否将Conda写入到PATH变量中，要选择yes，不然安装之后还是无法调用conda。不过即使你没有选择yes将变量写入，后续也有相应的补救措施。

## 配置

有的时候Conda安装之后并不能成功的将相关的配置信息写入到shell的配置文件中，这个时候就需要手动来更新一下shell的配置文件，命令如下:

```bash
cd /path/to/conda/install/path/bin #进入到conda的安装目录下面的bin文件夹
./conda init
```

执行之后退出重新进入到shell之后就可以看到在终端的提示符前面多了一点东西，如下：

```bash
(base) ➜  ~
```

# 如何使用Conda管理python环境与软件包

## python环境管理

### 基础环境

Conda在安装之后默认会生成一个名为"base"的基础的python环境，并且每次打开终端默认就是使用这个环境, 可以通过如下的命令来查看目前conda已经创建的环境

```bash
conda env list
# conda environments:
#
base                  *  /home/test/miniconda3 #这个环境是默认创建的
```

### 如果确定当前的环境名

#### 查看shell前面的提示符

在执行conda init命令之后会修改系统中存在的所有shell的配置文件，会在sheel的提示符前面加上环境的名字例如我使用的zsh效果如下：

```bash
(base) ➜  ~
```

#### 使用conda env命令

可以使用如下的命令来确定当前使用的环境名

```bash
(base) ➜  ~ conda env list
# conda environments:
#
base                  *  /home/test/miniconda3 # * 表示目前就在这个环境下
```

#### 使用which命令

通过"which python"命令可以间接的获取目前环境的名称：

```bash
(base) ➜  ~ which python
/home/test/miniconda3/bin/python #这个路径说明是在base环境下面


(py27) ➜  ~ which python
/home/test/miniconda3/envs/py27/bin/python #当前在名为py27的环境下，这个环境存放的路径是/home/test/miniconda3/envs/py27
```


### 创建新的环境

因为我安装的是Miniconda3，所以默认的环境python版本就是python3，此时如果要创建一个新的环境可以通过如下的命令：

```bash
(base) ➜  ~ conda create -n py27 python=2.7 #-n用来指定环境的名字，python=2.7用来指定安装python的版本。
```

在创建命令执行完成之后使用命令查看目前所有的环境：

```bash
(base) ➜  ~ conda env list
# conda environments:
#
base                  *  /home/changqing_xie/miniconda3
py27                     /home/changqing_xie/miniconda3/envs/py27
```

### 切换环境

创建了新的环境，可以使用如下命令来切换当前的环境：

```bash
(base) ➜  ~ conda activate py27
(py27) ➜  ~ conda env list #此时shell的提示符已经变了
# conda environments:
#
base                     /home/changqing_xie/miniconda3
py27                  *  /home/changqing_xie/miniconda3/envs/py27 #通过conda env命令查看是否切换成功
```

如果要回到base环境，可以执行命令：

```base
conda deactivate
# or
conda activate base
```

### 删除环境

如果要删除一个名为nouse的环境,那么可以使用如下的命令
```bash
(base) ➜  ~ conda env remove -n nouse #使用-n来指定环境的名字

Remove all packages in environment /home/changqing_xie/miniconda3/envs/nouse:

```


## python 软件包管理

### 安装pip包

```bash
(base) ➜  ~ conda install numpy #安装一个名为numpy的pip包
(base) ➜  ~ pip list | grep numpy #使用pip查看是否安装成功
numpy                  1.21.4
```

### 卸载pip包

```bash
(base) ➜  ~ conda uninstall numpy
```

# 如何用Conda管理除python外的其他软件包 & WHY?

Conda官网的介绍说conda不只可以应用的python的环境管理中，还可以用的其他的软件中。我认为这个功能是conda最实用的一个。因为大多数的时候我都是使用的服务器工作并且我没有root权限，大多数的软件安装都需要自己编译或者下载别人编译好的可执行文件，效率远没有使用apt，dnf等工具来的快。Conda可以完美的解决这一问题。

## 搜索软件

- 可以直接在 [https://anaconda.org/search](https://anaconda.org/search) 网站上面搜索，这种方式简单方便，而且可以搜索到conda可以提供的所有的软件包。
- 如果你有比较信任的conda源，也可以将它添加到你的conda配置里面，或者使用命令来搜索 "conda search -c conda-forge zsh", 这条命令的-c参数就是指定了在某个特定的频道里面搜索，conda的默认频道不包含这一个

个人建议网站搜索会好很多，毕竟不可能将所有的conda频道都加入到conda的配置里面，而且网站搜索之后还会告诉你安装的命令，省去了自己敲命令的部分。

## 软件安装

- 如果是在网站上面搜索到的软件包，在软件的详情界面就有安装的命令， 例如我搜索zsh会给出以下的安装方式, 选择一个安装即可。

```bash
conda install -c conda-forge zsh
conda install -c conda-forge/label/gcc7 zsh
conda install -c conda-forge/label/cf201901 zsh
conda install -c conda-forge/label/cf202003 zsh
```

- 另一种方式是直接在终端输入"conda install -c conda-forge zsh",如果你信任的这个频道有这个软件就会直接安装，如果没有请在网站上面搜索一下。


# END



