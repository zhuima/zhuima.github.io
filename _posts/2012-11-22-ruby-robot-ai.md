---
layout: post
title: "Ruby Robot AI"
description: "Ruby Robot AI"
category: Ruby, Programming
tags: [Ruby, Programming]
---
{% include JB/setup %}

最近看到一个[RRobot](http://rrobots.rubyforge.org/index.html)，这是一个用Ruby来实现的坦克对战平台。感觉挺好玩的，周三在公司也顺带和同事分享了一下。有时间的同学可以尝试尝试，用Ruby来写坦克的AI。另外这个不到1000行的程序也比较好读，这种Robot AI平台以前也有C++/Java版本的，不过都要比这个实现得复杂一点吧。

<img src="/images/tank.png" alt="tank-ai" class="img-center" />

每个你控制的robot的api是这些，注意雷达扫描到的目标只包含距离信息，没有x和y，如果雷达扫描得越快所得到的目标位置准确率越低。自己摸索着写，找一些别人[写好的策略](http://rubyforge.org/forum/forum.php?forum_id=4792)来对战一把吧。



     battlefield_height  #the height of the battlefield
     battlefield_width   #the width of the battlefield
     energy              #your remaining energy (if this drops below 0 you are dead)
     gun_heading         #the heading of your gun, 0 pointing east, 90 pointing 
                         #north, 180 pointing west, 270 pointing south
     gun_heat            #your gun heat, if this is above 0 you can't shoot
     heading             #your robots heading, 0 pointing east, 90 pointing north,
                         #180 pointing west, 270 pointing south
     size                #your robots radius, if x <= size you hit the left wall
     radar_heading       #the heading of your radar, 0 pointing east, 
                         #90 pointing north, 180 pointing west, 270 pointing south
     time                #ticks since match start
     speed               #your speed (-8/8)
     x                   #your x coordinate, 0...battlefield_width
     y                   #your y coordinate, 0...battlefield_height
     accelerate(param)   #accelerate (max speed is 8, max accelerate is 1/-1, 
                         #negativ speed means moving backwards)
     stop                #accelerates negativ if moving forward (and vice versa), 
                         #may take 8 ticks to stop (and you have to call it every tick)
     fire(power)         #fires a bullet in the direction of your gun, 
                         #power is 0.1 - 3, this power will heat your gun
     turn(degrees)       #turns the robot (and the gun and the radar), 
                         #max 10 degrees per tick
     turn_gun(degrees)   #turns the gun (and the radar), max 30 degrees per tick
     turn_radar(degrees) #turns the radar, max 60 degrees per tick
     dead                #true if you are dead
     say(msg)            #shows msg above the robot on screen
     broadcast(msg)      #broadcasts msg to all bots (they recieve 'broadcasts'
                         #events with the msg and rough direction)
   

最近关注Ruby比较多，平时工作中也会用Ruby来写一些脚本(渐渐代替了Python)。有两个原因，Ruby的语法更符合口味(不喜欢用Python的indent约束),
Ruby也更Lisp化，Ruby的开源气氛非常好。
