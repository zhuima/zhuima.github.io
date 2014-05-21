---
layout: post
title: "Do Presentation like a Geek"
description: "A introduction for Ruby tool: showoff"
category:
tags: [tool, Ruby]
---
{% include JB/setup %}

很多程序员不喜欢做PPT之类的东西，我也不喜欢。这有另外的原因是一直没找到一个合适的工具，Linux下PPT是个悲剧，Latex学习成本又大了点。
上次在公司分享的时候偶然找到了这个叫做[showoff](https://github.com/schacon/showoff)的工具，熟悉了大概半个小时就上手了，迅速把自己的PPT完成。

showoff是Ruby写的一个适合程序员写PPT的工具，你可以用类似Markdown的语法编辑文本文件，同时在terminal下开一个服务，浏览器访问[localhost:9090](localhost:9090)可以预览的成果。这个过程非常类似用Jekyll来写博客。当然最后可以导出成PDF格式的，或者直接在浏览器上展示。

## 安装

Showoff安装非常简单:

{% highlight sh %}
 $ gem install showoff
 $ git clone (ppt-repo)
 $ cd (ppt-repo)
 $ showoff serve
{% endhighlight %}

## 使用

我觉得showoff一些特别好的特点是:

1. 纯文本编辑 (对程序员有吸引力)
2. 嵌入代码方便，高亮代码
3. 嵌入图片方便
4. 可执行内嵌Javascript，Coffeescript 或者Ruby代码，并显示结果。(对程序员来说很不错)
5. 一些显示特效

赶快看看example目录吧，你就能上手了。
