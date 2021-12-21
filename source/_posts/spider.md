---
title: 一次简单的爬虫经历
date: 2021/12/21 21:00:00
update: 2021/12/21 21:00:00
tags:
- python
categories: python
excerpt: 为了看新闻联播，我写了个爬虫。
---

# 起因

测试视频播放器的时候需要用到一些视频（其实是很多）来看看长时间的播放会不会导致播放器崩溃，可是单一的文件多次播放还是会有一点点的偶然性，所以就需要一系列的视频来播放。这个时候我想到了新闻联播，一年365天不休息，而且播了这么多年，数据肯定多，所以就去下载一些新闻联播的视频好了。

# 开始下载

下载视频，我首先想到了you-get这个软件，只要输入视频的网页路径就可以下载网页上面的视频。所以我手动下载了最近几天的新闻， 可以下载没有问题，而且可以跑满带宽。

[You-Get](https://you-get.org/)

# 进阶

如果就需要几天的视频，一个网址一个网址地复制粘贴还可以，关键需要的数量太多，不可能手动完成，所以就需要写一个爬虫程序来获取视频了。

首先分析一下需求：

- 下载视频的时候 you-get 基本可以跑满带宽，所以不需要多线程加速
- 需要一个ip代理池，防止下着下着我的ip就被封了
- 要自动获取每天视频的网页地址

接下来就一步一步实现。

## 获取新闻联播的网页地址

打开浏览器，输入新闻联播的网址 [http://tv.cctv.com/lm/xwlb/](http://tv.cctv.com/lm/xwlb/) 。然后按下F12，切换到网络选项卡，点击不同日期的新闻就可以看到页面的网络请求，第一个请求的是一个shtml的网页文件，这个就是节目列表了，如下图

![https://s6.jpg.cm/2021/12/21/LcjhE4.png](https://s6.jpg.cm/2021/12/21/LcjhE4.png)

点开节目列表的内容看一下是不是和网页上面的新闻标题一致，看起来都是一致的。点开标题对应的链接就转向了视频的页面，那么每个标题对应的链接就是视频的网页地址了。

![https://s6.jpg.cm/2021/12/21/LcjjjX.png](https://s6.jpg.cm/2021/12/21/LcjjjX.png)

使用you-get测试一下上面的地址，没有问题。

```bash
(base) ➜  ~ you-get -i https://tv.cctv.com/2021/12/10/VIDERjGehdKgKOaBZPRTJvko211210.shtml
site:                CNTV.com
title:               [视频]国际联播快讯
streams:             # Available quality and codecs
    [ DEFAULT ] _________________________________
    - format:        1
      container:     mp4
      video-profile: 1280x720_2000kb/s
    # download-with: you-get --format=1 [URL]

    - format:        2
      container:     mp4
      video-profile: 1280x720_1200kb/s
    # download-with: you-get --format=2 [URL]

    - format:        3
      container:     mp4
      video-profile: 640x360_850kb/s
    # download-with: you-get --format=3 [URL]

    - format:        4
      container:     mp4
      video-profile: 480x270_450kb/s
    # download-with: you-get --format=4 [URL]
```

再进一步研究发现每天的节目信息网页地址是按照一定格式生成的，如下：

```
http://tv.cctv.com/lm/xwlb/day/年月日.shtm
```

所以到时只需要解析这个格式的网页的内容就可以了。

## ip代理池

ip代理池我直接使用别人写好的，按照其中的说明配置就可以了。

[GitHub - jhao104/proxy_pool: Python爬虫代理IP池(proxy pool)](https://github.com/jhao104/proxy_pool)

使用这个库有一个小问题，就是需要自己安装redis数据库的服务，可以在redis官网下载，然后编译安装。

[Redis](https://redis.io/download)

## 在python程序中使用you-get

在you-get的官网中只给出了cli的调用方式，没有给出python API的调用方式，所以只能使用os或者sys库吗。使用os或者sys库，感觉有一些多此一举，所以我阅读了一下you-get的源码，主要的下载程序在you_get.common 里面，参数的设置也在里面，所以可以通过引入you_get.common的方式来调用you-get。

## 编写爬虫程序

因为实现的功能比较简单，所以程序也比较简单，如下：

```python
import requests as r
import bs4
import you_get.common as uget_c

header = {"User-Agent":"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.20 Safari/537.36 Edg/97.0.1072.21"}

def get_proxy_dict():
    # 获取代理，由proxy_pool库提供
    proxy_pool_url = "http://0.0.0.0:5010/get/"
    http_proxy = None
    https_proxy = None

    while (http_proxy is None) and (https_proxy is None):
        res = r.get(proxy_pool_url, headers = header)
        if res.status_code != 200:
            continue

        res_json = res.json()

        if res_json["https"]:
            https_proxy = res_json["proxy"]
        else:
            http_proxy = res_json["proxy"]

    https_proxy_url = "https://%s"%(https_proxy)
    http_proxy_url = "http://%s"%(http_proxy)

    return {"https":https_proxy_url, "http":http_proxy_url}

def get_cctv_video(html_txt,out_dir):

    # 使用BeautifulSoup库解析html代码，也可以使用正则表达式
    res = bs4.BeautifulSoup(html_txt, "html.parser")

    all_video = res.find_all("a")

    for v in all_video:
        v_url = v["href"]
        print(v_url)
        uget_c.any_download(v_url, output_dir=out_dir, merge=True)
        #下载完主要视频后停止下载当天的视频，如果继续下载会下载拆分的视频内容
        break

def get_cctv(date):

    # 处理视频网页路径
    cctv_url_prefix = "http://tv.cctv.com/lm/xwlb/day/%s.shtml"
    url_to_get = cctv_url_prefix%(date)
    print("Get %s"%url_to_get)

    try_count = 0
    try_limit = 5

    # 因为网络和代理的不确定性，所以在获取网页失败后多次尝试
    while try_count < try_limit:

        try:
            proxy_dict = get_proxy_dict()
            res = r.get(url_to_get, headers=header, proxies=proxy_dict)
        except:
            try_count += 1
            continue

        if res.status_code != 200:
            try_limit += 1
            continue
        else:
            get_cctv_video(res.text, "./cctv/")
            break

    print("Get video fail!")

if __name__ == "__main__":

    # 获取十月份的新闻联播
    for i in range(1,32):
        get_cctv("202110%02d"%(i))
```

# 完成

查看一下已经下载完成的视频：

```python
(base) ➜  cctv ls
 《新闻联播》 20211001 2100.mp4   《新闻联播》 20211008 2100.mp4   《新闻联播》 20211015 2100.mp4   《新闻联播》 20211022 2100.mp4   《新闻联播》 20211029 2100.mp4
 《新闻联播》 20211002 1900.mp4   《新闻联播》 20211009 2100.mp4   《新闻联播》 20211016 2100.mp4   《新闻联播》 20211023 2100.mp4   《新闻联播》 20211030 1900.mp4
 《新闻联播》 20211003 1900.mp4   《新闻联播》 20211010 1900.mp4   《新闻联播》 20211017 1900.mp4   《新闻联播》 20211024 1900.mp4   《新闻联播》 20211031 1900.mp4
 《新闻联播》 20211004 1900.mp4   《新闻联播》 20211011 2100.mp4   《新闻联播》 20211018 2100.mp4   《新闻联播》 20211025 2100.mp4
 《新闻联播》 20211005 1900.mp4   《新闻联播》 20211012 1900.mp4   《新闻联播》 20211019 1900.mp4   《新闻联播》 20211026 2100.mp4
 《新闻联播》 20211006 2100.mp4   《新闻联播》 20211013 1900.mp4   《新闻联播》 20211020 1900.mp4   《新闻联播》 20211027 1900.mp4
 《新闻联播》 20211007 1900.mp4   《新闻联播》 20211014 1900.mp4   《新闻联播》 20211021 2100.mp4   《新闻联播》 20211028 1900.mp4
```

其实我发现我下载的视频数目也不多，不会被封IP，所以ip代理池可以不用。

