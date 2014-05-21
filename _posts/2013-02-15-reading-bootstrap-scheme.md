---
layout: post
title: "读bootstrap scheme"
description: ""
category: 
tags: [Programming, Lisp]
---
{% include JB/setup %}


<img src="/images/scheme_car_cdr.png" alt="scheme-car-cdr" class="img-center" />

For a List in Lisp, Car is the First, Cdr is the Rest, and Lisp means List-Proccessing.

前段时间偶然在网上看到这个[bootstrap scheme这个开源程序](https://github.com/petermichaux/bootstrap-scheme)，读来简洁明了，十分有趣。我对scheme有一点了解，毕竟以前看过一段时间SICP，自己做练习的代码也是scheme写的。scheme本身属于Lisp方言，语法也极其简单，学习起来非常快的。

看看这个简单的scheme实现，不禁再次感叹递归的优美。Lisp这样的语言直接使用语法树结构来表示程序，不仅使得表示出来的程序异常简洁，就是用C语言来实现这种语言的解释器代码也看起来非常优美。在这里区区2000行的C语言代码，当然没有完整地实现scheme所有的内容，甚至只支持了整数。但是包含scheme的基本语法层面的东西，还有lambda。抛开实现的效率不说，递归是易于编写和理解代码的一种方式，这里语法是递归的，parser是递归的，eval也是递归的。在这里所有的东西都是object，没有显示的列表结构，但是嵌套的pair里蕴含着列表和树的关系。在parse阶段建立好一个以object为基本元素的树结构，做eval的时候顺着往下走就是了。

推荐对语言实现感兴趣的同学阅读一下这个代码，如果对scheme不了解也没关系，用一个小时看几个scheme程序基本就了解了。再看这个解释器，你就懂了代码是如何被运行的。

参考[scheme-from-scratch-introduction](http://peter.michaux.ca/articles/scheme-from-scratch-introduction)

图片来自[Draperg's cartoons](http://www.cs.utah.edu/~draperg/cartoons/)