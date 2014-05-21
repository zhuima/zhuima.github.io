---
layout: post
title: "Jekyll使用MathJax来显示数学式"
description: ""
category:
tags: [Jekyll]
---
{% include JB/setup %}


使用Jekyll写作文章的时候有可能需要内嵌一些数学公式, [MathJax](http://www.mathjax.org/)就是用来干这个的，试用了一下感觉非常方便。步骤如下:

- 修改html头部。

  在每个页面开头加上这么一句，在Jekyll下可以通过修改default.html加上。

{% highlight cpp %}
<script type="text/javascript"
 src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endhighlight %}

- 本地安装kramdown。

  因为rdiscount和默认的markdown在解析带公式文件的时候都会出现[一些问题](http://stackoverflow.com/questions/10987992/using-mathjax-with-jekyll)，所以最简单办法还是安装kramdown。
 ` $ gem install kramdown`


- 修改_config.yml，把markdown选项修改为:

   `markdown: kramdown`

***

  然后在发布的时候就可以使用`$$`来把需要显示的数学式子扩起来。像这样：

   `$$a^2 + b^2 = c^2$$`



  发布出来就是漂亮的公式了。

$$a^2 + b^2 = c^2$$

$$x^my + a^2 + b^2 = c^2$$

$$x_\mu$$


  一些更酷的例子：

$$ J_\alpha(x) = \sum\limits_{m=0}^\infty \frac{(-1)^m}{m! \, \Gamma(m + \alpha + 1)}{\left({\frac{x}{2}}\right)}^{2 m + \alpha} $$

$$ \frac{1}{\Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{\frac25 \pi}} =
1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {1+\frac{e^{-6\pi}}
{1+\frac{e^{-8\pi}} {1+\ldots} } } } $$


$$ \left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)$$

$$
\begin{aligned}
\nabla \times \vec{\mathbf{B}} -\, \frac1c\, \frac{\partial\vec{\mathbf{E}}}{\partial t} = \frac{4\pi}{c}\vec{\mathbf{j}} \\ \nabla \cdot \vec{\mathbf{E}} = 4 \pi \rho \\
\nabla \times \vec{\mathbf{E}}\, +\, \frac1c\, \frac{\partial\vec{\mathbf{B}}}{\partial t} = \vec{\mathbf{0}} \\
\nabla \cdot \vec{\mathbf{B}} = 0 \end{aligned}
$$

   不过我可能永远用不到这么复杂的表达式 :).

   另外今天找了一个[markdown-mode.el](http://jblevins.org/projects/markdown-mode/)，在Emacs下编辑Markdown文件又方便了不少。
   Mac下的Markdown编辑器[Mou](http://mouapp.com/)也是非常不错的。
