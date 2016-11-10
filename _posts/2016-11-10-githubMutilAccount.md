---
layout: post
title: 配置git多账号多域名
category: java
modified: 2016-11-10
tags: [github,git]
comments: true
pinned: true
excerpt: "1. 背景
　　2.配置...."
---
### 1. 背景  
　　因为公司使用的私有git服务器，而平时喜欢折腾一些小东西，写的一些代码放在公司服务器不方便，所以不可避免的要是用github作为git服务器，所以就需要双账号并且git服务器地址也不同，本文是在mac下配置的，而且命令在Linux下也可以所以如果你是Windows用户需要改一些小细节。

### 2. 配置  
#### 2.1 密钥   
		这一步很重要，可以为你每次提交代码省去很多的麻烦，不管哪一个git服务器肯定都可以配置密钥免密登录，所以命令行输入如下命定生成密钥  
```ssh-keygen -t rsa -C "your-email-address"```  
一直按entrr即可，然后进入到.ssh/文件夹，里面生成了刚才id_rsa和id_rsa.pub文件，那么如果你有两个不同的账号那么只需要再执行一次秘钥生成命令，这一次需要修改秘钥文件，你可以加一个后缀，比如我这里我生成的第二个密钥文件id_ras_github  
![id_ras](id_rsa.png)  
		使用```ssh-add id_rsa_github```命令添加密钥到ssh中。
PS：你需要复制两个xxx.pub里面的内容到git服务器你自己的账号ssh key里面。  
#### 2.2 配置两个账号不冲突  
		查看.ssh文件夹下是否有config文件，如果没有则创建```touch config```，然后添加如下内容  
```
Host github.com
 HostName github.com
 User ch710798472
 IdentityFile ~/.ssh/id_rsa_github

Host gitlab
 HostName xxxx.alibaba-inc.com
 User yourusername
 IdentityFile ~/.ssh/id_rsa
```  
如果有config文件只需要按照上面的格式添加另一个host就可以了，其中HostName对应git服务器地址，Host是别名，identityFile就是对应的密钥，User是你在服务器上使用的用户名。  
#### 3. 验证  
确保git服务器上已经添加了ssh-key之后就可以本地测试一下是否成功了，```ssh -T git@github.com```首先是测试github是否成功，如果成功会出现  
```Hi ch710798472! You've successfully authenticated, but GitHub does not provide shell access.```  
等字样，可以看到你的用户名以及成功的授权消息。  