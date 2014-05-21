---
layout: post
title: "Ruby's Block and Proc"
description: ""
category: 
tags: [Ruby, Programming]
---
{% include JB/setup %}

### Callable objects

在Ruby当中一切都是对象，但是有一个例外，那就是block。Block和Proc类似，但是还是有稍有差别的，Block。最近在看《Metaprogramming Ruby》，在这节中有个例子是这样的。

<pre class="prettyprint ruby">
require 'highline'
hl = HighLine.new
friends = hl.ask("Friends?" , lambda {|s| s.split(',' ) })
puts "You're friends with: #{friends.inspect}"
⇒
Friends?
Bill,Mirella,Luca
You're friends with: ["Bill", "Mirella", "Luca"]
</pre>

这里看起来hl.ask把Proc当作参数来传递，而不是接受了一个block，接受Block是另外一种使用模式：

<pre class="prettyprint ruby">
require 'highline'
hl = HighLine.new
new_pass = hl.ask("password: ") { |prompt| prompt.echo = false }
</pre>

在highline代码可以看到相应的处理方式，第一种方式lambda构造成的Proc其实传递给了answer_type，而yield来处理block。

<pre class="prettyprint ruby">
    def initialize( question, answer_type )
      # initialize instance data
      @question    = question
      @answer_type = answer_type
      
      # allow block to override settings
      yield self if block_given?
</pre>

### Proc, Lambda, Block

有三种方式转化Block为Proc, Proc.new、Lambda、&Operator。但是在使用过程中Block还是比Proc要常见，在给一个函数传递这种callable objcts的时候，可以隐式或者显示传递，像这样：

<pre class="prettyprint ruby">
def foo(*args)
 yield(args.join(' '))
end

foo('Yukang', 'Chen'){|name| puts "Hello #{name}"} # => "Hello Yukang Chen"

def foo(*args, &blk)
 blk.call(args.join(' '))
end

foo('Yukang', 'Chen'){|name| puts "Hello #{name}"} # => "Hello Yukang Chen"
</pre>

隐式传递要比显式传递performance要好一些。这很早就有[讨论](http://www.ruby-forum.com/topic/71221)，具体原因是根据Ruby的实现一个Block在yield的时候并没有转换为Proc或者其他对象，所以少了一些开销。Ruby中的函数块是高阶函数的一种特殊形式的语法，Matz在设计块的时候考虑到： (1)在高阶函数中，这种只有一个函数参数非常常见，在实际使用中几乎没有必要在一个地方使用多个函数参数，(2)外观和形式上更直观，Enumerable利用块写的代码简洁易懂。
