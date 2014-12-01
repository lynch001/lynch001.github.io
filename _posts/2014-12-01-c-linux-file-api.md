---

layout: post
title: C Linux File API小结
category: coding

---

> 看完《Linux程序设计》关于文件操作api部分的小结。

<!--more-->

#底层文件访问

{% highlight c %}

#include <unistd.h>

size_t // 实际写入字节数 
write(
	int fildes,
	const void *buf,
	size_t nbytes
);

{% endhighlight %}

> 把缓冲区buf的前nbytes个字节写入与文件描述符fildes关联的文件中

{% highlight c %}

#include <unistd.h>

size_t // 实际读入字节数
read(
	int fildes,
	void *buf,
	size_t nbytes
);

{% endhighlight %}

> 从与文件描述符fildes相关联的文件里读入nbytes个字节的数据，并把它们放到数据区buf中

{% highlight c %}

#include <fcntl.h>
#include <sys/types.h>
#include <sys/stat.h>

int 
open(
	const char *path, 
	int oflags
);

int 
open(
	const char *path, 
	int oflags, 
	mode_t mode // oflags使用O_CREATE时，必须设定此参数
);

{% endhighlight %}

> 访问文件

{% highlight c %}

#include <unistd.h>

int 
close(
	int fields
);

{% endhighlight %}

> 终止文件描述符fildes与其对应文件之间的关联

{% highlight c %}

#include <unistd.h>

int 
ioctl(
	int fildes,
	int cmd,
	...
);

{% endhighlight %}

> 系统调用

{% highlight c %}

#include <unistd.h>
#include <sys/types.h>

off_t // 从文件头到文件指针被设置处的字节偏移值，失败时返回-1
lseek(
	int fildes,
	off_t offset,
	int whence
);

{% endhighlight %}

> 设置读写指针

{% highlight c %}

#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>

int 
fstat(
	int fildes, 
	struct stat *buf
);

int stat(
	const char *path, 
	struct stat *buf
);

int lstat(
	const char *path, 
	struct stat *buf
);

{% endhighlight %}

> fstat系统系统调用返回与打开的文件描述符相关的文件的状态信息，该信息将会写到一个buf结构中，buf的地址以参数形式传递给fstat

> stat和lstat返回通过文件名查找到的状态信息，但当文件是符号链接时，lstat返回的是符号链接本身的信息，而stat返回的是该链接指向的文件的信息

{% highlight c %}

#include <unistd.h>

int 
dup(
	int fildes
);

int 
dup2(
	int fildes, 
	int fildes2
);

{% endhighlight %}

> 复制文件描述符

## 标准I/O库
在标准I/O库中，与底层文件描述对应的是流（stream），它被实现为指向结构FILE的指针。
在stdio.h里定义的stdin、stdout和stderr分别代表着标准输入、标准输出和标准错误输出，与底层文件描述符0、1和2系相对应。

{% highlight c %}

#include <stdio.h>

FILE * // 失败时返回NULL
fopen(
	const char *filename, 
	const char *mode // e.g,"r"或"rb"代表只读方式
);

{% endhighlight %}

> 打开文件流

{% highlight c %}

#include <stdio.h>

size_t // 成功读到数据缓冲区里的记录个数，当到达文件尾时候，可能小于nitems甚至可以是零
fread(
	void *ptr, 
	size_t size, // 置顶每个数据记录长度
	size_t nitmes, // 要传输的记录个数
	FILE *stream
);

{% endhighlight %}

> 从文件流读取数据

{% highlight c %}

#include <stdio.h>

size_t // 成功写入的记录个数
fwrite(
	const void *ptr,
	size_t size,
	size_t nitems,
	FILE *stream
);

{% endhighlight %}

> 从指定的数据缓冲区读书记录，并把它们写到输出流

{% highlight c %}

#include <stdio.h>

int 
fclose(
	FILE *stream
);

{% endhighlight %}

> 关闭指定的文件流

{% highlight c %}

#include <stdio.h>

int 
fflush(
	FILE *stream
);

{% endhighlight %}

> 把文件流里的所有未写数据立刻写出,fclose函数隐含执行了一次flush操作

{% highlight c %}

#include <stdio.h>

int // 0表示成功，-1表示失败并设置errno指出错误
fseek(
	FILE *stream,
	long int offset, 
	int whence
);

{% endhighlight %}

> 为下一次读写操作置顶指定位置

{% highlight c %}

#include <stdio.h>

int // 到达文件尾部或出错时返回EOF
fgetc(
	FILE *stream
);

int 
getc( // 作为宏定义实现
	FILE *stream
);

int 
getchar(); // 相当于getc(stdin)，从标准输入读取下一个字符

{% endhighlight %}

> 从文件流中读取下一个字节并把它作为一个字符返回 

{% highlight c %}

#include <stdio.h>

int
fputc(
	int c, 
	FILE *stream
);

int
putc( // 作为宏定义实现
	int c,
	FILE *stream
);

int 
putchar( // 相当于putc(c, stdout)
	int c
);

{% endhighlight %}

> 向文件流写入一个字符

{% highlight c %}

#include <stdio.h>

char *
fgets(
	char *s,
	int n,
	FILE *stream
);

char *
gets( // 对传输字符的个数并没有限制，所以它可能会溢出自己的缓冲区
	char *s
);

{% endhighlight %}

> 从输入文件流读取一个字符串

## 格式化输入和输出

{% highlight c %}

#include <stdio.h>

int 
printf(
	const char *format, 
	...
);

int 
sprintf(
	char *s,
	const char *format, 
	...
);

int
fprintf(
	FILE *stream,
	const char *format
	...
);

{% endhighlight %}

> 格式化输出

{% highlight c %}

#include <stdio.h>

int 
scanf(
	const char *format,
	...
);

int
fscanf(
	FILE *stream,
	const char *format,
	...
);

int
sscanf(
	const char *s,
	const char *format,
	...
);

{% endhighlight %}

> 格式化输入

{% highlight c %}

#include <errno.h>

extern int errno;

{% endhighlight %}

> 此外部变量指出stdio函数的错误,应该总是在使用它之前将它复制到另一个变量中，因为像fprintf这样的输出函数本身就可能改变errno的值

{% highlight c %}

#include <stdio.h>

int 
ferror(
	FILE *stream
);

{% endhighlight %}

> 测试一个文件流的错误标识，如果该标识设置就返回一个非零值，否则返回0

{% highlight c %}

#include <stdio.h>

int 
feof(
	FILE *stream
);

{% endhighlight %}

> 测试一个文件流的文件尾标识，如果该标识被设置就返回非零值，否则返回零

{% highlight c %}

#include <stdio.h>

int 
clearerr(
	FILE *stream
);

{% endhighlight %}

> 清除由stream指向的文件流的文件尾标识和错误标识

## 文件和目录

{% highlight c %}

#include <sys/stat.h>

int 
chmod(
	const char *path, 
	mode_t mode
);

{% endhighlight %}

> 改变文件或目录的访问权限

{% highlight c %}

#include <sys/types.h>
#include <unistd.h>

int 
chown(
	const char *path, 
	uid_t owner, 
	gid_t group
);

{% endhighlight %}

> 改变文件属主

{% highlight c %}

#include <sys/types.h>
#include <sys/stat.h>

int
mkdir(
	const char *path,
	mode_t mode
);

{% endhighlight %}

> 建立目录

{% highlight c %}

int
rmdir(
	const char *path
);

{% endhighlight %}

> 删除目录

{% highlight c %}

#include <unistd.h>

int
chdir(
	const char *path
);

{% endhighlight %}

> 切换目录

{% highlight c %}

#include <unistd.h>

char *
getcwd(
	char *buf,
	size_t size
);

{% endhighlight %}

> 获取当前目录

{% highlight c %}

#include <sys/types.h>
#include <dirent.h>

DIR * // 失败时返回空指针
opendir(
	const char *name
);

{% endhighlight %}

> 打开一个目录并建立一个目录流

{% highlight c %}

#include <sys/types.h>
#include <dirent.h>

struct dirent *
readdir(
	DIR *dirp
);

{% endhighlight %}

> readdir函数返回一个指针，该指针指向的结构里保存着目录流dirp中下一个目录项的有关资料 

{% highlight c %}

#include <sys/types.h>
#include <dirent.h>

long telldir(DIR *dirp);

{% endhighlight %}

> telldir函数的返回值记录着一个目录流里的当前位置

{% highlight c %}

#include <sys/types.h>
#include <dirent.h>

void
seekdir(
	DIR *dirp,
	long int loc
);

{% endhighlight %}

> 设置目录流dirp的目录项指针

{% highlight c %}

#include <sys/types.h>
#include <dirent.h>

int 
closedir(
	DIR *dirp
);

{% endhighlight %}

> 关闭一个目录流

## 错误处理

{% highlight c %}

#include <string.h>

char *
strerror(
	int errnum
);

{% endhighlight %}

> 把错误代码隐射为一个字符串

{% highlight c %}

#include <stdio.h>

void 
perror(
	const char *s
);

{% endhighlight %}

> 把errno变量中报告的当前错误隐射到一个字符串，该字符串先加上字符串s，再加上一个冒号和一个空格,e.g:

{% highlight c %}

perror("errormsg");

// 可能输出"errormsg: Too many open files"

{% endhighlight %}
