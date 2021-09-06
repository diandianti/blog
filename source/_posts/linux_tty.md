---
title: Linux好玩的终端软件
date: 2021/9/6 20:00:00
update: 2021/9/6 20:00:00
tags: linux
categories: 总结
excerpt: 本文介绍了一些在bash终端中一些好玩的软件，无需图形界面即可使用。
---

# 开始
最开始使用linux的时候感觉里面的东西都很无聊，感觉这个系统基本上不能够用来娱乐。后来在使用的过程中逐渐发现了很多奇奇怪怪的软件，最开始遇到时会特别惊讶：“竟然还可以这样玩”， 不过后面就渐渐习惯了。下面就是我遇到的一些好玩的软件。

# cowsay
只听名字大概就可以猜到这个东西是用来干什么，一只牛说什么东西， 看下图：

![cowsay](https://s3.jpg.cm/2021/09/06/ItTVGO.png)

不过我感觉这个图更像一只驴， 或许你可以通过alias把它改名为“donkaysay”。除了牛之外还有其他的图形可以使用，如下：
```bash
ls /usr/share/cowsay/cows/
apt.cow        cock.cow            dragon.cow             flaming-sheep.cow  koala.cow         moofasa.cow       sheep.cow        suse.cow        unipony.cow
bud-frogs.cow  cower.cow           duck.cow               ghostbusters.cow   kosh.cow          moose.cow         skeleton.cow     three-eyes.cow  unipony-smaller.cow
bunny.cow      daemon.cow          elephant.cow           gnu.cow            luke-koala.cow    pony.cow          snowman.cow      turkey.cow      vader.cow
calvin.cow     default.cow         elephant-in-snake.cow  hellokitty.cow     mech-and-cow.cow  pony-smaller.cow  stegosaurus.cow  turtle.cow      vader-koala.cow
cheese.cow     dragon-and-cow.cow  eyes.cow               kiss.cow           milk.cow          ren.cow           stimpy.cow       tux.cow         www.cow
```
对于debian系发行版可以使用这个命令来安装：
```bash
apt-get install cowsay
```
# lolcat
这个是cat的彩虹屁版本，可以配合cowsay来使用，有莫名的喜感：

![lolcat](https://s3.jpg.cm/2021/09/06/ItTHnr.png)

github页面 https://github.com/busyloop/lolcat     或者可以使用snap命令来安装：
```bash
sudo snap install lolcat
```

# nyancat 
这个和lolcat用异曲同工之妙，输入之后会有一只彩虹猫出现在你的终端屏幕上。不过除此之外这个命令就没有别的功能了，获取可以把它设为屏幕保护图像？

![nyancat](https://s3.jpg.cm/2021/09/06/ItTrwz.gif)

github页面 https://github.com/klange/nyancat     或者对于debian系发行版可以使用这个命令来安装：
```bash
apt-get install nyancat
```

# tty-clock
一个平平无奇的终端数字时钟软件，或者也可以用来当作屏幕保护图案？
```bash
tty-clock -s -D -c -C4 #后面的参数都不是必须的
```
![tty-lock](https://s3.jpg.cm/2021/09/06/ItTsxu.png)

对于debian系发行版可以使用这个命令来安装：
```bash
apt-get install tty-lock
```

#  sl
输入之后便会有一辆火车在你的终端里面开过，不过我在使用终端的时候经常会把ls打错变成sl，所以这个软件玩过几次之后便卸载了。
```bash
sl
```
![sl](https://s3.jpg.cm/2021/09/06/ItTkE4.png)

可以使用这个命令来安装：
```bash
apt-get install sl
```

# nsnake
一听名字就知道这个软件是一条蛇，但是什么蛇呢？ 贪吃蛇。 这个是一款终端类的游戏，可以在无聊的时候玩一局放松一下身心。

```bash
nsnake
```
![nsanke](https://s3.jpg.cm/2021/09/06/ItTvnD.png)

可以使用这个命令来安装：
```bash
apt-get install nsnake
```
# END
未完待续，后续有好玩的软件会继续补充。
