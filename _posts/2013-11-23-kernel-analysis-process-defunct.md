---
layout: post
title: "Kernel analysis: Defunct Process"
description: ""
category:
tags: [Kernel, Linux]
---
{% include JB/setup %}

我发现带着问题去看内核代码比较容易理解。如果一个父进程显示的设置SIGCHLD为Ignore，子进程将自己清理自己。

{% highlight cpp %}
#include <signal.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

int main() {
	struct sigaction sa;
	memset(&sa, 0, sizeof(sa));
	sa.sa_handler = SIG_IGN;
	sigaction(SIGCHLD, &sa, NULL);
	int pid = fork();
	if(pid > 0) {
		printf("parent:%d\n", getpid());
		sleep(30);
	} else {
		printf("child:%d\n", getpid());
		sleep(4);
	}
	printf("finished\n");
	return 0;
}
{% endhighlight %}

我们可以顺便看看内核里面是怎么写的，

linux/kernel/exit.c里面这部分是负责进程退出的，我截取了相关的代码：

{% highlight cpp %}
/*
 * Send signals to all our closest relatives so that they know
 * to properly mourn us..
 */
static void exit_notify(struct task_struct *tsk, int group_dead)
{
    bool autoreap;

    forget_original_parent(tsk);
    write_lock_irq(&tasklist_lock);

    /* .... */

    } else if (thread_group_leader(tsk)) {
        autoreap = thread_group_empty(tsk) &&
            do_notify_parent(tsk, tsk->exit_signal);
    } else {
        autoreap = true;
    }

    tsk->exit_state = autoreap ? EXIT_DEAD : EXIT_ZOMBIE;

    /*..... */

    /* If the process is dead, release it - nobody will wait for it */
    if (autoreap)
        release_task(tsk);
}
{% endhighlight %}


其中有一段是判断是否autoreap，我们继续可以看看
linux/kernel/signal.c里面的do_notify_parent函数:

{% highlight cpp %}
bool do_notify_parent(struct task_struct *tsk, int sig)
{
    struct siginfo info;
    unsigned long flags;
    struct sighand_struct *psig;
    bool autoreap = false;

    /* .... */

    if (!tsk->ptrace && sig == SIGCHLD &&
        (psig->action[SIGCHLD-1].sa.sa_handler == SIG_IGN ||
         (psig->action[SIGCHLD-1].sa.sa_flags & SA_NOCLDWAIT))) {
        /*
         * We are exiting and our parent doesn't care.  POSIX.1
         * defines special semantics for setting SIGCHLD to SIG_IGN
         * or setting the SA_NOCLDWAIT flag: we should be reaped
         * automatically and not left for our parent's wait4 call.
         * Rather than having the parent do it as a magic kind of
         * signal handler, we just set this to tell do_exit that we
         * can be cleaned up without becoming a zombie.  Note that
         * we still call __wake_up_parent in this case, because a
         * blocked sys_wait4 might now return -ECHILD.
         *
         * Whether we send SIGCHLD or not for SA_NOCLDWAIT
         * is implementation-defined: we do (if you don't want
         * it, just use SIG_IGN instead).
         */
        autoreap = true;
        if (psig->action[SIGCHLD-1].sa.sa_handler == SIG_IGN)
            sig = 0;
    }
    if (valid_signal(sig) && sig)
        __group_send_sig_info(sig, &info, tsk->parent);

    __wake_up_parent(tsk, tsk->parent);
	return autoreap;
}
{% endhighlight %}

可以看到如果父进程对子进程的生死不关心，那么设置autoreap为TRUE，甚至这个信号也可以不发送了。
