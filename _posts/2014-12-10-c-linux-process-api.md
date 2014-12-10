---

layout: post
title: C Linux Process API小结
category: coding

---

> 《Linux程序设计》关于进程API小结。

<!--more-->

#进程操作

{% highlight c %}

#include <stdlib.h>

int 
system(
	const char *string
);

{% endhighlight %}

> 执行命令，等同于：

{% highlight sh %}

$ sh -c string

{% endhighlight %}

{% highlight c %}

#include <unistd.h>

int
execl(
	const char *path,
	const char *arg0,
	...,
	(char *)0
);

int 
execlp(
	const char *file,
	const char *arg0,
	...,
	(char *)0
);

int 
execle(
	const char *path,
	const char *arg0,
	...,
	(char *)0,
	char *const envp[]
);

int 
execv(
	const char *path,
	char *const argv[]
);

int 
execvp(
	const char *file,
	const *const argv[]
);

int
execve(
	const char *path,
	char *const argv[],
	char *const envp[]
);

{% endhighlight %}

> 将当前进程替换为一个新进程

{% highlight c %}

#include <sys/types.h>
#include <unistd.h>

pid_t fork(void);

{% endhighlight %}

> 复制进程映像， 常用语法：

{% highlight c %}

pid_t new_pid;
new_pid = fork();

switch(new_pid) {
case -1: /* 错误 */
	break;
case 0 : /* 子进程 */
	break;
default: /* 父进程 */
	break;
}

{% endhighlight %}

{% highlight c %}

#include <sys/types.h>
#include <sys/wait.h>

pid_t // 返回子进程PID
wait(
	int *stat_loc //如果非空指针，状态信息将被写入它所指向的位置
);

{% endhighlight %}

> 暂停父进程直到子进程结束为止

{% highlight c %}

#include <sys/types.h>
#include <sys/wait.h>

pid_t 
waitpid(
	pid_t pid, // 指定进程PID
	int *stat_loc,
	int options // 常用WNOHANG，作用是防止waitpid调用将调用者的执行骨气
);

{% endhighlight %}

> 等待指定子进程的结束

# 信号

信号是UNIX和Linux系统响应某些条件而产生的一个事件。接收到该信号的进程会相应地采取一些行动。

{% highlight c %}

#include <signal.h>

void 
(*signal(
	int sig, // 信号代码
	void (*func)(int) // 处理函数
))(int);

{% endhighlight %}

> 信号处理

{% highlight c %}

#include <sys/types.h>
#include <signal.h>

int
kill(
	pid_t pid,
	int sig
);

{% endhighlight %}

> 发送信息

{% highlight c %}

#include <unistd.h>

unsigned int 
alarm(
	unsigned int seconds
);

{% endhighlight %}

> 在seconds秒之后发送发送一个SIGALRM信号

{% highlight c %}

#include <unistd.h>

int pause(void);

{% endhighlight %}

> 把进程挂起直到有一个信号出现为止

# 更加健壮的信号接口

{% highlight c %}

#include <signal.h>

int 
sigaction(
	int sig,
	const struct sigaction *act,
	struct sigaction *oact
);

// sigaction至少包括以下介个成员
struct sigaction {
	void (*) (int)sa_handler, /* 函数指针, SIG_DEF 或者SIG_IGN  */
	sigset_t sa_mask, /* sa_handler屏蔽的信号集 */
	int sa_flags
};

{% endhighlight %}

> 接收并处理信号

# 信号集

\#include \<signal.h\>

{% highlight c %}

int 
sigaddset(
	sigset_t *set,
	int signo
);

{% endhighlight %}

> 向信号集中添加信号

{% highlight c %}

int 
sigemptyset(
	sigset_t *set
);

{% endhighlight %}

> 将信号集初始化为空

{% highlight c %}

int 
sigfillset(
	sigset_t *set
);

{% endhighlight %}

> 将信号集初始化为包含所有已定义的信号

{% highlight c %}

int 
sigdelset(
	sigset_t *set, 
	int signo
); 

{% endhighlight %}

> 从信号集中删除信号

{% highlight c %}

int sigismember(
	sigset_t *set,
	int signo
);

{% endhighlight %}

> 检查给定信号是否是一个信号集的成员

{% highlight c %}

int 
sigpending(
	sigset_t *set
);

{% endhighlight %}

> 将被阻塞的信号中停留在待处理状态的一组信号写到参数set指向的信号集中

{% highlight c %}

int
sigsuspend(
	const sigset_t *sigmask
);

{% endhighlight %}

> 将进程的屏蔽字替换为由参数sigmask给出的信号集，然后挂起程序的执行
