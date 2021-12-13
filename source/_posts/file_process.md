---
title: 复制与清理数据的自动化实现
date: 2021/12/13 21:00:00
update: 2021/12/13 21:00:00
tags:
- linux
- bash
- expect
- crontab
categories: Tool
excerpt: 使用expect与crontab命令来定时自动登录开发板清理数据。
---


# 重复的工作

最近使用一块嵌入式linux开发板来采集图像数据，但是板子上面的emmc存储空间不是特别大，所以需要定期登录到开发板上面把已经采集好的数据复制下来，然后把emmc的空间清理一下，大概过程就是：

- telnet 开发板
- 使用tftp命令把数据传输到PC
- 删除开发板的文件

但是对于我这样的人，连统计文件数的命令都要写成一个简单的脚本，让我每天手动做这些是不可能的，更何况tftp不支持传输文件夹，所有的文件都要单独用命令发送，必须写一个脚本来完成这些工作。

```bash
#!/bin/bash

# c.sh 统计目录下面的文件数目
# 使用方式 c.sh 路径

file_count()
  {
    expr $(ls -l $1| wc -l) - 1
  };

file_count $1
```

# 发送文件到PC

首先要实现最重要的功能，就是批量把文件发送到PC，然后把文件清理掉，所以写了一个简单的脚本在开发板上面运行，如下

```bash
#!/bin/sh

#tftp_file.sh

funPushfile() {
    l_path=${1}
    r_path=${2}
    s_add=${3}

    for i in `ls ${l_path}`#遍历文件夹下面的所有文件
    do
        echo "tftp ${l_path}/${i} ${r_path}/${i}"
        tftp -p -l ${l_path}/${i} -r ${r_path}/${i} ${s_add}#使用tftp传输文件
        rm ${l_path}/${i}#传输完成之后删除文件
    done
}

funPushfile "/path/to/img" "/path/to/pc/tftpsrv" 192.168.2.1
```

# 自动进行telnet

为了开发板的安全我为telnet添加了密码（但是实际使用过程中也就只有我会telnet上去，而且是局域网，属实是增加了自己的工作量），所以每次需要手动输入用户名和密码，然后执行脚本，为了可以自动telnet并执行，我使用的expect来实现这一功能。

```bash
#!/usr/bin/expect

# get_img.expect

#设置连接的超时时间 -1为永不超时
set timeout -1

#使用输入脚本的第一个参数作为要登录的主机ip
set name [lindex $argv 0]

#设置用户名
set user "root"

#设置密码
set password "mima"

#使用tenlet登录目标主机
spawn telnet $name

#等待telnet程序打印 "login:"
expect "login:"
#然后输入用户名
send "$user\r"

#等待telnet程序打印"Password:"
expect "Password:"
#然后输入密码
send "$password\r"
#上面的一步之后telnet登录就成功了

#把pc上面的传输文件的脚本放到开发板上面
send "tftp -g -r tftp_file.sh 192.168.2.1\r"

#运行刚才放过来的脚本
send "sh /root/tftp_file.sh\r"

#删除刚才放过来的脚本，这一步可以不做
send "rm /root/tf.sh\r"

#退出程序
send "exit\r"

expect eof
```

现在只需要执行get_img.expect 这一个文件就可以直接把开发板上面的数据拷贝回来，方便多了，但是现在还不能自动化执行，还需要一步。

# 定时执行命令

定时执行命令需要用的crontab命令，这个命令是很多linux发行版默认就安装的，不需要额外安装，执行命令 "crontab -e"，然后终端会打开编辑器，输入一下内容：

```bash
*/30 * * * * /path/to/get_img.expect >> /tmp/get_img.log 2>&1
```

上面的文本表示每三十分钟执行一次get_img.expect这个脚本，并把错误输出与stdout数据输入到文件/tmp/get_img.log

# 总结

整个自动化复制数据的过程分为了三个部分：

- crontab的定时执行
- expect的自动化telnet登录与执行命令
- 使用bash命令批量传输数据

第二步与第三步可以合并在一起，使用expect批量传输数据，但是实现起来过于繁琐，所以将数据传输的部分单独拆分了出来，也方便后续改进脚本。

# END
