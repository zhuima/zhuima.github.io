---
layout: post
title: "Heartbleed简单分析"
description: "Heartbleed简单分析"
category:
tags: [Programming, Bug]
---
{% include JB/setup %}

<img src="/images/heartbleed.png" alt="heartbleed" class="img-center" height="200" width="200"/>

这几天不断听到一个词“心血漏洞”，近年来影响最严重的互联网漏洞。今天小小地研究了一把，顺便把引起一些思考记录下来。

## 到底是什么样的代码

有一些C语言和开发经验的朋友看看这个[Fix](https://git.openssl.org/gitweb/?p=openssl.git;a=commitdiff;h=96db9023b881d7cd9f379b0c154650d6c108e9a3;hp=0d7717fc9c83dafab8153cbd5e2180e6e04cc802)就能了解些具体细节了。
在网络传输中有一个叫做心跳的概念，简单来讲就是客户端发送一个简单的心跳包给服务端，服务端又返回给客户端，然后客户端检查传回来的内容是否是预期，这样就知道了当前的TLS通信是否正常。这个Bug不是协议的问题，而是具体实现的时候的遗漏了相关的逻辑。

这个函数dtls1_process_heartbeat就是处理这块代码的，先读出长度和包类型，然后申请一段内存空间做一个memcpy，其中长度为write_length, 而这里遗漏的就是这个长度的合法性检查。

{% highlight cpp %}
/* Read type and payload length first */
      hbtype = *p++;
      n2s(p, payload);
      pl = p;

{% endhighlight %}

{% highlight cpp %}
   unsigned char *buffer, *bp;
   unsigned int write_length = 1 + 2 + payload + padding;
   buffer = OPENSSL_malloc(write_length);
   bp = buffer;

   /* Enter response type, length and copy payload */
   *bp++ = TLS1_HB_RESPONSE;
   s2n(payload, bp);
   memcpy(bp, pl, payload);
   bp += payload;

   /* Random padding */
   RAND_pseudo_bytes(bp, padding);

   r = dtls1_write_bytes(s, TLS1_RT_HEARTBEAT, buffer, write_length);
{% endhighlight %}

可以想象如果客户端发送一个长度为很大的数，而实际给的内容还是在符合范围内的长度，而memcpy仍旧拷贝了一个比较大范围的内存空间(因为申明的包长度类型这里最大为64K)。
而这个临近的内存空间存的是些什么东西就不确定了，偶尔可能包含一些敏感信息，比如用户密码等等，这些数据有一定特征，是可以通过一定手段检测出来的。这个Bug的名字很形象，就像是血从服务器这个身体里慢慢渗出来一样。

这个简单的长度检查遗漏照理来说应该会被发现，因为内存如果越界了可能会引起SegmentFault。但是OpenSSL有一个自己的内存分配器。可以想象OpenSSL先开辟一大块内存，后面的内存使用再自行分配。这样memcpy即使超出了预订的范围也没有造成问题。

## 影响有多大
OpenSSL作为一个基础设施，世界上大量现存的网络相关的软件都在使用，特别是一些服务器。光Apache和nginx就占了Web server的66%，甚至还包括Email服务器(SMTP,POP, IMAP协议等)，VPN，和一大堆的客户端软件。这些都使得大量用户的密码有可能泄露。各个互联网公司都在为自己的产品打patch来解决这个潜在的风险。用户也有可能要再修改自己的密码来规避风险。

## 如何避免这样的Bug
这个Bug引起了一些争议，是否开源软件存在更大的风险。因为这个Bug如果是在私有软件里，可能不会一下引起这么多人的关注，整个互联网也不必整个为此patch一遍。

对于程序员来说，如何避免这样的Bug? Redis的开发者Antirez的这篇文章[Using Heartbleed as a starting point
](http://antirez.com/news/76)写得挺不错，公司应该投入更多的资金在这种关键的涉及到安全的代码上，[OpenSSL每年接收到的资助为2000美金](http://www.solidot.org/story?sid=39112)。系统程序员和测试人员应该使用一些静态代码分析器，另外动态检测器(比如Valgrind)也很有帮助。因为C是一个贴近硬件的语言，可以在C上再增加一个抽象层来保护关键信息。Random测试有可能发现很多软件中潜在的问题，单元测试有可能测不到这种情况。我现在工作的公司对于测试这块还是做得挺不错(这也与我的产品特性有关，测试相对容易一些)，我们每天晚上除了跑单元测试，还需要跑Valgrind来检测内存问题，还有大量极端的random case可以发现很多Bug。
