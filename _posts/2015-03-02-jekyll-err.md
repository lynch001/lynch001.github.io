---

layout: post
title: Ubuntu下因为ruby版本问题导致jekyll安装失败
category: coding

---

> 经过测试，更换ruby版本为ruby-dev即可解决

<!--more-->

# 错误

{% highlight sh %}

ERROR:  Error installing jekyll:
ERROR: Failed to build gem native extension.

/usr/bin/ruby1.9.1 extconf.rb
/usr/lib/ruby/1.9.1/rubygems/custom_require.rb:36:in require: cannot load such file -- mkmf (LoadError)
from /usr/lib/ruby/1.9.1/rubygems/custom_require.rb:36:in require
from extconf.rb:1:in <main>

{% endhighlight %}

# 解决方法

{% highlight sh %}

$ sudo apt-get remove ruby

$ sudo apt-get install ruby-dev

{% endhighlight %}

# 另外遇到ExecJS::RuntimeUnavailable错误

> 添加nodejs运行环境即可

{% highlight sh %}

$ sudo apt-get install python-software-properties

$ sudo add-apt-repository ppa:chris-lea/node.js

$ sudo apt-get update

$ sudo apt-get install nodejs

{% endhighlight %}

