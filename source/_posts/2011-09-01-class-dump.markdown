---
layout: post
title: "class dump"
date: 2011-09-01 13:39
comments: true
tags: 
---

class dump是一个命令行工具，用来监测存储在Mach-O二进制文件理的Objective-C运行时信息。它为类(class)，分类(category)和协议(protocol)生成声明信息。这些信息与 otool -ov 命令提供的信息是一样的，但表示得更像正常的Objective-C的声明，所以它更紧凑，更易读
<!-- more -->

为什么要使用class-dump
对于好奇的人，是一个好工具。你可以看到闭源应用程序，框架(framework)和软件包(bundle)的设计。

[http://stevenygard.com/download/](http://stevenygard.com/download/)

[http://bitbucket.org/nygard/class-dump](http://bitbucket.org/nygard/class-dump)

用法：
class-dump [options]
选项可以是
```
-a		\\ 显示实例变量偏移
-A		\\ 显示实现地址
–arch <arch>		\\ 选择一个指定的架构，如ppc, ppc7400, ppc64, i386, x86_64
-C <regex>		\\ 只显示同正则表达式匹配的类
-f <str>		\\ 在方法名中查找字符串
-H		\\ 在当前目录生成头文件，或者在用-o选项指定的目录生成
-I		\\ 对类，目录，协议 按照继承关系(inheritance)进行排序(覆盖-s选项)
-o <dir>		\\ 为-H选项指定输出目录
-r		\\ 递归扩展framework，并修复VM共享库
-s		\\ 按名称对classes和categories进行排序
-S		\\ 按名称对方法(method)进行排序
–list-arches		\\ 类出文件中的arches,然后退出
–sdk-root		\\ 指定 SDK root 路径（完全路径，或者 4.1, 4.0, 3.2, 10.6，10.5，3.1.3，3.1.2）
```

class-dump-x是一个class-dump的修改版，在当时（2008年) class-dump 3.1.2不支持Objective-C 2.0 ABI). Objective-C 2.0 ABI删除了OBJC段，在data段引入了一些新的section. 并且class/obj的布局也边了。它不生成@property声明，因为所有的properties被映射到方法了。 不创建@property 元素，这样的源码可以与objc 1.0的编译器兼容
class_dump_z 是对上面两个的改进
[http://networkpx.googlecode.com/files/class-dump-z](http://networkpx.googlecode.com/files/class-dump-z)

为什么需要另外一个class-dump

因为class-dump-x对ivar offsets的计算也是错误的，并不支持properties
原版的class-dump虽然也支持ABI2 了，但对ivar 的计算依然是错误的
class-dump-z 主要针对 iPhone 程序进行dump, 不支持以下特性
64位（除非以后iPhone上的内存超过4G了）

Objective-C 1.0 ABI(iPhone用2.0)
class-dump-z完全用C++重写，避免动态调用，不像class-dump和class-dump-x那样使用Objective-c写。删除不必要的调用，使得class-dump-z比它的前任们快10倍左右。并且可以在Linux，Mac, iPhone上运行。

选项
```
-p		\\ 转换未声明的getters和setters为properties 
-h proto		\\ 隐藏那些已经出现在 协议中的方法
-h super		\\ 隐藏继承来的方法
-y <root>		\\ 选择sysroot, 默认是最后一版的iPhoneOS SDK 或者 /
-u <arch>		\\ 选择指定的架构( armv6, armv7等)
-a		\\ 打印ivar 偏移
-A		\\ 打印 实现的VM地址
-k		\\ 显示额外的注释
-k -k		\\ 显示更多注释
-R		\\ 显示指针声明 , int *a 而不是 int* a(因为前者更明显地表达了指针的语义)
-N		\\ 保持原始结构名 （不用 CFArrayRef代替 __CFArray)
-b		\\ 在 +/- 号之后放一个空格,  也就是 + (void) ，而不是 +(void)
-i  <file>		\\ 读取并更新签名提示文件
-C  <regex>		\\ 只显示匹配的types
-f  <regex> 	\\ 只显示匹配的methods
-g		\\ 只显示导出classes
-X <list>	\\ 忽略所有types(除了categories)
-h cats		\\ 隐藏categories
-h dogs		\\ 隐藏协议（这哥们太幽默了，上面是cats（分类），这里就是dogs了
-S		\\ 将types按字幕顺序排序
-s		\\ 将方法按字母顺序排序
-z		\\ 按照字母顺序对方法排序，但将class方法和 -init 放在最见面
-H		\\ 分离头文件
-o <dir>		\\ 将头文件放到这个目录，而不是当前目录
```