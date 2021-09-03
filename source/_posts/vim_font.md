---
title: VIM中将注释设置为斜体
date: 2021/9/3 21:00:00
tags:
- vim
- linux
categories: linux
excerpt: 人闲着的时候总想找点事干。
---

# 起因
有一天在写代码的时候突然就感觉vim的语法高亮一点都不明显，所有的文字都是一个字体很难分辨出那个是那个（其实就是写代码写烦了）。随即我就想到了Source Insight这个软件，它可以使用不同大小的字体来显示函数名、变量名等元素。但是 这个软件是收费的，但是我的钱包也不咋富裕，所以只能自力更生了。

 <img src="https://s3.jpg.cm/2021/09/03/ItccXC.png" style="zoom:67%;" />
    Source Insight界面（网上找的图）
<img src="https://s3.jpg.cm/2021/09/03/ItXCC4.png" style="zoom:67%;" />
    vim界面（我的）

# 如何实现
我搜索了一下发现终端软件好像没有可以支持不同大小字体的，所以最多就只能在vim中显示不同的字体了，大小就甭想调整了。所以整个过程就分为了以下几步：
 - 找一个支持不同字体的终端软件，比如 alacritty；
 - 找一些好用的编程字体， 比如 Nred Font系列；
 - 更改终端与vim的配置文件；

对于alacritty来说，配置文件很长，但是支持不同字体最重要的部分是：

```yaml
font:
  # Normal (roman) font face
  normal:
    # Font family
    #
    # Default:
    #   - (macOS) Menlo
    #   - (Linux/BSD) monospace
    #   - (Windows) Consolas
    family: SauceCodePro Nerd Font Mono
    style: Regular

  # Bold font face
  bold:
    # Font family
    #
    # If the bold family is not specified, it will fall back to the
    # value specified for the normal font.
    family: SFMono Nerd Font 
    style: Heavy

  # Italic font face
  italic:
    # Font family
    #
    # If the italic family is not specified, it will fall back to the
    # value specified for the normal font.
    family: SauceCodePro Nerd Font

    style: Light Italic

  # Bold italic font face
  bold_italic:
    # Font family
    #
    # If the bold italic family is not specified, it will fall back to the
    # value specified for the normal font.

    family: SauceCodePro Nerd Font Mono
    style: Black Italic 

  # Point size
  size: 14
```

对于vim支持斜体的配置部分如下, 当然你也可以根据需要自行调整：
```vim
highlight Comment cterm=italic
highlight Function cterm=bold, italic
```
 
 # 效果

如图： 

 <img src="https://s3.jpg.cm/2021/09/03/Itc3iO.png" style="zoom:80%;" />
