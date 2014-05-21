---
layout: post
title: "Learning Ruby with Ruby Warrior"
description: "Learning Ruby with Ruby Warrior"
category: programming
tags: [programming]
---
{% include JB/setup %}

Ruby上总有好玩的东西，偶然看到这个[RubyWarrior](https://github.com/ryanb/ruby-warrior)，玩了一把感觉还有些意思。这个有些像我原来介绍的[RubyRobot](http://cyukang.com/2012/11/22/ruby-robot-ai.html),
不过更像之前的[Wumpus](http://cyukang.com/2011/07/22/wumpus-and-2.html)，看来我对这种游戏有些兴趣。

Ruby新手边玩边熟悉了语言。需要代码的可以clone下来看看，如果只是玩可以gem装上，然后运行rubywarrior就开始练级了。

> gem install rubywarrior

我现在只是完成了初学者模式，这里的AI还比较简单，主要实现一个函数就行了。分为两种模式，第一种只用对付当前的场景，第二种为epic(史诗?)模式，要从1~9连续闯关。

我的平均成绩是C，所有级的代码放在[Github上](https://github.com/chenyukang/rubywarrior)了。
<pre><code>
Level Score: 27
Time Bonus: 18
Level Grade: F
Total Score: 374 + 45 = 419
Your average grade for this tower is: C

Level 1: S
Level 2: C
Level 3: B
Level 4: B
Level 5: D
Level 6: F
Level 7: B
Level 8: F
Level 9: F
</code></pre>
----------------------------------------------------

中级模式是二维的地图，所以更有挑战。

[这里](https://github.com/Spooner/ruby_armor)有一个前端，不过我还没用过。

[这还有人用神经网络的方法来做的](https://github.com/Sujimichi/ruby_warrior_NN_solution)，可以学习一下，:)。
