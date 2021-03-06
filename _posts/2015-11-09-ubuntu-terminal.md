---
layout: post
title: 命令提示符个性化
category: ubuntu
modified: 2015-11-09
tags: [ubuntu,terminal]
comments: true
pinned: true
excerpt: "一、简单的方法　vi ~./bashrc　直接找到~./bashrc文件中　#force_color_prompt=yes　把#给去掉，就可以简单的实现，不用复杂的修改。　二、标准定制个性化"
---

#linux 命令提示符个性化
##一、简单的方法

`vi ~./bashrc`

　　直接找到~./bashrc文件中


```
#force_color_prompt=yes
```

　　把#给去掉，就可以简单的实现，不用复杂的修改。

##二、标准定制个性化
　　shell 彩色配置出现在 bash 的个人配置文件 ~/.bashrc 或者是全局配置文件 /etc/bashrc 里面。 可以通过bashrc 里面的 PS1 变量来设置提示符的外观。

　　一般默认的情况下.bashrc里面应该是：

```
~/.bashrc
export    PS1=' \u@\h:\w\$ '
```

　　上面的意思是：用户@主机：当前目录 $

　　下面简单说说环境下默认的特殊符号所代表的意义：

```
/d ：代表日期，格式为weekday month date，例如："Mon Aug 1"
/H ：完整的主机名称。例如：我的机器名称为：fc4.linux，则这个名称就是fc4.linux
/h ：仅取主机的第一个名字，如上例，则为fc4，.linux则被省略
/t ：显示时间为24小时格式，如：HH：MM：SS
/T ：显示时间为12小时格式
/A ：显示时间为24小时格式：HH：MM
/u ：当前用户的账号名称
/v ：BASH的版本信息
/w ：完整的工作目录名称。家目录会以 ~代替
/W ：利用basename取得工作目录名称，所以只会列出最后一个目录
/# ：下达的第几个命令
/$ ：提示字符，如果是root时，提示符为：# ，普通用户则为：$
```

####转义序列

　　要是通过彩色化提示符来增加个性化，就要用到转义序列。 转义序列就是一个让 shell 执行一个特殊步骤的控制指令。 转义序列通常都是以 ESC 开头（这也是它的命名原因）。 在 shell 里表示为 ^[ .这种表示法需要一点时间去适应， 也可以用 \033 完成相同的工作（ESC 的 ASCII 码用十进制表示就是 27，等于用八进制表示的 033）。

　　要直接在 shell 里面输入转义序列我们需要先按 ctrl-v：CTRL-v ESC。 

　　下面进入主题,说下该如何用shell的颜色。我用我自己的shell颜色设置详细说明.

代码：

```
PS1="\033[1;32;40m[\033[0;32;40m\u@\h:\033[1;35;40m\w\033[1;32;40m]\033[1;31;40m$\033[1;32;40m
"
```

　　最外边的""是为了把转义序列的字符串括起来，防止转义序列的文本显示在 shell 里占用太多的空间。

　　\033 声明了转义序列的开始，然后是 [ 开始定义颜色。 后面的 0 定义了默认的字体宽度，接着的中间的数字定义字符颜色。最后面的数字定义了字符背景色。字母m是定义本身所必须的，字母m后面的字符就是你想改变的字符了。

　　可选颜色：红色、绿色、黄色、蓝色、洋红、青色和白色。 他们对应的颜色代码是：30（黑色）、31（红色）、32（绿色）、 33（黄色）、34（蓝色）、35（洋红）、36（青色）、37（白色）。

　　用同样色方法设置背景色，不过要把第一个数字“3”替换成“4”， 例如 40、41、42、43、44、45、46、47。

　　比如上面我的定义中： 最后显示的结果是在$号之前的字符全部用一个浅绿色的中括弧扩起来。$字符 显示为红色。后面的输入显示为高亮浅绿色。在括弧里面，用户名@主机显示为深绿色，路径显示为洋红。

##自己正在使用的

```
PS1="┌─[\[\033[0;32m\]\A \[\033[0;31m\]\u\[\033[0;34m\]@\[\033[0;35m\]\h\[\033[0;34m\]:\[\033[00;36m\]\W\[\033[0;33m\] $\[\033[0m\]]\n└─>"
```

![](http://i.imgur.com/SIppgF9.png)
#####　　上述所有的PS1可以拷贝进去放在./bashrc最后一行，重启terminal试试效果，说不定会有惊喜！

#补充：
　　也可以直接用zsh,fish等，用的比较多的是oh my zsh！有些用户喜欢个性化定制的桌面，添加一些桌面监测小工具，可以试试[Unity Tweak Tool](http://www.2cto.com/os/201501/372413.html), 或者Gnome Tweak Tool，但是最好是Ubuntu系统大于12.04版本，不然不支持很多特性。