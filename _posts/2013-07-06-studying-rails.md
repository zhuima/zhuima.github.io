---
layout: post
title: "初学Rails"
description: "初学Rails"
category: Programming, Rails
tags: [Programming, Rails]
---
{% include JB/setup %}

我在2012年左右开始关注Ruby，平时有的时候会用Ruby写一些脚本。这是一个很活跃的社区，Ruby火起来也不是最近的事。可贵的这里总是有一些新的东西出来，比如我现在的这个博客是基于jekyll和Github的。
Ruby的迅速崛起更多的还是因为Rails，所以学习Rails也是了解Ruby的一个好方法。

最近为公司内部所配置的GitLab是Rails开发的。另外我自己也在公司做一些Web程序，其实是很简单的东西，就是把每天晚上跑的程序各种测试结果展示出来(nightly/weekly/coverage等等)。我选用Rails来开发，果然一个最初的版本很快就做出来了。在初学Rails的过程中让我体会到了一些web开发的乐趣。


Rails适合小团队的快速开发，其中的一些理念是：

<ul>
<li>Encourage Agility             --鼓励敏捷开发 </li>
<li>Convention Over Configuration --约定高于配置 </li>
<li>DRY                           --不要重复自己 </li>
<li>Less Code                     --更短小的代码 </li>
</ul>

正是这些开发原则使得Rails开发如此简单明了(当然前提是你按照Rails约定的方式来)。
我原来做过一些web开发服务器方面的工作，在那种模式下开发需要每个人各司其责。
但是Rails不同，在ActiveRecord这样的抽象层基础上你需要关注的数据库方面的东西少了，
明确的MVC模式把你需要关注的撤离开来，这种复杂程度一个人完全能掌控下来。当然这种高度的抽象是以牺牲一部分效率为前提的，但其实在很多时候开发效率的优先级是高于实现效率的，
这也是Ruby所选择的一个理念。 

学习Rails的过程中这些资料是非常好的，这几本书都面向初学者，写得非常详细：

- [Ruby On Rails教程](http://railstutorial-china.org/)
- [Begining Rails 3](http://book.douban.com/subject/4005707/)
- [Agile Web Development with Rails](http://book.douban.com/subject/1416743/)

当我熟悉了一些基本概念的时候，我就可以看Github上各种Rails的代码了，约定高于配置的另外一个优点就是所有Rails开发的东西结构看起来是一样的，便于不同开发者之间的交流。

--------------------------------------------

Rails的一个比较突出的问题是版本之间的兼容性比较差。

比如Begining Rails里面Plugin那章的那个例子，在Rails3.1系列开始已经不支持那种方式的plugin了，其中用到的[class_inheritable_accessor](http://stackoverflow.com/questions/12313621/undefined-method-class-inheritable-accessor-for-activerecordextensionssqlite)也变成了class_attribute。这种问题非常多，另外据说最新的Rails4.0改动也很大。

这是一个老问题，在早起的版本就有人在这上面都发生过争吵。一些人说变化太频繁，不容易学习。
其中这篇["WTH is happening to Rails?" I'll tell you](http://erniemiller.org/2011/06/14/wth-is-happening-to-rails-ill-tell-you/) 
解释了一下Rails如此的原因，并称这种改变位『成长』。

学习Rails的路还比较长，后面继续。

