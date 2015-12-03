---

layout: post
title: mysql导入数据失败解决方法
category: code

---

> ERROR at line 2104889: ASCII '\0' appeared in the statement, but this is not allowed unless option --binary-mode is enabled and mysql is run in non-interactive mode. Set --binary-mode to 1 if ASCII '\0' is expected.

<!--more-->

解决方法:

开启二进制日志功能

{% highlight sh %}

vim /etc/mysql/my.cnf

{% endhighlight %}

{% highlight sh %}

[client]

binary-mode = 1

{% endhighlight %}

{% highlight sh %}

service mysql restart

{% endhighlight %}

