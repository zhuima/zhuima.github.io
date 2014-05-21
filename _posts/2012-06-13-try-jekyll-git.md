---
layout: post
title: "使用Jekyll和Github搭建博客"
description: "基于Github提供平台，使用jekyll来写博客，这是非常适合程序员的博客环境"
category: Notes
tags: [jekyll, git]

---

{% include JB/setup %}

## 为什么要折腾
折腾了几次终于把博客从wordpress搬到Github了，迁徙这事本来是够麻烦的，而且也比较无聊。不过最终还是抑制不住诱惑，这有下面几点点好处。

- - -

+ __编辑方便，专注写作__

  在线下编辑，可以随便选择自己喜欢的编辑器。当然wordpress也有离线编辑工具，不过Linux下我还没找到合适的，
我平常是用[Muse](http://mwolson.org/projects/EmacsMuse.html)生成html，然后再粘贴到站上。其实还好，
就是插入图片不方便。使用Github和Jekyll是完全的离线，你甚至都不需要离开终端就可以发布文章，一切都只是简单的git push而已。
写的过程中还可以用jekyll  --server预览最终排版。

+ __可以使用Github进行版本管理__
	
像写程序一样写日志，这对程序员吸引很大。
我用这个小脚本来完成发布:

{% highlight sh %}

#!/bin/sh
do_commit() {
    cmd="git commit -a -m\"$log\""
    echo $cmd
    git add .;
    git commit -a -m"$log"
    git push;
}

while [ $# -gt 0 ]
do
  case $1 in
    -commit |-u) shift; log=$1; do_commit; exit 0;;
  esac
  shift
done

{% endhighlight %}

+ __简洁__
  
我喜欢这套是因为感觉一切都很简单,在_post目录下写markdown格式的文章，生成网页、push上去就发布了。页面也非常整洁，这对于一个以文字和代码为主要内容的站点来说最合适不过了。而且因为生成的全是静态网页，所以打开的速度也非常快。

- - - 

## 折腾过程

- - - 

 这套工具适合程序员，因为一切都可以在本机上操作，你可以自己写程序来批量处理文档。我是自己写了一点Python小程序转移图片。 迁徙的过程中也会碰上一些问题，不过如果你懂一点Ruby，这些都还是比较好解决的。基本步骤为：

 + 申请GitHub，这个不少程序员有，直接跳过。
 
 + 安装Jekyll 在本地，可能会遇上ruby版本的问题，我的机子上是1.8.7，需要1.9.2，使用[rvm](https://rvm.io//)来解决，具体参考[这里](http://lanvige.iteye.com/blog/857501)。具体使用下面一些命令:

{% highlight sh %}
sudo apt-get install gems curl
gem install rvm
rvm get latest 
rvm reload
rvm install 1.9.2
rvm use 1.9.2
ruby -v #(use 1.9.2)
gem install directory_watcher liquid open4 maruku classifier jekyll

{% endhighlight %}

 + 再建立yourname.github.com项目，git clone [jekyll bootstrap](https://github.com/plusjade/jekyll-bootstrap.git)到自己的代码库。

 + 从wordpress迁徙，我使用wordpress.xml这个方法，最后修改域名解析就大功告成了。
   修改域名的时候在Git上建立CNAME为demo.com，在DNS处设置demo.com的A记录到Github的地址(207.97.227.245)，同时为了使得www.demo.com也指向GitHub,设定
   www的A记录到这个地址。这是我设置的时候出错了的地方。

 整个流程非常简单，你甚至可以在[三分钟内完成Github的博客搭建](http://ztpala.com/2012/01/12/zero-to-hosted-jekyll-blog-in-3-minutes/)，更相详细可以参考[这里](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)[这里](http://vitobotta.com/how-to-migrate-from-wordpress-to-jekyll/)。
