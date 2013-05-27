---
layout: post
title: "Linux下特别权限位-setuid，setgid，sticky"
date: 2011-12-16 0:08
comments: true
tags: 
---

通常，我们用八进制表示一个权限时，如644或者755，省略了最前面的一个特别权限位，完整地表示是0644或者0755，而第一位就是特别权限位。这里着重要说的是三个特别权限位：setuid，setgid和 sticky位。
<!-- more -->
setuid位：当文件设置了setuid位后，任何能够执行此文档的用户都有与文件属主相同的权限，即使得任意使用者在执行该文件时，都绑定了文件属主的权限。例如，某个程序为root拥有，又设置了setuid位，那即使是一个普通用户运行该程序，该程序的身份一样是root的身份，可以访问所有只有root可以访问的资源，设置setuid位的程序曾是被攻击的对象。setuid位典型的应用是/usr/bin/passwd，查看该文件权限显示-rwsr-xr-x，这里的s表示设置了setuid位且该文件可由文件属主执行，如果一般用户执行该文件，则在执行过程中可以获得root权限，从而更改用户密码。大写S表示文件本来没有可执行权限并且设置了setuid位。
    命令用法：chmod 4755 your_program

setgid位：与setuid位相似，只对目录有效，setgid权限位被设定以后，任何用户都拥有了该文件所属组的权限，也就是能够像该文件组内成员相同运行它。另外当一个目录被配置setgid位后，以后用别的用户创建或者复制到这个目录下的文件，它所属的组会自动被改成目录文件所在的组，除非复制是加上-p (preserve)选项。
    命令用法：chmod 2755 your_program

sticky位：一般只用在目录上，可以理解为防删除位，当一个目录被设置了sticky位，则该目录下的文件只能由

一、超级管理员删除；二、该目录的所有者删除；三、该文件的所有者删除。也就是说,即便该目录是任何人都可以写,但也只有文件的属主才可以删除文件。

要删除一个文件，你不一定要有这个文件的写权限，但你一定要有这个文件的上级目录的写权限。也就是说，你即使没有一个文件的写权限，但你有这个文件的上级目录的写权限，你也可以把这个文件给删除，而如果没有一个目录的写权限，也就不能在这个目录下创建文件。 如何才能使一个目录既可以让任何用户写入文件，又不让用户删除这个目录下他人的文件，sticky就是能起到这个作用。stciky一般只用在目录上，用在文件上起不到什么作用：[http://www.diybl.com/course/6_system/linux/linuxjq/2007211/15640.html](http://www.diybl.com/course/6_system/linux/linuxjq/2007211/15640.html)。
典型应用为/temp目录，/tmp是一个存放临时文件的目录，要求是对所有用户可写。但每个用户都只能删除自己拥有的文件。通过ls -l |grep tmp可以看到/temp的权限表述为drwsrwsrwt，其中字母t代表了这个目录被设置了粘着位。相同的需求往往在ftp服务器的upload 目录中也存在。
    命令用法：chmod 1755 your_directory

 setuid，setgid，sticky的八进制位分别是4, 2, 1，助记法表示为u+s，g+s，o+t，(删除标记位是u-s，g-s，o-t)。
 可以用 ls -l 来查看权限. 如果有这些标志, 则会在原来的执行标志位置上显示. 如

 rwsrw-r--       表示有setuid标志
 rwxrwsrw-       表示有setgid标志
 rwxrw-rwt       表示有sticky标志

那么原来的执行标志x到哪里去了呢? 系统是这样规定的, 如果本来在该位上有x, 则这些特殊标志显示为小写字母 (s, s, t). 否则, 显示为大写字母 (S, S, T)。

转载自： [http://blog.csdn.net/shrimp51/archive/2009/05/08/4159559.aspx](http://blog.csdn.net/shrimp51/archive/2009/05/08/4159559.aspx)