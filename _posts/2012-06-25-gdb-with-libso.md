---
layout: post
title: "GDB调试动态链接库"
description: ""
category: 
tags: [gdb, Programming, debug]
---
{% include JB/setup %}


今天解决了一个长期会碰到的问题，就是用GDB如何来调试动态链接库。我这个问题的难点是我的需要调试代码是在动态链接库里面，但是启动的不是普通的可以调试的二进制文件，换句话说这不是我所能控制的代码所编译出来的，甚至可能是由脚本程序来控制启动的。这个问题时不时地困扰着我，总结一下尝试过几种调试方式：


 1 使用print来打印log，有时候有用，不好的地方是有时候定位出问题的代码位置还是稍显麻烦。很常用的会定义一对宏，进入函数和退出某个函数的时候都相应调用。

<pre class="prettyprint lang-c">
#define APP_LOG(X)                                  \
    fprintf(stderr, "log: %s %d %s %s\n",           \
            __FILE__, __LINE__, __FUNCTION__, X);   \

#define LOG_ENTER     \
    APP_LOG("enter")
    
#define LOG_LEAVE \
    APP_LOG("leave")

</pre>

 2 对于crash掉的bug，打印出来调用栈是非常有用的。使用[libc提供的Backtraces函数](http://www.gnu.org/software/libc/manual/html_node/Backtraces.html#Backtraces)来获取调用栈。这是在不能提供GDB环境下拿到调用栈的不错方法。不过经过我的实验这对于动态链接库有一定的问题。

 3 最后就是今天试用的比较通用办法。

 我们不管是如何调用到动态链接库文件的，但是肯定会调用进来。所以需要想办法让代码在库代码处停下来，然后把找机会把GDB弄进去。于是乎有这么一个变态的办法，在动态链接库入口处来这么一段，就是执行到这里停住，等待GDB attach这个进程，然后在GDB里设置一个断点，touch创建当前文件夹debug文件就跳出死循环，接下来就是一切在GDB控制下了。


<pre class="prettyprint lang-c">

void wait_attach() {
    fprintf(stderr, "Waiting attach pid: %d\n", getpid());
    while(1) {
        if((access("./debug", F_OK)) != -1) {
            break;
        }
        else
            ;
    }
}
</pre>

这是一个stupid and work的方法，不过我总觉得还有更好的办法来在这种情况下调试。

在查找资料的过程中有点意外收获，顺便推荐GDB一个选项，gdb -tui, 以texture gui方式启动GDB，这是非常方便的文字界面。如果不用这个选项也可以在运行GDB以后按下快捷键盘C-x C-a(怎么这么像Emacs快捷键)来进行gui和非gui的切换。CLI爱好者可以试用一下，DDD什么的可以放下了，嘿嘿。


另外一些有用的GDB命令：

<pre><code>
rbreak:              break on function matching regular expression
where:               Line number currently being executed
tbreak:              Break once, and then remove the breakpoint
watch:               Suspend the process when a certain condition is met
finish:              Continue till end of function
info locals:         View all local variables
backtrace full:      Complete backtrace with local variables
up, down, frame:     Move through frames
set print pretty on: Prints out prettily formatted C source code
set print array on:  Pretty array printing
enable and disable:  Enable/disable breakpoints
set logging on:      Log debugging session to show to others for support
</code></pre>



