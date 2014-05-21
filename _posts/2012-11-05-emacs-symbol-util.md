---
layout: post
title: "Emacs iedit/occur 插件"
description: "Emacs symbl util"
category: 
tags: [Emacs]
---
{% include JB/setup %}

今天看到[Mastering Emacs](http://www.masteringemacs.org/articles/2012/10/02/iedit-interactive-multi-occurrence-editing-in-your-buffer/)上介绍iedit插件的一篇文章。对于程序员来说，经常要重命名一个变量，之前我在Emacs下面使用替换命令来完成的，而Iedit可以编辑当前buffer里面多处相同的一个单词，编辑一处其他地方相同的symbol会自动被修改，这对于这样的操作是非常地直观有效，看下面这样的效果，图片来自[Mastering Emacs](http://www.masteringemacs.org/articles/2012/10/02/iedit-interactive-multi-occurrence-editing-in-your-buffer/)。

<img src="/images/iedit.png" alt="screen" class="img-center" />

最开始看到这个功能是在比较新的编辑器[Sublime](http://www.sublimetext.com/)上，算是编辑器里一个很好的小创新吧。

另外在buffer中查找一个symbol也是经常需要的一个操作，我基本会用

<pre class="prettyprint lang-python">
(global-set-key [f3] 'highlight-symbol-next)
(global-set-key [(shift f3)] 'highlight-symbol-prev)
</pre>

来快速地在相同的symbol之间切换，这是来自highlight-symbol.el里面的。

同样的操作也可以用occur-mode来实现，occur的好处在于可以在另外一个窗口列出所找到结果大纲，这样更方便快速跳到相应的位置，这对于任何类型的文件都可以使用，而不止是可能需要静态分析后生成tags的程序。在[Mastering Emacs](http://www.masteringemacs.org/articles/2011/07/20/searching-buffers-occur-mode/)后面有一段代码使得occur-mode可以在当前所有打开的buffer里找关键字。
