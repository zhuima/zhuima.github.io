---
layout: post
title: "Have a try on Ninja"
description: ""
category: Tools
tags: [Ninja, makefile]
---
{% include JB/setup %}



<img src="/images/compiling.png" alt="My code is compiling" class="img-center" />

## 什么是Ninja
 在Unix/Linux下通常使用Makefile来控制代码的编译，但是Makefile对于比较大的项目有时候会比较慢，看看上面那副漫画，代码在编译都变成了程序员放松的借口了。所以这个Google的程序员在开发Chrome的时候因为忍受不了Makefile的速度，自己重新开发出来一套新的控制编译的工具叫作[Ninja](https://github.com/martine/ninja)，Ninja相对于Makefile这套工具更注重于编译速度。除了Chrome现在还有一些其他的比较大的项目也在开始使用Ninja，比如LLVM。我试用了一下感觉还是不错，比如编译Cmake时间大概是原来的1/4。Ninja试用C++实现，[其支持的语法非常简单](http://martine.github.com/ninja/manual.html)，作者在这里说明了为了控制复杂度。

## 代码如何编译
其实对于C/C++和很多其他程序的编译都是一个道理，就是把一些源代码文件编译成目标文件，或者有的目标文件再编译到一个库里，然后再链接起来。所以Ninja的配置文件分为两个部分，rule和文件依赖关系。看个简单的例子:

{% highlight sh %}
    cc=gcc
    cflags= -g -c
    
    rule cc
         command =	$cc $cflags $in -o $out
    
    rule link
         command = $cc $in -o $out
    
    rule cleanup
         command = rm -rf *.exe *.o
    
    build func.o           : cc func.c
    build main.o           : cc main.c
    
    build app.exe            : link main.o func.o
    
    build all:  phony || app.exe
    build clean: cleanup 
{% endhighlight %}    

非常易懂，编译的可执行未见叫做app.exe, 其中有三条rule: cc, link, cleanup。看看这个官方的试用手册，还有一些附加参数可以加在rule的下面，比如description用来在编译的时候显示出来。Ninja还有个比较好玩的功能就是Ninja -t graph all命令，这可以用来生成编译时候的依赖关系，可以用dot来生成图片等。Ninja的实现也可以大概推测到，根据用户给的依赖关系图，_并行_ 地编译各个文件。

使用Ninja的一个问题就是需要生成这个build.ninja文件，对于大型项目来说这样一条一条地写配置文件是不可能的。幸好我们可以使用Cmake来生成这个配置文件，Cmake对应的是automake这样的东西。在[Cmake的最新版本](http://www.kitware.com/news/home/browse/CMake?2012_11_07&CMake+2.8.10+Just+Released)中已经支持参数Camke -G Ninja，Cmake会根据用户给定的CMakeLists.txt来生成build.ninja文件。而CmakeLists文件相对来说要简单一些，只要写清楚编译的可执行文件的名字，和其依赖的包含main函数的源文件。把我的[迷宫小项目](https://github.com/chenyukang/Maze)来举个例子,在项目文件夹下写配置文件CMakeLists.txt:

{% highlight sh %}    
cmake_minimum_required(VERSION 2.8)
project (Maze)

add_library(maze A_star.cpp Algorithm.cpp DFS_L.cpp DFS_R.cpp DisjSets.cpp Maze.cpp)

add_executable(Maze.exe main.cpp)
target_link_libraries(Maze.exe maze)

{% endhighlight %}

add_library写明了生成一个叫做maze.a的库文件，然后和main.cpp编译出来的main.o生成可执行文件，写好CmakeList.txt后运行Cmake -G Ninja, 然后运行ninja all就能编译这个工程。具体的Cmake语法参考[这里](http://www.cmake.org/cmake/help/cmake_tutorial.html)，对于不少项目来说Cmake已经足够使用，只是我觉得Cmake还是稍微复杂了一点。

## 我这样来使用

 整个Ninja是使用C++写的开源项目，如果我们想增加一些自己的feature可以hack一下，不过作者估计不会接受增加语法支持的patch。我准备做一个小的hack来自动分析我当前的源码，自动生成build.ninja文件，不要求处理所有的复杂情况，只是分析.cc和.c，自动检测main函数文件。最后用户只用配置链接参数就可以了。我觉得这样用起来就非常方便了，待完成中，顺便看看Ninja的内部实现。
