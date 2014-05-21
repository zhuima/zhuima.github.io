---
layout: post
title: "分支预测优化"
description: ""
category: Programming
tags: [Programming, 优化, 分支预测]
---
{% include JB/setup %}

## 问题

Stack_overflow上有这么一个[帖子:为什么排序后会快很多](http://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-an-unsorted-array)，说是下面这段代码比较诡异，引发了比较多的回复，一起来看看。

  <script src="https://gist.github.com/3090254.js?file=gistfile1.cpp">gist</script>


 * 如果把std::sort(data, data + arraySize);注释掉，下面那段for循环所花费的时间是11.54秒。
 * 如果不注释掉，即排序后下面那段所用的时间是1.93秒。

相差比较大。那个for循环总是要执行完的，为什么排序后会快不少?

## 分支预测
  
  下面的获得票数最多的回复质量非常高，很生动细致地说明了cpu的分支预测技术。

   
<img src="/images/train.jpg" alt="Black Cube Theme" class="img-center" width="500px" />

  看上面这情形，如果在没有通讯设备的年代，如果你是这个火车枢纽的操作人员，
是不是要让火车驾驶员把车停下来，然后告诉你他需要往哪个方向行驶，等你完成转向操作的时候再继续行驶火车呢。也许有一些更好的办法，你可以猜测火车要往哪边行驶。

<ul>
<li>如果你猜对了，那么节省了不少时间。</li>
<li> 如果猜错了，火车停下来，等你撤销刚才的操作，再往前走，这会比较耗费时间。</li>
</ul>

同样在执行指令的时候，cpu也能做这样类似的工作。现代cpu都采用 __指令流水线技术__ ，即处理器会预取下面要执行的一些指令，如果下面的指令正是需要被执行的那就节省了时间，如果在概率上大部分能猜对下面要运行的指令，那就提高了cpu的运行效率。更详细的图文介绍可以参考[wiki](http://en.wikipedia.org/wiki/Branch_predictor)。简单的说明就是cpu会根据前面所执行的指令的历史，归纳出相应的模式，把预测的指令预取进来，然后继续沿着预测的指令执行。如果发现预测错误，则倒过来重新初始化预测表、刷新指令管道然后继续执行。所以看上面的例子：

<pre><code>
T = branch taken
N = branch not taken

data[] = 0, 1, 2, 3, 4, ... 126, 127, 128, 129, 130, ... 250, 251, 252, ...
branch = N  N  N  N  N  ...   N    N    T    T    T  ...   T    T    T  ...

       = NNNNNNNNNNNN ... NNNNNNNTTTTTTTTT ... TTTTTTTTTT  (easy to predict)
</code></pre>

 后面作者又加了一条hack，把这段代码重新改写一下：


      if (data[c] >= 128)       ====>              int t = (data[c] - 128) >> 31; 
         sum += data[c];        ====>              sum += ~t & data[c];


 那么前面是否排序就对这段代码效率没有影响了，同样是2秒多。 这和编译器的优化非常相关，不同的编译器的结果不一样，4.6.1 GCC 加上-O3或者-ftree-vectorize编译选项可以对这种情况进行优化，所以排序与否没有关系，而VC++2010却不能进行类似优化(GCC果然强大)。

## 一个优化例子
 在Linux kernel里面会看到类似likely和unlikely这样的代码，从其名字就很直观的解释了其意义。看其定义为两个宏。
<pre><code>
include/linux/compiler.h
 #define likely(x) __builtin_expect   (!!(x), 1)
 #define unlikely(x) __builtin_expect (!!(x), 0)
</code></pre>

 Linux内核开发者使用这两个宏来通过编译器提示cpu：某一段代码很可能会执行，应该被预测，而有的情况出现的概率比较小，不必预测。类似这样的代码：

<pre><code>
if(likely(some_cond)) { //this is often happen!
    /* Do something */
}

if (unlikely(some_cond)) { //this is rare!
    /* Do something */
}
</code></pre>

关于这个方面，在这篇论文[What every Programmer should know about Memory](http://www.akkadia.org/drepper/cpumemory.pdf)里面有更详细的讲述。分支预测在现代cpu上如此通用，竟然还有人利用这个来尝试破解RSA的，看这个[On the Power of Simple Branch Prediction Analysis](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.80.1438&rep=rep1&type=pdf)。
