---
layout: post
title: "Linux下快捷切换屏幕"
description: "Linux switch screen with key"
category: 
tags: [Linux, keycut]
---
{% include JB/setup %}

<img src="/images/screen.jpg" alt="screen" class="img-center" width="500px" />

在办公室工作的时候一般面对两个显示器，大部分时候左边用来看代码，右边用来写程序。双显示屏还是有助于提高工作效率的。有一点困扰我的是如果要切换屏幕一般得用鼠标，这对于
Emacs党是有些不能忍受的，右手离开键盘总是得停顿一下的感觉。今天找到一个解决办法。

最终找到的是这个号称Linux下键盘精灵的一个程序:[xdotool](http://www.semicomplete.com/projects/xdotool/)，下载下来编译安装。这个东西可以模拟鼠标和键盘的行为：

比如：

<pre class="prettyprint lang-sh">
xdotool search "Mozilla Firefox" windowactivate --sync key --clearmodifiers ctrl+l 
(快速切换倒firefox,并focus在地址输入栏)

xdotool getmouselocation --shell  (获取当前鼠标位置等信息)
X=880
Y=443
SCREEN=0
WINDOW=16777250

xdotool getactivewindow windowmove 100 100    # Moves to 100,100
xdotool getactivewindow windowmove x 100      # Moves to x,100
xdotool getactivewindow windowmove 100 y      # Moves to 100,y
xdotool getactivewindow windowmove 100 y      # Moves to 100,y
xdotool mousemove --screen 0 100 100          # Moves to screen 0 pos at 100,100
</pre>

有了上面windowmove命令，屏幕的切换就好实现了。写个丑陋的python脚本来保存当前的位置，切换到另外一个屏幕，再次调用的时候返回到原来的位置，
保存为mouse.py。

<pre class="prettyprint lang-python">

#!/usr/bin/python
import os
import sys
import commands

data_f = "/tmp/window_data"
now_info = commands.getoutput("xdotool getmouselocation --shell").split('\n')
x = (now_info[1])[2:]
y = (now_info[2])[2:]
screen = (now_info[3])[7:]
window = (now_info[4])[7:]

def do_store():
    data = open(data_f, "w+")
    content = screen+"\n"+x+"\n"+y+"\n"+window
    data.write(content)
    data.close()
    
def do_update():
    if screen == "1":
        new_sc = "0"
    else:
        new_sc = "1"
    cmd = "xdotool mousemove --screen " + new_sc +  " 0 0"
    commands.getoutput(cmd)

if os.path.exists(data_f):
    data = open(data_f, "r+")
    content = data.readlines()
    data.close()
    screen = content[0][0:-1]
    old_x = content[1][0:-1]
    old_y = content[2][0:-1]
    old_window = content[3]
    if old_window != window:
        cmd = "xdotool mousemove -w " + old_window + " " + old_x + " " + old_y
        commands.getoutput(cmd)
        do_store()
    else:
        do_store()
        do_update()
else:
    do_store()
    do_update()
</pre>

最后，通过Emacs下绑定快捷键来调用这个脚本即可实现两个屏幕之间的切换，又可以远离鼠标了。哈哈。

<pre class="prettyprint lang-lisp">
(defun switch-screen()
  (interactive)
  (start-process "mouse.py" nil "bash" "-c" 
                 "/home/yukang/apps/bin/mouse.py"))

(global-set-key (kbd "C-x q") 'switch-screen)
</pre>

Jekyll下写点东西快多了。


