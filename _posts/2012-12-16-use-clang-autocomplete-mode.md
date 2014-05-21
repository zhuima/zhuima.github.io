---
layout: post
title: "Clang is Making Emacs Smarter"
description: ""
category: Tools
tags: [LLVM, Gcc, Emacs]
---
{% include JB/setup %}

在Emacs下自动补全总是个问题，对于同一个buffer内的基于symbol补全auto-complete-mode做得非常好了，但是因为没有进行代码的分析，所以像结构体的成员变量或者类的成员函数的补全是不可能的。当然你可能试过这个号称最智能的[GCCSence](http://cx4a.org/software/gccsense/),但是我觉得这个东西够复杂的，在使用之前还需要用户手动运行一个命令来用Gcc处理一遍，它还会把一些东西放在sqlite数据库里面。这大概是因为Gcc不编译做静态分析工具造成的，在[这里](http://lwn.net/Articles/493599/)、[这里](http://lwn.net/Articles/493627/)、[这里](http://lwn.net/Articles/493630/)有讨论，Google的一个静态分析的项目从Gcc迁移到LLVM，重点是这:

    The gcc version has been difficult to support and maintain, due mainly to the fact that the GIMPLE intermediate language was never designed for static analysis. The abstract syntax tree provided by Clang is an easier data structure to work with for front-end analyses of this kind.

这个thread挺好玩的，后面变成了一大群人争论functional programming和Imperative Programming。这篇[The Downfall of Imperative Programming](http://fpcomplete.com/the-downfall-of-imperative-programming/)再好好看看。

回到正题，我最近切换到Mac下。因为在Mac OS X下编译器变成了Clang， Clang是基于LLVM的。LLVM对于分析代码是有比较方便的支持，所以基于LLVM有各种分析源程序的工具了，Xcode下的一些辅助开发的工具还是很舒服的。前些天突然想到那么会不会有个东西来作为Emacs的自动补全的后端，一搜果然有了这个[auto-complete-clang](https://github.com/mikeandmore/auto-complete-clang)，使用了一下非常的方便。其实看看其代码是在后面调用Clang的，比如在main.cc源文件里面写一些代码:

{% highlight cpp %}

    #include <string>
    #include <vector>
    using namespace std;
    
    class Demo{
    public:
        void print();
        void test();
    private:
        int value;
    };
    
    int main() {
        std::string s;
        Demo demo;
        demo.
    }
{% endhighlight %}


结果还是非常精准的，不想截图了。后端运行的命令其实是:
{% highlight sh %}
    cmd: clang -cc1 main.cc -fsyntax-only -code-completion-at main.cc:18:10
{% endhighlight %}

所得到的结果是:
{% highlight sh %}
    COMPLETION: Demo : Demo::
    COMPLETION: operator= : [#Demo &#]operator=(<#const Demo &#>)
    COMPLETION: print : [#void#]print()
    COMPLETION: test : [#void#]test()
    COMPLETION: value : [#int#]value
    COMPLETION: ~Demo : [#void#]~Demo()
{% endhighlight %}

auto-complete-clang做的事情就是把这个结果再展示出来，其实这条命令也做了语法检查的，所以加上一个语法检查的功能应该也是可以的。一搜果然还是有了，看这个[Realtime syntax checking with emacs](http://duncan.mac-vicar.com/2011/08/30/realtime-syntax-checking-with-emacs/)，需要翻墙，不过代码在[Github上](https://github.com/dmacvicar/duncan-emacs-setup/tree/master/site-lisp)。其实其后端运行的命令是：

{% highlight sh %}
   cmd: clang  -fsyntax-only -fno-exceptions main.cc
{% endhighlight %}

最近用这个插件，基本代码都会是一遍编译通过啊，哈哈。Clang错误提示也人性化一点，比如在Xcode下会提示你想的是不是"XXX"之类的。

