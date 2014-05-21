---
layout: post
title: "bsfl指令和Bitmap的一个优化"
description: ""
category: 
tags: [Programming, bitmap]
---
{% include JB/setup %}


## 如何找出int中第一个1

对于这个问题我们可以从最原始的做法开始，如果没找到1返回0，如果第一位为1返回1。所以代码很简单如下：

<pre class="prettyprint lang-c">
static int first_onebit(int x){
    if(!x)
        return 0;
    else{
        int idx = 0;
        if(x%2 != 0) return 1;
        while( x%2 == 0 ) {
            x>>=1;
            idx++;
        }
        return idx+1;
    }
}
</pre>

如何能做到更好呢?这里有一个trick使用一条指令来完成这个工作，具体可以参考[Linux Kernel里面这个ffs](http://lxr.free-electrons.com/source/arch/x86/include/asm/bitops.h)的代码。我来简化一下就是这么一个函数:

<pre class="prettyprint lang-c">
/* ffs: if ret == 0 : no one bit found
   return index is begin with 1 */
static int first_onebit(int x)
{
    if (!x) {
        return -1;
    }
    else {
        int ret;
        __asm__("bsfl %1, %0; incl %0" : "=r" (ret) : "r" (x));
        return ret;
    }
}
</pre>

这里的bsfl是一条intel汇编指令，它的用法是bsfl op1,op2:顺向位扫描从右向左（从位0-->位15或位31）扫描单节字或双字节操作数op2中第一个含"1"的位，并把扫描到的第一个含'1'的位的位号送操作数op1中。
所以就是一条指令完成了这个计算过程。

这里真的会有多大的差别么，我们可以用程序来计算一下，测试如下:


    int main()
    {
        int x;
        for(x=0; x<=1000000000; x++){
            first_onebit(x);
        }
        return 0;
    }

第一个版本花费时间15.685s，第二个版本花费时间5.960s,而其实如果只是循环1000000000次什么也不做也好花费3.091s,所以第二个版本快到如此程度。


## bitmap的优化

bitmap是一种常用的数据结构，在编程珠玑有详细介绍，应用也比较广泛比如可以用来做操作系统当中的[地址索引查询](http://wiki.osdev.org/Page_Frame_Allocation)。
对于bitmap中我们常常需要一个操作来找一个空位的bit来做set操作。既然我们知道了第一个1是如何快速查找的，第一0也就好办了，先取反，然后再找第一个1就行了。

    #define first_zerobit(x) (first_onebit(~(x)))

继续bitmap的first_empty就优化成这样了:


    u32 first_empty()
    {
        u32 idx;
        for(idx=0; idx<max_idx; idx++){
            if(arr[idx] == 0xFFFFFFFF) //full
                continue;
            u32 v = arr[index];
            return 32*idx + first_zerobit(v) - 1;
        }
        return -1;
    }


## 注意

这样的用汇编指令来优化可能会有平台差异，所以你最好清楚自己的平台是否适用。


