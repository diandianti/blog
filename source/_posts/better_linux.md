---
title: 让linux桌面版更好用
date: 2021/12/23 21:00:00
update: 2021/12/23 21:00:00
tags:
- linux
categories: 总结
excerpt: 感觉linux发行版可以更好用一点。
---


# 前言

生活不易，何必让一些无所谓的事情为难自己。

由于开发和爱好的原因，我主要使用的操作系统都是linux，所以linux发行版的好用程度直接决定了我的工作效率和心情，为了提升自己的使用体验我也做了大量的努力，下面就是我个人使用的经验。

整篇文章分为了三部分：GUI软件 、终端软件、个人建议。

# GUI软件

大多数人都要有一个带有图形界面的系统，不然你也不会看到这篇文章。在介绍正式的内容之前先说明一下linux的桌面环境构成，绝大多数的linux发行版在安装的时候都是安装一个桌面环境（Desktop Environment，简称为DE），比较出名的有KDE、DDE。

一个DE包含以下的几部分：窗口管理器WM，渲染器，一系列的小工具（比如终端模拟器，计算器，截图工具，壁纸工具）。大多数的桌面环境使用的窗口管理器都是层叠式的（就是像windows那样的窗口呈现方式），这样的窗口管理方式使用快捷键操作会比较费手，所以会有较多的人使用平铺式的窗口管理（类似于tmux的终端分割方式）。

## 平铺式窗口管理器

平铺式窗口管理器的优点是所有的窗口控制逻辑可以通过键盘来实现，包括但不限于窗口大小调整，窗口位置移动，窗口最大化，窗口隐藏。当所有的操作都由键盘来实现时可以减少伸手去够鼠标时造成的操作割裂感，效率不一定提升多少，但是心理满足感提升了不少。

### i3WM

- 优点：成熟，基本无bug，不想自己配置可以找别人现成的配置文件，这个网址 [http://dotshare.it/category/wms/i3/](http://dotshare.it/category/wms/i3/)
- 缺点：上手难度高，如果没有足够好的动手能力不建议尝试。
- 效果图（网上找的别人的， 这个网址： [http://dotshare.it/category/wms/i3/](http://dotshare.it/category/wms/i3/)）
    
    ![http://dotshare.it/public/images/uploads/8287.png](https://s6.jpg.cm/2021/12/23/LbFfG5.png)
    
    ![http://dotshare.it/public/images/uploads/8456.png](https://s6.jpg.cm/2021/12/23/LbFgXC.png)
    

- 官网，里面有介绍如何安装。  [i3 - improved tiling wm](https://i3wm.org/)
    

### DWM

- 优点：高度自定义，如果没有你想要的功能可以自己写
- 缺点：复杂，甚至配置文件都是头文件的形式，要自己编译，没两把刷子真的不行
- 效果图，也是刚才的网站找的：
    
    ![http://dotshare.it/public/images/uploads/8460.png](https://s6.jpg.cm/2021/12/23/LbFzvt.png)
    
- 官网：   [dwm - dynamic window manager | suckless.org software that sucks less](https://dwm.suckless.org/)
    

## 桌面渲染器

就是一个渲染器 picom，支持窗口阴影，窗口透明，高斯模糊，窗口动画，github上面还可以找到支持窗口圆角的版本，下面是官方项目地址：

[GitHub - yshui/picom: A lightweight compositor for X11](https://github.com/yshui/picom)

## 状态栏

如果自行安装窗口管理器，默认使用的是WM自带的状态栏，如果想要一个自定义的好看的状态栏，那么可以试试polybar，官方库：

[GitHub - polybar/polybar: A fast and easy-to-use status bar](https://github.com/polybar/polybar)

如果你嫌弃默认的polybar不够好看可以使用别人的配置文件对polybar进行修改，比如这个项目：

[GitHub - adi1090x/polybar-themes: A huge collection of polybar themes with different styles, colors and variants.](https://github.com/adi1090x/polybar-themes)

效果图一张

![https://raw.githubusercontent.com/adi1090x/files/master/polybar-themes/previews/shapes/md9.gif](https://raw.githubusercontent.com/adi1090x/files/master/polybar-themes/previews/shapes/md9.gif)

## 不仅是启动器的ROFI

在win10系统上面用过powertoy 或者 用过KDE的krunner的小伙伴可能更习惯使用alt+space来快速查找启动一个软件。Rofi便是一个类似的软件，但是rofi并不限于作为一个启动器，你可以通过编写脚本的方式用Rofi实现一个文件浏览器，音乐控制器等等。官方项目：

[GitHub - davatorium/rofi: Rofi: A window switcher, application launcher and dmenu replacement](https://github.com/davatorium/rofi)

如果你没有特别的想法，可以参考这个项目，里面有很多的rofi脚本，或许可以给你灵感

[GitHub - adi1090x/rofi: A large collection of Rofi based custom Menu, Applets, Launchers & Powermenus.](https://github.com/adi1090x/rofi)

效果图若干：

![https://raw.githubusercontent.com/adi1090x/files/master/rofi/previews/android/main.gif](https://raw.githubusercontent.com/adi1090x/files/master/rofi/previews/android/main.gif)

![https://raw.githubusercontent.com/adi1090x/files/master/rofi/previews/ribbon/main.gif](https://raw.githubusercontent.com/adi1090x/files/master/rofi/previews/ribbon/main.gif)

![https://raw.githubusercontent.com/adi1090x/files/master/rofi/previews/menu/main.gif](https://raw.githubusercontent.com/adi1090x/files/master/rofi/previews/menu/main.gif)

## 截图软件

各大DE基本上都有自带的截图软件，但是好用程度有好有坏，可以尝试一下这个软件 Flameshot，一款开源的截图软件：

![https://flameshot.org/img/flameshot-demo.gif](https://flameshot.org/img/flameshot-demo.gif)

官网：

[Flameshot](https://flameshot.org/)

## 壁纸软件

### Feh

feh其实是一个轻量化的看图软件，但是不影响它可以更换壁纸，命令如下：

```bash

feh --bg-fill --randomize ~/.wallpaper/*
```

官网：

[feh - a fast and light image viewer](https://feh.finalrewind.org/)

### Variety

一个开源的自动换壁纸的软件，可以自动下载必应、unsplash、wallhaven的壁纸。也可以自己给定关键词或者链接下载。

官方网站：

[Variety](http://peterlevi.com/variety)

## 终端模拟器

### Terminator

自带分屏，类似于tmux的终端复用方式，可以自定义按键行为，简单好用，官网：

[Terminator · software-jessies-org/jessies Wiki](https://github.com/software-jessies-org/jessies/wiki/Terminator)

如果你使用的是debian的相关发行版，可以直接apt install terminator

### Alacritty

好看，快速，跨平台，可以自定义按键行为，支持在终端内显示不同的字体，一张效果图：

![https://s3.jpg.cm/2021/09/03/Itc3iO.png](https://s3.jpg.cm/2021/09/03/Itc3iO.png)

官网：

[GitHub - alacritty/alacritty: A cross-platform, OpenGL terminal emulator.](https://github.com/alacritty/alacritty)

### Tilda

一个下拉式的终端模拟器，按下快捷键，一个终端就会在桌面的上方或者其他方向弹出，用完自动回收，适合用来查看系统的资源消耗等情况，一张效果不咋好的效果图：

![https://s6.jpg.cm/2021/12/23/LbuwE5.gif](https://s6.jpg.cm/2021/12/23/LbuwE5.gif)

官网：

[GitHub - lanoxx/tilda: A Gtk based drop down terminal for Linux and Unix](https://github.com/lanoxx/tilda)

# 终端软件

## 终端

### zsh

个人感觉比bash好用，但是部分语法和bash不兼容。zsh的优势在于相关的资源比如：

oh-my-zsh, zinit以及其他相关的插件加持。

官网： [https://www.zsh.org/](https://www.zsh.org/)

oh-my-zsh：[https://github.com/ohmyzsh/ohmyzsh](https://github.com/ohmyzsh/ohmyzsh)

zinit: [https://github.com/zdharma-continuum/zinit](https://github.com/zdharma-continuum/zinit)

新手推荐使用zsh + ohmyzsh一整套，省去了自己配置的时间，但是如果插件开多了ohmyzsh会造成zsh比较卡，所以进阶玩家可以尝试一下zinit，速度比ohmyzsh快不少。除此之外推荐一个异步加载的zsh主题：

p10k: [https://github.com/romkatv/powerlevel10k](https://github.com/romkatv/powerlevel10k)

一张效果图

![https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/prompt-styles-high-contrast.png](https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/prompt-styles-high-contrast.png)

### fish

更加人性化的终端，但是过于小众导致资源不多，而且好多软件兼容性比较差，可以尝鲜，不建议用于生产环境。

官网： [https://github.com/fish-shell/fish-shell](https://github.com/fish-shell/fish-shell)

oh-my-fish: [https://github.com/oh-my-fish/oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)

## 终端增强软件

终端增强软件不区分是bash还是zsh，都可以使用，可以提升使用终端的幸福感或者效率。其中一部分软件我是在这个项目了解到的 [https://github.com/ibraheemdev/modern-unix](https://github.com/ibraheemdev/modern-unix) ，不过其中有一部分软件实用性不高。

### fzf

模糊搜索，拯救了我记不住终端命令的脑子。可以通过配置将ctrl+r映射到fzf，可以模糊搜索历史命令，也可以将ctrl+t映射到fzf用来模糊搜索文件。另外还可以把fzf配置到vim中，在vim中模糊搜索。

一张效果图：

![https://raw.githubusercontent.com/junegunn/i/master/fzf-preview.png](https://raw.githubusercontent.com/junegunn/i/master/fzf-preview.png)

官网： [https://github.com/junegunn/fzf](https://github.com/junegunn/fzf)

### Ag

搜索文件内容，可以替代grep，但是比grep快很多，之前使用grep搜索都是“grep -rn keyword”

现在直接“ag keyword”就可以了。

效果：

![https://s6.jpg.cm/2021/12/23/LbJnXi.png](https://s6.jpg.cm/2021/12/23/LbJnXi.png)

官网： [https://geoff.greer.fm/ag/](https://geoff.greer.fm/ag/)

### ranger

终端文件管理软件，类似的还有 [nnn](https://github.com/jarun/nnn)、[lf](https://github.com/gokcehan/lf)，看效果图更能够了解他们的作用

![http://dotshare.it/public/images/uploads/438.png](https://s6.jpg.cm/2021/12/23/LbF4gr.png)

官网： [https://github.com/ranger/ranger](https://github.com/ranger/ranger)

### Tmux

终端复用工具，可以在不开多个终端模拟器的情况下实现终端的复用。可以保持ssh连接，也就意味着不需要使用nohup 命令 & 的方式来在服务器上面运行命令也可以保持命令的持续执行，效果图如下：

![https://cloud.githubusercontent.com/assets/553208/19740585/85596a5a-9bbf-11e6-8aa1-7c8d9829c008.gif](https://cloud.githubusercontent.com/assets/553208/19740585/85596a5a-9bbf-11e6-8aa1-7c8d9829c008.gif)

官网： [https://github.com/tmux/tmux](https://github.com/tmux/tmux)

好用的配置文件： [https://github.com/gpakosz/.tmux](https://github.com/gpakosz/.tmux)

### Tig

git的CLI工具，类似的有[lazygit](https://github.com/jesseduffield/lazygit)

没找到合适的图，放一张lazygit的吧：

![https://github.com/jesseduffield/lazygit/raw/assets/staging.gif](https://github.com/jesseduffield/lazygit/raw/assets/staging.gif)

官网： [https://github.com/jonas/tig](https://github.com/jonas/tig)

### Htop

代理top，更加好用，效果图：

![https://htop.dev/images/htop-2.0.png](https://htop.dev/images/htop-2.0.png)

官网： [https://htop.dev/](https://htop.dev/)

# 个人建议

## 关于系统安装

- 建议将系统安装到固态硬盘，即将“/”挂载到固态硬盘
- 将home文件夹或者其他的数据文件夹单独挂载一个分区或者硬盘，这样重装系统地时候可以避免个人文件丢失
- 增加boot分区的容量，在linux发行版使用的时候会安装不同版本的linux内核，boot分区过小（好多人只留200M）会导致一些问题
- 如果使用双系统，建议将linux的引导与windows的引导放在不同的分区，这样如果一个系统引导奔溃不会导致另一个系统也起不来

## 关于软件使用

- 如果不了解，不建议修改系统已经安装好的软件依赖库，可以使用conda或者其他软件来创建虚拟环境安装新的依赖库（真的有人会去修改libc库）
- 如果在多人使用的服务器上面，尽量不适用sudo安装软件，我的大部分软件都是安装在~/.local目录下面，如果我的帐号崩溃不会影响其他人。

## 关于终端使用

- 建议将终端提示符的最后添加回车，这样你会有一行的空间来发挥，而不是命令过长而换行
- 善用alias， 将常用的命令添加一个简短的别名，这样每次就不用输入一大长串命令，而且还不一定对
- 经常使用的文件可以使用ln创建链接，而不是往每一个用到的文件夹复制一份
- 本地工程也可以使用git记录，防止自己误删文件
