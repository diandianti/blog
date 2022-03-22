---
title: 如何自定义Notion Widget
date: 2022/3/22 21:00:00
update: 2022/3/22 21:00:00
tags:
- web
- notion
categories: web
excerpt: 既然Notion Widget的原理是嵌入整个网页，那我为什么不自己写一个网页，自己托管？
---

# 起因

前几天在少数派的网站上看到了一篇介绍notion资源的文章 （https://sspai.com/post/71893）。 其中让我最感兴趣的是在notion中嵌入天气倒计时等信息的widget， 但是提供这些widget的网站又或多或少的有一点点限制，用起来会有一点点的难受，所以我便研究了一下这些widget的实现，下面便是我的研究成果。

# Widget的构成

以下面这个倒计时widget为例（其实这个是我自己后来瞎写的）
![倒计时](https://s6.jpg.cm/2022/03/22/L39nyS.png)

如果在一个单独的页面打开它，它的显示是这样的
![倒计时全屏](https://s6.jpg.cm/2022/03/22/L39Uyi.png)

查看一下它的源代码, 发现这就是一个普通的网页然后在不停的更新时间（样式好看一些的更新）， 那么如果我想实现一些类似的功能，那么只需要写一个网页，然后找个服务器托管就可以了，那么就需要实践一下了。

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Lato:400,700|Montserrat:900">
    <title>Document</title>
    <style>

body{
    background-color: white;
}
  
#timer {
  color: #eeeeee;
  text-align: center;
  text-transform: uppercase;
  font-family: 'Lato', sans-serif;
  font-size: .7em;
  letter-spacing: 5px;
}

.days, .hours, .minutes, .seconds {
  display: inline-block;
  padding: 20px;
  width: 100px;
  border-radius: 5px;
}

... ...


</style>
</head>
<body>
    
<div id="timer">

    <!-- <div class="days"> 
        <div id="days" class="numbers "> </div>days</div>  -->
      <div class="hours"> 
        <div  id="hours" class="numbers"> </div>hours</div> 
      <div class="minutes"> 
        <div  id="minutes" class="numbers"> </div>minutes</div> 
      <div   class="seconds"> 
        <div id="seconds" class="numbers"> </div>seconds</div> 
      </div>

</div>

</body>
<script>

// const year = new Date().getFullYear();
// const myDate = new Date('Oct 15, 2024 00:00:00');

const myDate = new Date().setHours(18, 0, 0, 0);
console.log(myDate);

// countdown
let timer = setInterval(function() {

  // get today's date
  const today = new Date().getTime();

  // get the difference
  const diff = myDate - today;


  if (diff >= 0) {
	// math
	//let days = Math.floor(diff / (1000 * 60 * 60 * 24));
	let hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
	let minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
	let seconds = Math.floor((diff % (1000 * 60)) / 1000);

	// display
	//document.getElementById("days").innerHTML=days
	document.getElementById("hours").innerHTML=hours
	document.getElementById("minutes").innerHTML=minutes
	document.getElementById("seconds").innerHTML=seconds
  } 

}, 1);
</script>

```

# 自定义widght

##  服务器

这里只是为了测试一下效果，没有必要兴师动众的从头弄一个主机，我使用的是这个静态网页托管网站  https://vercel.com/  ， 我的博客也托管在这个平台上面。

## 网页编写

准备好了服务器之后，需要写一个自己想要的网页来作为widget，构思过程略过，使用codepen来测试一下效果， 看起来还不错。然后将网页的文件添加到github的项目中，命名为test.html
![codepen](https://s6.jpg.cm/2022/03/22/L391Se.png)

## 部署

在vercel中新建一个项目，然后关联github，将刚才github库导入进来部署，参数不用修改，具体的导入和设置过程可以参考  https://blog.diandianti.ml/2022/01/28/blog/ ，在部署好了之后复制分配的网址在地址栏输入 网址 + /test.html 就可以看到刚才部署的静态网页了

![finish](https://s6.jpg.cm/2022/03/22/L39Nu5.png)

# 我已经写好的widget

可以参考这个项目 https://github.com/diandianti/notion

# End

