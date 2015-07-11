---

layout: post
title: tomcat在正式运行环境中默认catalina.out增长过快的解决方法
category: code

---

> 因为一直都使用tomcat默认日志配置才会出现这样的问题.

<!--more-->

解决思路

1. [用log4j管理tomcat的输出日志](http://blog.csdn.net/hbsong75/article/details/39432811)
2. [过滤debug信息,特别是项目中使用的框架的debug信息](http://blog.csdn.net/zuoanren/article/details/8465958)
3. 使用crontab定时删除或合并一段时间以前的日志


## 只保留最近七天的访问日志文件

{% highlight sh %}

#!/bin/bash
LOGS_PATH=/var/log/tomcat/access
cd ${LOGS_PATH}
find . -mtime +7 -name "*20[0-9][0-9]*" | xargs rm -f
exit 0

{% endhighlight %}

