---
title: 如何拥有一个自己的博客
date: 2022/01/28 21:00:00
update: 2022/01/28 21:00:00
tags:
- 网络
categories: Tool
excerpt: 不就是张飞吃豆芽。
---



# 为啥要有一个自己的博客？

如果使用第三方平台来写博客，自己可能会省去好多事情，但是同时也限制住了自己写作的自由。虽然我也不会发表什么奇奇怪怪的文章，但是写作的自由在自己的手上总比在别处要开心的多。

# 基本流程

1. 申请域名
2. 使用DNSPod管理域名，加速国内访问（如果没有需求可以跳过）
3. 购买服务器
4. 拉取源码搭建博客
5. 域名指向

搭建一个自己的博客的大致流程就是这个样子，下面是详细的流程。

# 域名申请

域名可以使用国内的各种云，比如阿里云、腾讯云等，但是国内的域名购买需要实名制，部分还需要备案，我比较懒，直接使用国外的免费域名。打开下面的这个网址

[https://my.freenom.com/domains.php](https://my.freenom.com/domains.php)

然后在搜索框里面输入你想要的域名

![https://s6.jpg.cm/2022/01/28/Lv9S6U.png](https://s6.jpg.cm/2022/01/28/Lv9S6U.png)

然后选择一个你想要的，点击“Get it now”

![https://s6.jpg.cm/2022/01/28/LvT6ue.png](https://s6.jpg.cm/2022/01/28/LvT6ue.png)

然后跟着提示一步一步往下走就可以了，最后或者域名一个。

# 使用DNS Pod管理域名

如果你不在意国内对于域名访问的速度问题，以及部分网络无法访问的问题，那么这一步可以跳过。

打开网址 [https://www.dnspod.cn/](https://www.dnspod.cn/) 然后使用微信登录（当然其他的登陆方式也是可以的）。登录之后点击 控制台 → DNS解析 → 域名管理 → 我的域名 → 添加域名

![https://s6.jpg.cm/2022/01/28/LvafWz.png](https://s6.jpg.cm/2022/01/28/LvafWz.png)

在里面输入你刚才申请的域名，然后点击确认，这个时候域名的状态为 DNS不正确

![https://s6.jpg.cm/2022/01/28/Lva3cX.png](https://s6.jpg.cm/2022/01/28/Lva3cX.png)

点击红色的DNS不正确，会出现一个详细信息

![https://s6.jpg.cm/2022/01/28/LvaCvD.png](https://s6.jpg.cm/2022/01/28/LvaCvD.png)

复制右面框里的DNS服务器地址，然后打开

[https://my.freenom.com/clientarea.php?action=domains](https://my.freenom.com/clientarea.php?action=domains)

点击 Manage Domain，在接下来的界面如下操作

![https://s6.jpg.cm/2022/01/28/LvaKip.png](https://s6.jpg.cm/2022/01/28/LvaKip.png)

![https://s6.jpg.cm/2022/01/28/Lvajl6.png](https://s6.jpg.cm/2022/01/28/Lvajl6.png)

将刚才DNS Pod提供的服务器地址填入，点击确定即可。

![https://s6.jpg.cm/2022/01/28/LvaysT.png](https://s6.jpg.cm/2022/01/28/LvaysT.png)

稍等片刻，DNSPod中的域名解析就会正确。

# 搭建博客

服务器需要购买才可以使用，但是搭建一个博客的话购买一个主机就有些杀鸡用牛刀了，这里直接使用目前免费的平台。

我使用的是[https://vercel.com/](https://vercel.com/)，当然你也可以使用github pages，操作都是类似的。登录 [https://vercel.com/](https://vercel.com/)网站，然后点击new project

![https://s6.jpg.cm/2022/01/28/Lvilxr.png](https://s6.jpg.cm/2022/01/28/Lvilxr.png)

然后导入一个你喜欢的网站模板，或者是已经存在的github项目，为了演示我新建一个jekyll项目。最后点击create即可。之后便会自动部署你的博客。

![https://s6.jpg.cm/2022/01/28/LviV65.png](https://s6.jpg.cm/2022/01/28/LviV65.png)

# 域名指向

上一步虽然搭建好了博客，而且可以通过vercel给的域名访问，但是给出的域名是vercel下面的，而且很长不好记，所以可以将自己刚才申请的域名通过CNAME的方式指向自己的博客。

在vercel中点击刚才创建的项目，然后点击 stettings，按照下图方式操作，其中填入的域名格式为：

“你想要的前缀.刚才申请的域名”，比如我刚才申请的是mofish.ga，我就修改为blog.mofish.ga。

![https://s6.jpg.cm/2022/01/28/Lvi8EC.png](https://s6.jpg.cm/2022/01/28/Lvi8EC.png)

添加之后如果没有问题，应该会出现下面的界面

![https://s6.jpg.cm/2022/01/28/LviAjt.png](https://s6.jpg.cm/2022/01/28/LviAjt.png)

记下 “cname.vercel-dns.com”这个网址，然后打开DNSPod，在刚才申请的域名记录里面添加一个指向这个网址的CNAME记录

![https://s6.jpg.cm/2022/01/28/LviUtz.png](https://s6.jpg.cm/2022/01/28/LviUtz.png)

然后等待一会，等DNS记录的值刷新之后，vercel项目中的域名就变成了这个样子

![https://s6.jpg.cm/2022/01/28/Lvib0u.png](https://s6.jpg.cm/2022/01/28/Lvib0u.png)

然后你就可以通过blog.mofish.ga这个域名来访问刚才创建的博客了。

需要注意的是部分平台会提供HTTPS的证书，如果平台不提供则需要自己申请。

# END