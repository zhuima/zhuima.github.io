---
layout: post
title: "UbiGraph动态显示Python函数调用"
description: ""
category: 
tags: [Python, Ubigraph]
---
{% include JB/setup %}

## UbiGraph显示环境
 [UbiGraph](http://www.ubietylab.net/ubigraph/content/Docs/index.html)是一个显示平台，可以非常方便地使用Python/C/Ruby来控制渲染，只需要制定节点和边还有其他相关属性，其余的都不用管了。其使用XML-RPC服务于客户端，所以甚至可以在一台机器上开server，在另外一台机器上用渲染代码控制，这个环境对于算法和数据的可视化很有用。 比如：

<pre class="prettyprint lang-python">
import ubigraph

U = ubigraph.Ubigraph()
U.clear()

x = U.newVertex(shape="sphere", color="#ffff00")
  
smallRed = U.newVertexStyle(shape="sphere", color="#ff0000", size="0.2")
  
previous_r = None
for i in range(0,10):
  r = U.newVertex(style=smallRed, label=str(i))
  U.newEdge(x,r,arrow=True)
  if previous_r != None:
    U.newEdge(r,previous_r,spline=True,stroke="dashed")  
  previous_r = r

</pre>

显示效果如下：
<img src="/images/ubigraph_py.png" alt="ubigraph_python" class="img-center"/>

只是这个软件是免费的但不是开源的，另外还没有支持Windows平台。

## 使用Ubigraph显示Python函数调用

这是在[这里](http://pyevolve.sourceforge.net/wordpress/?p=210)看到的，貌似需要翻墙。代码比较简单，在点击查看[prof3d.py](images/prof3d.py)。

使用方法是先启动Ubigraph的server，然后运行下面的代码：

<pre class="prettyprint lang-python">
import prof3d
 
def run_main():
   # your code
 
if __name__ == "__main__":
   prof3d.profile_me()
   run_main()
</pre>

这段Python的代码函数调用关系就显示出来了，而且还是动态的。

效果如下：
<img src="/images/python_call.png" alt="ubigraph_python" class="img-center"/>



