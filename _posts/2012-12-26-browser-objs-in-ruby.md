---
layout: post
title: "Browser objs and class hierarchy  in Ruby"
description: "Browser objs and class hierarchy  in Ruby"
category: Programming
tags: [Ruby, Programming]
---
{% include JB/setup %}

Ruby里一切都是对象，如何能看到Ruby内建的对象模型呢。这里有个小程序来查看Ruby内部构建好的的对象和类。ObjectSpace可以迭代所有对象。

{% highlight ruby %}
    set = Set.new()
    ObjectSpace.each_object do |x|
      set.add(x.class)
    end

   set.each do |x|
     puts x
   end
   
{% endhighlight %}

下面这段就能根据对象，取得class对象，建立起类的继承图。

{% highlight ruby %}

  # Creates or updates a klass_tree.
  # When updating no classes or objects are removed
  def object_browser(classtree = ClassTreeNode.new(Kernel))
    ObjectSpace.each_object do | x |
      classnode = classtree
      x.class.ancestors.reverse[1..-1] \
        .inject(classtree){ | classnode, klass |
        classnode.add_class(klass)
      }.add_object(x)
    end
    classtree
  end

{% endhighlight %}

use this command to get image:

  $ruby prog.rb > class.dot; dot -Tpng class.dot -o class.png


结果看起来像这样，所有对象都画出来比较多，看大图还稍微能看到一些。完整的代码在[这里](https://gist.github.com/4380597)。
<img src="/images/class.png" alt="class in Ruby" class="img-center" />
<img src="/images/objs.png" alt="class in Ruby" class="img-center" />
