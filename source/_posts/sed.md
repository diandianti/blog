---
title: SED编辑器使用教程
date: 2021/9/7 21:00:00
update: 2021/9/7 21:00:00
tags: 
 - linux
 - 编辑器
categories: linux
excerpt: sed编辑器使用的简单介绍，以例子为主。
---


# 简介

sed编辑器被称为*流编辑器* （stream editor）， 区别于普通的交互式文本编辑器，sed可以很快地在命令行中完成对于文本的修改。sed的工作流程如下

- 从输入中读取入一行数据
- 根据命令来匹配数据
- 根据命令来修改数据
- 将新的数据输出到STDOUT
- 重复步骤一，直到数据流结束。

# 命令格式

```bash
sed option script file
输出流  |  sed option script
```

sed主要的可用选项有：

- "-e" 将新的命令添加到已有的命令中
- "-f"  在文件中读取命令添加到已有的命令中
- "-i"  直接修改原文件， 而不是将结果输出的STDOUT（这个选项要慎用，如果不确定命令会造成什么后果就尽量不要使用）
- "-n" 数据不输出到STDOUT，依靠脚本中的print命令来完成输出

# 使用方式

## 替换字符串

### 命令格式

替换字符串的命令格式为

```bash
sed -e 's/要匹配的文本/用来替换的文本/[替换的选项]' 文件名 #[]内表示可选内容
```

### 例子

现在有一个文本文件， 内容如下：

```bash
〉 cat data1.txt
This is first line!
This is second line!
This is third line!
```

现在把它里面的单词“is”替换为“are”， 如果使用sed的 's' 命令可以按照如下方式书写命令：

```bash
〉 sed -e 's/is/are/' data1.txt
Thare is first line!
Thare is second line!
Thare is third line!
```

但是得到的结果并不是期望的结果，这个因为**sed默认只会替换第一个匹配的文本**，如果要替换全部的文本需要使用命令:

```bash
〉 sed -e 's/is/are/g' data1.txt
Thare are first line!
Thare are second line!
Thare are third line!
```

但是这样还是不满足要求，因为sed将“This”中的"is"也替换掉了，所以对于目前的情况，只让sed替换掉第二个找到“is”才行，命令如下：

```bash
〉 sed -e 's/is/are/2' data1.txt
This are first line!
This are second line!
This are third line!
```

但是很多时候，我们并不知道要替换的文本到底在第几个位置，这个时候可以使用正则表达式来进一步明确我们要匹配的目标。

```bash
〉 sed -e 's/\bis\b/are/g' data1.txt  #\bis\b 表示在is的周围只能为空白字符
This are first line!
This are second line!
This are third line!
```

如果要替换指定行的文本数据，例如：替换刚才文本文件第二行到最后一行的中的单词"is"为"are"，那么命令可以这么写：

```bash
〉 sed -e '2,$ s/\bis\b/are/g' data1.txt
This is first line!
This are second line!
This are third line!
```

这条命令中 **"$"代表输入文本的最后一行，类似于正则表达式中的"$"**。

但是更多的时候，我们并不知道我要替换的文本在第几行，也不知道文本会出现在什么位置。例如，我要将含有"second"的行中的"line"改成"lion"， 那么命令可以这么写：

```bash
〉 sed -e '/second/s/\bline\b/lion/g' data1.txt
This is first line!
This is second lion!
This is third line!
```

## 删除行

### 命令格式

删除文本行的命令格式为

```bash
sed 'd' 文本文件
```

### 例子

现在有一个文本文件：

```bash
〉 cat data1.txt
This is first line!
This is second line!
This is third line!
```

如果我想删除里面的第一行，那么命令可以这么写：

```bash
〉 sed '2d' data1.txt
This is first line!
This is third line!
```

删除第二到最后一行的命令如下：

```bash
〉 sed '2,$d' data1.txt
This is first line!
```

删除包含单词"second"的一行：

```bash
sed '/second/d' data1.txt
This is first line!
This is third line!
```

删除包含单词"second"与"third"之间的所有行：

```bash
〉cat data2.txt
This is first line!
This is second line!
This is third line!
test line
This is 5 line!
test line
This is 6 line!
test line
This is 7 line!
test line
This is third line!
This is second line!
end line

〉 sed '/second/,/third/d' data2.txt
This is first line!
test line
This is 5 line!
test line
This is 6 line!
test line
This is 7 line!
test line
This is third line!
```

但是对于这条命令来说，sed不仅删除了要删除的命令，而且把最后的两行也删除掉了，这是因为sed在寻找到了单词"second"之后就将后面的都进行删除动作，但是到文本的最后都没有找到"third"，就将"second"之后的行都删除了。所以**慎用这种命令**。

## 插入文本

### 命令格式

插入文本可以使用两种方式来实现，一种是"i"命令（insert），另一种是"a"命令（append）

```bash
sed '[行号]i\插入内容' 文件名
sed '[行号]a\附加内容' 文件名
```

### 例子

有如下的文本数据：

```bash
〉 cat data1.txt
This is first line!
This is second line!
This is third line!
```

要在第二行之前插入一行新内容，命令如下：

```bash
〉 sed '2i\This is insert line!' data1.txt
This is first line!
This is insert line!
This is second line!
This is third line!
```

在最后一行后面附加一行新内容， 命令如下：

```bash
〉 sed '$a\This is append line!' data1.txt
This is first line!
This is second line!
This is third line!
This is append line!
```

在每一行后面附加一行新内容，命令如下：

```bash
〉 sed 'a\This is append line!' data1.txt
This is first line!
This is append line!
This is second line!
This is append line!
This is third line!
This is append line!
```

还是之前的问题，大多数时候并不知道要在第几行后面添加新的信息，例如，我要在含有单词"second"的行后面添加一行新的内容，那么命令可以这么写：

```bash
〉 sed '/second/a\This is append line!' data1.txt
This is first line!
This is second line!
This is append line!
This is third line!
```

## 修改整行文本

### 命令格式

```bash
sed '[行号]c\新内容' 文本文件
```

### 例子

将文本文件的第二行改为新的内容，命令如下：

```bash
〉 sed '2c\This is new line' data1.txt
This is first line!
This is new line
This is third line!
```

将含有单词"second"的行改为一行新的内容， 命令如下：

```bash
sed '/second/c\This is new line' data1.txt
This is first line!
This is new line
This is third line!
```

## 转换单个字符

### 命令格式

```bash
sed 'y/要转换字符/用来替换的字符' 文本文件
```

### 例子

有如下的文本文件：

```bash
〉 cat data3.txt
This is line 1!
This is line 2!
This is line 3!
```

分别将"1" "2"  "3" 替换为 "4" "5" "6"，命令为：

```bash
〉 sed 'y/123/456/' data3.txt
This is line 4!
This is line 5!
This is line 6!
```

### 转换命令与替换命令的区别

- 转换命令只能替换单个字符，而替换命令可以替换整个字符串
- 转换命令是一个全局命令，默认会转换所有的指定字符，而替换命令默认只会替换第一个

## 读写文件

sed的处理文件的过程中可以从其他的文件中读取数据，并且可以把处理过的行输出的文件中。

### 命令格式

```bash
sed '[行号] {w,r} 操作文件名' 文本文件名
```

### 例子

对于如下的两个文本文件：

```bash
〉 cat data1.txt
This is first line!
This is second line!
This is third line!
〉 cat data3.txt
This is line 1!
This is line 2!
This is line 3!
```

将文件"data3.txt"插入到文件"data1.txt"的第一行之后：

```bash
〉sed '1r data3.txt' data1.txt
This is first line!
This is line 1!
This is line 2!
This is line 3!
This is second line!
This is third line!
```

将文件"data3.txt"插入到文件"data1.txt"的含有单词"second"的一行之后：

```bash
〉sed '/second/r data3.txt' data1.txt
This is first line!
This is second line!
This is line 1!
This is line 2!
This is line 3!
This is third line!
```

将文件"data1.txt"中的单词"line"替换为"lion"并将结果写入到文件"output.txt"中：

```bash
〉sed -ne 's/\bline\b/lion/g;w output.txt'  data1.txt
〉cat output.txt
This is first lion!
This is second lion!
This is third lion!
```

## 打印命令

### 命令格式

打印命令有三种"p"  "=" "l(小写的L)"， 命令格式如下：

```bash
sed -n "p" 文本文件 #p命令可以打印处理的整行 -n参数可以忽略但是加上之后打印会更清晰
sed -n "=" 文本文件 #=可以打印行号
sed -n "l" 文本文件 #l可以打印出不可见的字符，以转义字符或者八进制码来显示
```

### 例子

```bash
〉 sed -n 'p;=;l' data3.txt
This is line 1!#p命令打印出整行
1#=命令打印出行号
This is line 1! $ #l命令打印出不可见字符
This is line 2!
2
This is line 2!$
This is line 3!
3
This is line 3!$
```

## 命令混用与命令文件

对于复杂的文本文件，不只是一条命令就可以完成所有的操作，所以sed的所有命令都是可以混用的，只要在不同的命令之间添加";"作为分隔符即可。当然还可以把所有的命令写成一个单独的命令文件，在使用的时候调用。例如：

```bash
〉cat data1.txt
This is first line!
This is second line!
This is third line!
〉cat script.sed
s/\bis\b/are/g
2i\This is a new line!
3d
〉sed -f script.sed data1.txt
This are first line!
This is a new line!
This are second line!
```

# 总结

本文简要说明了sed的使用方式，如果需要更加详细的使用方式可以使用命令"man sed"来查看详细的使用说明。需要着重所说的一点是，如果你不知道自己的命令会产生什么效果不要在原文件上面进行操作，复制一份进行操作会更加安全。
