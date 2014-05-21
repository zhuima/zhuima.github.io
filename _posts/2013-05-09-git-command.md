---
layout: post
title: "迁移到Git"
description: "CVS to Git"
category: 
tags: [Tools]
---
{% include JB/setup %}

公司这群人终于打算从CVS迁徙到Git上了，CVS这套公司用了六年。CVS这是90年代的东西，我们不能因为年代久远而嫌弃这，只是CVS这东西对于一个比较大的项目来说创建分支是相当漫长，大多数程序员都没有耐心的。
迁徙计划虽然纸上谈兵了很长时间，直到现在才终于打算行动。

上午把Git在服务器上搭建好，主要卡在一个Git的命令上，因为一些权限问题。

{% highlight sh %}

git init --bare --shared=group ; --shared=group forget this

{% endhighlight %}

Git的web接口是用的是[ViewGit](http://viewgit.fealdia.org/)，自己做了一些修改，加上[GeShi](http://qbnz.com/highlighter/)来高亮代码，并使用了[GitStats](https://github.com/trybeee/GitStats)来做代码统计。GitStats统计的项目非常多，看起来很直观。

稍微记录一下常用的一些git命令。

这里有一个最直观的Git学习的地方[leanGitBranch](http://pcottle.github.io/learnGitBranching/?demo)。

<table border="2" cellpadding="5" align="center">
  <tbody>
<tr>
<td>检出仓库 </td> <td>   git clone repo</td>
</tr>
<tr>
<td> 更新</td>   <td> git pull</td>
<tr>
<td> 提交到远程</td> <td> git push</td>
</tr>
<tr>
<td>提交到本地</td>  <td>git commit -am"log message" </td>
</tr>
<td>创建branch</td>  <td>git branch branch_name</td>
<tr>
<td>切换branch</td>  <td>git checkout branch_name</td>
</tr>
<tr>
<td>合并branch</td>  <td>git merge branch_name</td>
</tr>
<tr>
<td>图形界面</td>         <td> gitk</td>
</tr>
<tr>
<td>解决冲突</td>          <td>git mergetool</td>
</tr>
<tr>
<td>撤销上一次commit</td>  <td>git revert HEAD</td>
</tr>
<tr>
<td>撤销上上次commit</td>  <td>git revert HEAD^ </td>
</tr>
<tr>
<td>撤销上一次的merge</td>  <td>git reset --hard HEAD^</td>
</tr>
</tbody>
</table>


