---

layout: post
title: C MySql核心API小结
category: coding

---

> 看完《Linux程序设计》关于mysql api部分的小结。

<!--more-->

\#include \<mysql.h\>


## 连接mysql

用c语言连接MySQL包含两个步骤：

1. 初始化一个连接句柄接口；
2. 实际进行连接。

{% highlight c %}

MYSQL *mysql_init(MYSQL *);

{% endhighlight %}

> 初始化mysql，通常会传递NULL，它会返回一个指向新分配的连接句柄结构的指针。如果传递一个已有的接口，它将被重新初始化。

{% highlight c %}

MYSQL *mysql_real_connect(MYSQL *connection,
			const char *server_host,
			const char *sql_user_name,
			const char *sql_password,
			const char *db_name,
			unsigned int port_number, // 0则设置端口3306
			const char *unix_socket_name, // NULL则使用mysql默认设置
			unsigned int flags);

{% endhighlight c %}

> 连接mysql，如果无法连接，它将返回NULL

{% highlight c %}

void mysql_close(MYSQL *connection);

{% endhighlight %}

> 释放连接

{% highlight c %}

int mysql_options(MYSQL *connection,
			enum option_to_set,
			const char *argument);

{% endhighlight %}

> 连接设置选项，列出了3个最常用的选项。

|enum选项						|实际参数类型					|说明									
|:------------------------------|:------------------------------|:-------------------------------
|MySQL_OPT_CONNECT_TIMEOUT		|const unsigned int *			|连接超时之前的等待秒数
|MySQL_OPT_COMPRESS				|None，使用NULL					|网络连接中使用的压缩机制	
|MySQL_INIT_COMMAND				|const char *					|每次连接建立后发送的命令	

> e.g.如果要设置连接超时时间为7秒:

{% highlight c %}

unsigned int timeout = 7;
...
connection = mysql_init(NULL);
ret = mysql_options(connection, MYSQL_OPT_CONNECT_TIMEOUT, (const char *)&timeout);

if(ret) {
	/* Handle error */
	...
}
connection = mysql_real_connect(connection ...

{% endhighlight %}

## 错误处理

{% highlight c %}

unsigned int mysql_errno(MYSQL *connection);

{% endhighlight %}

> 返回错误代号

{% highlight c %}

char *mysql_error(MYSQL *connection);

{% endhighlight %}

> 返回错误信息

## 执行SQL语句

{% highlight c %}

int mysql_query(MYSQL *connection, 
			const char *query)

{% endhighlight %}

> 执行sql

{% highlight c %}

my_ulonglong mysql_affected_rows(MYSQL *connection);

{% endhighlight %}

> 检查受影响的行数

{% highlight c %}

MYSQL_RES *mysql_store_result(MYSQL *connection);

{% endhighlight %}

> 一次提取所有数据，用户结果及比较小时

{% highlight c %}

my_ulonglong mysql_num_rows(MYSQL_RES *result);

{% endhighlight %}

> 返回结果集中的行数

{% highlight c %}

MYSQL_ROW mysql_fetch_row(MYSQL_RES *result);

{% endhighlight %}

> 从使用mysql_store_result得到的结果结构中提取一行，当数据用完或者发生错误时返回NULL

{% highlight c %}

void mysql_data_seek(MYSQL_RES *result, 
			my_unlonglong offset //行号);

{% endhighlight %}

>  在结果集中进行跳转

{% highlight c %}

MYSQL_ROW_OFFSET mysql_row_tell(MYSQL_RES *result);

{% endhighlight %}

> 返回一个偏移量，表示结果集中的当前位置。它不是行号，不能把它用于mysql_data_seek

{% highlight c %}

MYSQL_ROW_OFFSET mysql_row_seek(MYSQL_RES *result,
			MYSQL_ROW_OFFSET offset);

{% endhighlight %}

> 在结果集中移动当前位置，并返回之前的位置

{% highlight c %}

void mysql_free_result(MYSQL_RES *result);

{% endhighlight %}

> 情侣结果集对象

{% highlight c %}

MYSQL_RES *mysql_use_result(MYSQL *connection);

{% endhighlight %}

> 一次提取一行数据

## 处理数据

{% highlight c %}

unsigned int mysql_field_count(MYSQL *connection);

{% endhighlight %}

> 返回结果集中的字段数目

{% highlight c %}

MYSQL_FIELD *mysql_fetch_field(MYSQL_RES *result);

{% endhighlight %}

> 提取元数据和数据

$gcc -I/usr/include/mysql hello_mysql.c -L/usr/lib/mysql -lmysqlclient -o hello_mysql



