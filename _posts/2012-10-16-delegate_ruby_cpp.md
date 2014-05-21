---
layout: post
title: "Ruby vs C++ for delegation"
description: ""
category: 
tags: [Ruby, C++, delegator]
---
{% include JB/setup %}


下班之前同事BigBird给我show他的一段C++代码，对于我等拿C++当作C来用的未入门者实看起来实在是炫丽。虽然比较冗长晦涩，不过还是能看懂个大概，然后觉得这对于动态语言是非常容易实现的。 于是晚上回来用Ruby来搞搞，弄出下面这么段代码。


C++版本在这里[https://gist.github.com/3900077](https://gist.github.com/3900077)。
可见动态语言和编译型语言实现起来效率还是好太多了，同时代码也好理解。
再次我讨厌C++类型推导，^_^。

Ruby实现这个方式很多，另外Ruby的库包含SimpleDelegator的，将调用的方法直接传递到其他对象。

<pre class="prettyprint lang-ruby">
#!/usr/bin/ruby

class Delegate
  attr_reader :proc_list

  def initialize()
    @proc_list = []
  end

  def add(*proc)
    proc_list.push(proc)
  end

  def eval(obj)
    for e in proc_list:
        if obj.respond_to?(e[0])
          if e.size == 1
            obj.__send__(e[0])
          else
            obj.__send__(e[0], e[1])
          end
        else
          printf "ERROR:%s is not defined\n", e[0]
        end
    end
  end

end

class Demo
  attr_writer :value

public
  def print()
    printf "value:%d\n", @value
  end

  def hello()
    printf "Hello world!\n"
  end

  def set(val)
    @value = val
  end
end

delegate = Delegate.new()
delegate.add("print")
delegate.add("set", 1)
delegate.add("print")
delegate.add("hello")
delegate.add("nodefine")

d = Demo.new()
delegate.eval(d)
</pre>
