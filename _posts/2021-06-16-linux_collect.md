---
title: linux 工具
categories: [linux]
comments: true
---

# linux 工具

# Shell

## zsh

- 简单易用
- 大量的资源
- debian 安装方式 apt install zsh

### oh-my-zsh

- zsh的配置脚本，一键配置，有大量的主题和插件，适合linux初学者，但是开启较多插件之后会导致zsh卡顿
- [https://ohmyz.sh/](https://ohmyz.sh/)。

### zinit

- 同样是zsh的配置脚本，知名度不如ohmyzsh，但是速度比ohmyzsh快许多
- [https://github.com/zdharma/zinit](https://github.com/zdharma/zinit)

### powerlevel10k

- zsh的一个主题， 审美在线，采用异步方式加载，可以在有限范围内自定义。
- [https://github.com/romkatv/powerlevel10k](https://github.com/romkatv/powerlevel10k)

## Fish

- 比zsh还易用, 原名friendly interactive shell
- 也有较多的资源支持，但是数目不及zsh
- 官网 [https://fishshell.com/](https://fishshell.com/)
- **oh-my-fish**  fish的配置脚本 [https://github.com/oh-my-fish/oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)
- 不少操作和bash相差甚大，linux初学者慎用
- debian 安装方式 apt install fish

# Shell 软件

## Tmux

- 著名的 终端分屏工具，但是不止终端分屏
- 非官方但是写的很好的教程   [http://louiszhai.github.io/2017/09/30/tmux/](http://louiszhai.github.io/2017/09/30/tmux/)
- 一个比较好用的配置工具   [https://github.com/gpakosz/.tmux](https://github.com/gpakosz/.tmux)
- debian 安装方式 apt install tmux

## Ranger

- 终端文件管理器，高效实用
- 相关文档   [https://wiki.archlinux.org/title/Ranger_(简体中文)](https://wiki.archlinux.org/title/Ranger_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
- debian 安装方式 apt install ranger
- 一张截图
![pics/ranger.png](https://github.com/diandianti/linux_collection/raw/main/pics/ranger.png)


## Fzf

- 模糊搜索软件
- 完成配置之后可以 ctrl+r 可以搜索历史命令 ctrl + t 搜索文件，也可以通过插件配置到vim中
- [https://github.com/junegunn/fzf](https://github.com/junegunn/fzf)

## Fd

- find 的缩写，名字比find简单，用法也是
- rust实现
- 可以配合fzf使用
- [https://github.com/sharkdp/fd](https://github.com/sharkdp/fd)

## Tig

- git 工具 简单好用
- 不用记忆太多的git命令
- [https://github.com/jonas/tig](https://github.com/jonas/tig)

## Ag

- Ag 是类似ack， grep的工具, 它来在文件中搜索相应关键字
- 它比ack还要快 （和grep不在一个数量级上）
- [https://geoff.greer.fm/ag/](https://geoff.greer.fm/ag/)

# 终端模拟器

## Alacritty

- 快速 跨平台 好看
- /[https://github.com/alacritty/alacritty](https://github.com/alacritty/alacritty)

## Terminator

- 自带分屏
- 简单好用
- debian 安装

    ```bash
    apt install terminator
    ```

# 图形界面

## WM

区别于DE（desktop environment）冗杂的环境和多年用不上的自带软件包，WM（Window Manger）只有一个窗口管理器，渲染器，锁屏，登录界面等都需要自行配置。但是WM更加轻量化，占用资源更少常见的WM有i3  dwm

### i3-wm

我先在正在用的wm，简单快速，可自定义

官网 [https://i3wm.org/](https://i3wm.org/)

附图一张

![pics/i3.png](https://github.com/diandianti/linux_collection/raw/main/pics/i3.png)

## dwm

- 完全自定义，修改相应的头文件定义
- 需要自己从源码安装，但是源码十分简单（相比于其他的wm）
- 有大量的修改patch可以使用
- 缺点：过于硬核，非常不建议初学者使用
- [https://dwm.suckless.org/](https://dwm.suckless.org/)

## 小工具

### polybar

- 使用WM的时候默认的状态栏是不怎么好看的，polybar便是一个简单实用好看的软件
- [https://github.com/polybar/polybar](https://github.com/polybar/polybar) 官方项目
- [https://github.com/adi1090x/polybar-themes](https://github.com/adi1090x/polybar-themes)  polybar 主题

### rofi

- WM默认使用dmenu作为程序启动器，但是dmenu ...
- rofi是一个好看可高度自定义的软件，可以作为启动器，开关机菜单等你想要的功能
- [https://github.com/davatorium/rofi](https://github.com/davatorium/rofi)

### feh

- 一款快速的图像浏览器
- 可以给WM来换壁纸

    ```jsx
    feh --bg-fill --randomize ~/.wallpaper/*
    ```
