---
layout: post
title: "Emacs会说话"
description: "An Emacs minor-mode enable Emacs speaking English"
category: Emacs
tags: [Emacs]
---
{% include JB/setup %}

出来工作之前我从来没认真考虑过我的英语口语问题，大学时候的四级口语考试C级也没让我意识到自己的发音比较烂。学了好多年哑巴英语，又因为本人生性有点害羞经常不好意思开口说英语，悲剧早就注定。其实我的英语阅读能力还是可以的，不过工作之后同事们都嘲笑我口语听起来像印度人，据说发音极其古怪。

在Mac下有一个叫做say的命令行程序，我有时候会用来听单词单词发音。这个程序加上-f参数也可以用来朗读整个文件。

     say hello wrold
     say -f demo.txt

前几天突然觉得如果写个Emacs Minor Mode，能在边写单词的时候Emacs就把你写的朗读一遍就好了，Emacs号称能煮咖啡，这点小事当然不在话下。其实除了在公司我也很少写英文，不过这个想法看起来比较好玩，于是动手做了一下。预想的基本功能是实现了，我把它叫做EmacSay-mode，意为在Emacs+Mac+Say下实现的，所以这东西可能不能在Linux下运行。

这也是我第一次学着写一个minor mode，实现起来也很简单。整个不到100行elisp代码。

基本思路就是如果当前输入的字符是空白(或者其他非字母字符)，寻找前面一个字符串，格式化成一个命令行，用start-process或者shell-command来调用。
注意start-process会fork出来一个子进程来执行命令，在书写过程中最好还是使用start-process来调用命令，因为say可能要待个一两秒才返回，如果使用shell-command来调用会造成输入有迟钝的感觉。

绑定的快捷键有这些，其中eamcsay-say-buffer是用来朗读当前的整个buffer，如果你想在其中中断朗读使用emacsay-say-stop。

{% highlight cl %}

(defvar emacsay-mode-map nil
  "Keymap for emacsay minor mode")
(unless emacsay-mode-map
  (let ((map (make-sparse-keymap)))
    (define-key map "\C-cs" 'emacsay-say-current-string)
    (define-key map "\C-cp" 'emacsay-say-buffer)
    (define-key map "\C-ct" 'emacsay-say-stop)
    (setq emacsay-mode-map map)))

{% endhighlight %}

还可以有一些小的改进，比如阅读时候闪烁单词，或者say声音的选择等等。

所有代码在GitHub: [emacSay-mode](https://github.com/chenyukang/emacSay)。 


