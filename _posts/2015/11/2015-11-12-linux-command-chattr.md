---
layout: post
title: linux命令学习：chattr
categories: 操作系统
tags: [ubuntu, fcitx, idea, phpstrom]
status: publish
type: post
published: true
author: blackfox
permalink: /20151112/chattr.html
description: 解决搜狗输入法在linux系统不能输入中文,idea phpstorm 无法出入中文
---

今天在服务器上删除某个文件时发现一直删除不了，提示：Operation not permitted。 个人觉得很诡异，因为我是用root登录的，不应该没有权限，
果断google一下，原来是chattr命令在作怪，chattr命令是用来改变文件、目录属性的，和chmod，ls这些命令相比，chmod只是改变文件的读写、执行权限，
更底层的属性控制是由chattr来改变的。

chattr命令的用法：chattr [ -RV ] [ -v version ] [ mode ] files...
最关键的是在[mode]部分，[mode]部分是由+-=和[ASacDdIijsTtu]这些字符组合的，这部分是用来控制文件的
属性。
<blockquote>
    <p>+ ：在原有参数设定基础上，追加参数。</p>
    <p>- ：在原有参数设定基础上，移除参数。</p>
    <p>= ：更新为指定参数设定。</p>
    <p>A：文件或目录的 atime (access time)不可被修改(modified), 可以有效预防例如手提电脑磁盘I/O错误的发生。</p>
    <p>S：硬盘I/O同步选项，功能类似sync。</p>
    <p>a：即append，设定该参数后，只能向文件中添加数据，而不能删除，多用于服务器日志文 件安全，只有root才能设定这个属性。</p>
    <p>c：即compresse，设定文件是否经压缩后再存储。读取时需要经过自动解压操作。</p>
    <p>d：即no dump，设定文件不能成为dump程序的备份目标。</p>
    <p>i：设定文件不能被删除、改名、设定链接关系，同时不能写入或新增内容。i参数对于文件 系统的安全设置有很大帮助。</p>
    <p>j：即journal，设定此参数使得当通过mount参数：data=ordered 或者 data=writeback 挂 载的文件系统，文件在写入时会先被记录(在journal中)。
    如果filesystem被设定参数为 data=journal，则该参数自动失效。</p>
    <p>s：保密性地删除文件或目录，即硬盘空间被全部收回。</p>
    <p>u：与s相反，当设定为u时，数据内容其实还存在磁盘中，可以用于undeletion.</p>
</blockquote>

各参数选项中常用到的是a和i。a选项强制只可添加不可删除，多用于日志系统的安全设定。而i是更为严格的安全设定，只有superuser (root) 或具有CAP_LINUX_IMMUTABLE处理能力（标识）的进程能够施加该选项。

应用实例：

+ 用chattr命令防止系统中某个关键文件被修改

```bash
chattr +i /etc/fstab
```

然后试一下rm mv rename等命令操作于该文件，都是得到Operation not permitted 的结果


+ 让某个文件只能往里面追加内容，不能删除，一些日志文件适用于这种操作

```bash
chattr +a /data1/user_act.log
```