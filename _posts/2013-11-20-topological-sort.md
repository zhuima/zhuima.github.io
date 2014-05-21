---
layout: post
title: "拓扑排序"
description: ""
category:
tags: [Algorithms]
---
{% include JB/setup %}

最近在看一些图算法，发现拓扑排序频繁出现，这里写一下自己的一些总结。

拓扑排序是对于有向无环图而言的(DAG)，就是对于这个图所有的点(V1, V2, ... Vn)找到一个点序列使得任意边(u, v)， u出现在v的前面。很容易证明，如果一个有向图中有环那么不存在拓扑排序。


## 现实中的问题

首先来看现实中哪些问题需要拓扑排序的，课程排序，编译依赖问题，类似的凡是涉及到相关顺序的时间安排，比如Rails里面的初始化调用了库[Tsort](http://ruby-doc.org/stdlib-2.0/libdoc/tsort/rdoc/TSort.html)来进行排序。Unix中有个命令也叫[tsort](http://en.wikipedia.org/wiki/Tsort_(Unix))，在有的makefile里面还直接使用了这个命令来解决依赖问题。

## O(V+E)的算法

<img src="/images/topologicalsort.png" alt="topologicalsort" class="img-center" width="250" height="250"/>

拓扑排序的基本算法是用DFS，我们希望把有出度的点尽量排在前面，所以这里需要注意和DFS的区别。比如上面图中的一个DFS访问顺序是: 5 2 3 1 0 4, 但是这不是一个拓扑排序，4需要排在0的前面，5, 4, 0, 2, 3, 1。
拓扑排序中需要等迭代完节点的连接邻点后再把当前点压入栈。


{% highlight cpp %}

void Graph::_topological(int v, bool visited[], stack<int>& stack) {
    visited[v] = true;
    for(list<struct Edge>::iterator it = adj[v].begin();
		it != adj[v].end(); ++it) {
	int u = it->v;
	if(visited[u] == false)
		_topological(u, visited, stack);
	}
	stack.push(v);
}

void Graph::TopologicalSort(stack<int>& stack) {
    bool visited[V];
    for(int i=0; i<V; i++)
        visited[i] = false;
    for(int i=0; i<V; i++) {
        if(visited[i] == false) {
            _topological(i, visited, stack);
        }
    }
}

{% endhighlight %}

## 唯一性
如果一个DAG的拓扑排序中任意连续的两点都是可连通的，那么这个序列也就是DAG的Hamiltonian路径，而且如果DAG图的Hamiltonian路径存在，那么拓扑排序就是唯一的。否则如果一个拓扑排序结果不是Hamiltonian路径，那么就存在多个拓扑排序结果。

## 其他图算法的预处理

- DAG的强连通分支问题
 先得到拓扑排序，形成逆向图(所有边与原来方向相反)，然后根据拓扑排序依次再进行DFS。

- DAG的最短路径问题，这可以在O(V+E)复杂度解决最短路径问题。同样类似的算法适用与DAG的最长路径问题，给定一个点求DAG中的各个点与给定点之间的最长路径。最长路径问题要比最短路径问题难，因为最长路径问题没有最优子结构，[对于通用的图的最长路径算法还是NP难的问题](http://en.wikipedia.org/wiki/Longest_path_problem)。
