---
layout: post
title: "巧妙的XOR Link List"
description: ""
category: Programming
tags: [C/C++, XorLinkList]
---
{% include JB/setup %}

XOR Link List, 只用一个附加的变量来实现双向链表。首先xor本身是个稍微有点难理解的操作。xor有下面的一些特性:

`A ^ 0 = A`

`A ^ A = 0`

`A ^ B = B ^ A`

`(A ^ B) ^ A = B`

`(B ^ A) ^ B = A`

注意最后两条，这是XOR Link List的关键，这也是通过xor操作来实现swap的关键。

{% highlight cpp %}
void xorSwap (int *x, int *y) {
     if (x != y) {
         *x ^= *y;
         *y ^= *x;
         *x ^= *y;
     }
 }
 {% endhighlight %}

这里注意需要判断x!=y，否则如果传入的是相同的指针，最后所指向的变量被设置为0了。

通过最后两条联想到双向链表中的两个指针的实现，一般如下图所示：

{% highlight cpp %}
      ...  A       B         C         D         E  ...
               –>  next –>  next  –>  next  –>
               <–  prev <–  prev  <–  prev  <–
 {% endhighlight %}

 如果把next和prev用一个变量替换还能实现前向和后向遍历，那就节省了一个变量的空间。

{% highlight cpp %}
...  A        B         C         D         E  ...
        <–>  A⊕C  <->  B⊕D  <->  C⊕E  <->
 {% endhighlight %}

比如当前在B节点，其pointer变量为A⊕C，如果前面的A地址保存下来然后做运算(A⊕C)⊕A -> C，这样就得到下一个节点指针，反向遍历同样如此。当然其缺点是逻辑复杂了，删除其中的某一个节点也不方便(删除头和尾要好点)，遍历的时候需要保存上一个节点。这样看来为了省一点点空间这样实现似乎有点不值，在大部分情况下这样的一个pointer的节省并没什么用，不过这其中的细节有趣、巧妙。

同样上面的xorSwap[对于现代的CPU来说也没什么优化](http://stackoverflow.com/questions/249423/how-does-xor-variable-swapping-work)，这样的代码只是更加不便于编译器来实现指令级别的优化。这种类型trick的东西还是要避免使用才好。


自己稍微写了一下，代码在[这个Gist](https://gist.github.com/chenyukang/5364515)。
