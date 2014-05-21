---
layout: post
title: "获取挂掉程序的栈信息"
description: "在程序退出的时候获取栈信息"
category: Programming
tags: [C/C++, Programming]
---
{% include JB/setup %}

在程序挂掉的时候最好还是留点有用的遗言，特别是对于一些比较难重现的Bug，也许这些信息会成为解决问题的关键。

下面这个技巧可以让程挂掉的时候打印出来栈信息。这个办法来自[这里](http://neugierig.org/software/blog/2012/06/backtraces.html), 我觉得把SIGABRT、SIGBUS信号加进去也挺好的，在此做了点修改。曾经尝也试过glibc的backtrace函数，，但是给的信息不全(没有行号)，对此做得最好的还是gdb。 在终端可以用gdb获取某个进程的当前栈：

    $ gdb -p 5595 -batch -ex bt
    0xb7fb4410 in __kernel_vsyscall ()
    #0  0xb7fb4410 in __kernel_vsyscall ()
    #1  0xb7dc2d50 in nanosleep () from /lib/tls/i686/cmov/libc.so.6
    #2  0xb7dc2b87 in sleep () from /lib/tls/i686/cmov/libc.so.6
    #3  0x0804874f in main () at print_stack.cc:64


那么一个好的办法就是在程序开始的时候设置好信号，绑定SIGSEGV和SIGABRT到DumpBackTrace()函数，DumpBackTrace函数fork出来一个新进程，运行上面的命令来获取调用栈。

{% highlight cpp %}

#include <stdlib.h>
#include <signal.h>
#include <stdio.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <assert.h>

void DumpBacktrace(int) {
    pid_t dying_pid = getpid();
    pid_t child_pid = fork();
    if (child_pid < 0) {
        perror("fork() while collecting backtrace:");
    } else if (child_pid == 0) {
        char buf[1024];
        sprintf(buf, "gdb -p %d -batch -ex bt 2>/dev/null | "
                "sed '0,/<signal handler/d'", dying_pid);
        const char* argv[] = {"sh", "-c", buf, NULL};
        execve("/bin/sh", (char**)argv, NULL);
        _exit(1);
    } else {
        waitpid(child_pid, NULL, 0);
    }
    _exit(1);
}

void BacktraceOnSegv() {
    struct sigaction action = {};
    action.sa_handler = DumpBacktrace;
    if (sigaction(SIGSEGV, &action, NULL) < 0) {
        perror("sigaction(SEGV)");
    }
    if (sigaction(SIGABRT, &action, NULL) < 0) {
        perror("sigaction(SEGV)");
    }
}

void test() {
    //assert(0);
    int* p = 0;
    *p = 0;
}

int main() {
    BacktraceOnSegv();
    test();
}
{% endhighlight %}

另外前段时间看到这篇文章[Solving vs. Fixing](http://www.runswift.ly/solving-bugs.html)写得不错，在面对一个bug的时候，先不要急于立马上gdb调试，根据现有的信息好好思考为什么会出现这个情况。
Reddit上的一个得分最高的回复：


     The ability to reason about code is probably the most important skill. 
     But it is sadly rare, and doesn't seem to be taught much, if at all.
     Some things are simple, others take some more thought:

     * Under what conditions will this branch get taken?
     * What could cause this API to fail?
     * Are all these parameters even valid?
     * What sequence of events could lead to this situation?
     * What assumptions does this code make?
     * What side-effects does this code have?
     * What contract is this code making (or breaking)?
     
     The most talented engineer I know, when presented with a bug, does nothing but 
     read the code and think about the code and how it could fail. 
     Most of the time,  he just figures it out in his head and fixes it. 
     Sometimes he will insert some strategic printfs and narrow it down like that. 
     I don't think I have ever seen him use a debugger, 
     even on the most complex of problems.
