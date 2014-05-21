---
layout: post
title: "Metaprogramming Ruby"
description: ""
category:
tags: []
---
{% include JB/setup %}

『Metaprogramming Ruby』这本书看了两遍，从这本书里获取了一些乐趣。技术书籍就应该这样简明扼要，寓理于事。通过一个显示中的例子引入问题，展示元编程的解决办法， 顺带介绍一下用到相关技术的gems。

 下面这些不是书评，只是我在看第二遍的时候的一些简单的择要，用于自己的记忆和检索。


## Introduction

- Meteprogramming is writing code that writes code
- 鬼城和集市，很多语言的运行环境在执行的时候已经固定，一片死寂。而支持Metaprogramming的语言的执行环境是充满活力的集市。很好的比喻。
- 动态元编程和静态元编程，C++的template属于静态元编程。


## The Ruby Object Model

1. Class定义永远是开放的，你能重新定义任何类或者给类加上一些新的东西。注意MonkeyPatch可能导致的Bug。
2. 分清楚instance_method和class_method，
3. <img src="/images/instance_method.png" alt="instance_method" class="img-center" />
4. Class也是对象。与C#/Java的Class不一样的地方，Ruby允许在代码运行期间操作类相关的信息，比如增加method或者重新定义method。
5. <img src="/images/object_model.png" alt="object_model" class="img-center" />

## Methods
- static type checking, for example, if you call simple_talk() on Layer object that has no such method, the compiler protests loudly.
- call method dynamic using send().
- define_method generates instance method dynamically, to_s vs to_sym.
- Ghost method, method_missing.

> 过多是用会不会拖慢执行效率，要顺着继承链一直查找method。
>
> 注意method_missing可能导致的死循环调用。
>
> 和继承过来的method之间的冲突， undef_method解决。

## Blocks
- class, module, and def change scope.
- Flat Scope.
- instacen_eval/instance_exec
- create block : lambda/proc/Proc.new
- lambda vs Proc

> return in Proc also return from the scope.
>
> lambda's argument checking is more strict.

- A event DSL, a elegent example for blocks.

## Class Definitions
- A Ruby class definition is actually regular code that runs.
- class_eval vs instance_eval

> class_eval both changes self and current class
>
- Eigenclass, the metaclass of a object
- three way to define class method
- Around alias

## Code writes code
- The powerful weapon: eval
- A good example: add_attribute
- Three ways to express this idea

## Active record
- Validations
- alias_method_chain

* * * * *

- Dynamic attributes, define read/write/question Dynamic Methods for all the columns in databases, for performance.
- Lesson learned, performance/complexity/readable trade-offs.

## Metaprogramming safely
- Defusing Monkeypatches, make it explicit with module, check it before patche, add warning messages.
