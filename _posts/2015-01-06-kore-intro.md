---

layout: post
title: Kore简单介绍
category: coding

---

> [Kore][2]是用c写的超快速和灵活的Web服务器/框架，作者[Joris Vink][1]，
> 今天在github上找到，准备用来练习一下ｃ语言

<!--more-->

# 安装

{% highlight sh %}

$ git clone https://github.com/jorisvink/kore.git
$ cd kore
$ make
$ sudo make install

{% endhighlight %}

# 创建工程

{% highlight sh %}

$ kore create myapp

{% endhighlight %}

此时工程的目录:

+ myapp
+
	+ src（源码）
	+
		+ myapp.c
	+ conf（配置文件）
	+
		+ myapp.conf
	+ cert（证书文件）
	+
		+ server.crt
		+ server.key
	+ assets（页面,css,图片等）

# 编译运行

{% highlight sh %}

$ cd myapp
$ kore build
$ kore run

{% endhighlight %}

通过浏览器查看https://127.0.0.1:8888/，可以看见一个空白页面

# Hello World

> 修改myapp.c

{% highlight c %}

#include <kore/kore.h>
#include <kore/http.h>

#include "assets.h"

int	page(struct http_request *);

int
page(struct http_request *req)
{
	http_response_header(req, "content-type", "text/html");
	http_response(req, 200, asset_index_html, asset_len_index_html);
	return (KORE_RESULT_OK);
}

{% endhighlight %}

> 在assets文件夹下添加index.html

{% highlight html %}

<!DOCTYPE>
<html>
<head>
	<title>Hello World!</title>
</head>

<body>

Hello World!

</body>
</html>

{% endhighlight %}

>  编译运行

{% highlight sh %}

$ kore build
$ kore run

{% endhighlight %}

通过浏览器查看https://127.0.0.1:8888/，看见Hello World!


[1]: https://github.com/jorisvink
[2]: https://kore.io
