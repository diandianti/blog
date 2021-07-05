---
title: linux发行版使用感受
date: 2021/7/05 20:00:00
tags: linux
categories: 总结
excerpt:  本文介绍了我使用不同linux发行版的感受，评价更加偏向于主观，仅供参考。
---

# ubuntu

- ubuntu是我最早接触的linux系统，当然也是目前最有知名度的linux发行版之一；
- **简单易用，安装方便，开箱即用**；
- 软件包较多，很多的软件都会有ubuntu专门的配适；
- 但是ubuntu也有或多或少的bug，但是一般搜索一下都有相应的解决方法；
- 不能说缺点，我用的很不爽的地方：软件策略偏于保守，在编译一些软件的时候一些底层的库比如libc.so， 版本很旧，会造成一些问题；除此之外gnome桌面取消了托盘程序的停靠，很多托盘停靠的程序就不能最小化了。
- 官网：https://ubuntu.com/

# MINT

- mint 是基于ubuntu的一个linux发行版，最大的特点就是**简单易用**；
- 桌面环境和win7很像，适合刚从window迁移过来的linux初学者；
- 因为mint基于ubuntu，所以很多问题搜索的时候只需要套用ubuntu的答案就可以；
- 官网： https://linuxmint.com/

# Arch

- Arch邪教，最大的特色应该使用AUR软件源，数量庞大，是所有的linux发行版中**软件包数目最多的**；
- 高度自定义，从安装系统到桌面环境都是需要什么安装什么；
- 但是安装过程不是GUI界面，对于初学者有一定的难度，但是Arch系的发行版有宜于安装的版本： https://wiki.archlinux.org/title/Arch-based_distributions_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

# OpenSUSE

- 最大的特色就是所有的linux配置均提供了相应的GUI界面， 官网介绍是这样的：“**YaST 最好的、唯一的综合性 Linux 系统配置和安装工具。**”
- 对于像我这样不咋记配置命令的人很是受用。

# Deepin

- 深度操作系统最大的特点就是**DDE**，真的很好看，虽然有些果里果气；
- 我没有将deepin作为过生产环境，所以关于兼容性等方面就不多说了；

# 总结

Arch真的很和我的心意，全部的过程都是我自己决定的，但是Arch真的很容易在滚动更新的时候滚挂了，所以我只在我的年老体弱的笔记本上安装了arch。在生产环境中我使用的是Mint+i3, mint开箱即用，加上好用的i3，仅需要一些简单的配置就可以快速使用。当然你如果不用i3等wm就是直接就可以使用。Deepin的DDE真的不错，但是看久了有些审美疲劳。

以上就是全部的体验感受。