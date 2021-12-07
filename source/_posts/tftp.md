---
title: TFTP与脚本
date: 2021/12/07 21:00:00
update: 2021/12/07 21:00:00
tags:
- linux
categories: Tool
excerpt: 我可不想机械地重复一些工作，所以tftp命令需要进行自定义。
---


# TFTP与脚本

# 起因

很多的嵌入式开发板使用uboot升级系统的时候会使用tftp协议，需要在PC端使用tftp命令把文件传给开发板命令如下：

```bash
tftp IP地址 -m binary -c put 文件
```

但是PC端的tftp命令并不好用，它只能传输当前路径的文件，也就是说命令运行的路径要和实际传输的文件路径是一样的。所以每次我都是这样操作

```bash
cd /path/to/binary_file
tftp IP地址 -m binary -c put 文件
cd -
```

费劲！

# 改进

在多次繁琐的使用过程之后我发现我大部分时间只用tftp的这一种功能，其他的功能几乎用不上，所以编写了一个bash脚本简化整个过程，如下：

```bash
#!/bin/bash

# tf.sh

#获取要传输文件的文件名
bin_name=`basename ${2}`

#获取文件的真实路径
bin_path=`realpath ${2}`

#获取文件所在的文件夹
bin_dir=`dirname ${bin_path}`

#获取当前命令执行的文件夹
pwd_dir=`pwd`

#判断当前文件夹与文件所在的文件夹是不是同一个
if [ "${bin_dir}" == "${pwd_dir}" ];then
  # 如果是同一个文件夹那么就传输文件
  # 因为我常用的传输模式就是这样的，所以就参数写成固定的
  tftp ${1} -m binary -c put ${2}
else
  # 如果不在同一个文件夹就使用pushd进入到文件所在的文件夹
  pushd ${bin_dir} > /dev/null
  # 传输文件
  tftp ${1} -m binary -c put ${bin_name}
  # 下面的这一步可以不用做
  popd > /dev/null
fi
```

把文件改名为tf，添加可执行权限然后放到PATH的目录下面，后面要传输文件给开发板就容易了：

```bash
tf IP地址 文件路径
```

省去了cd命令和tftp的参数，效率不就上去了。

# 新的问题

后来公司的网络结构做了变化，之前编译代码的工作机有双网卡，即可以访问OA网段又可以访问开发板所在的测试网段。变化之后工作机拆除了双网卡，只保留了访问OA网段的网卡，但是可以通过一个服务器访问测试网段。所以在工作机使用之前的命令传输文件就不太可能了，但是这样把工作机的文件传输给开发板就更麻烦了。这就引发了一个问题：把工作机的文件发给开发板需要几步？

1. scp 文件到服务器
2. ssh登录到服务器
3. 执行tftp

但是我感觉可以缩减到一步。

- tftp命令可以可以替换为之前的tf命令，那么整个过程就是scp → ssh → tf
- 整个过程可以写为一个脚本

脚本如下

```bash
#!/bin/bash

set -e

# 获取开发板的IP地址
ip_addr=${1}
# 获取要传输的文件
tf_file=${2}

# 使用rsync把文件放到服务器
echo -e "\033[32m Copy file to nfs ... \033[0m"
rsync -avu ${tf_file}  user@host:/path/to/${bin_file} > /dev/null

# 获取要传输的文件名
bin_file=`basename ${2}`

# 在服务器上面执行tf命令
echo -e "\033[32m Do tftp ... \033[0m"
ssh user@host "/path/to/tf ${ip_addr} /path/to/${bin_file}"
```

问题完美解决！

# END
