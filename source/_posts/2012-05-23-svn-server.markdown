---
layout: post
title: "Mac 下配置 svn 服务器"
date: 2012-05-23 17:32
comments: true
tags: 
---

MAC已经自带了SVN，所以，直接使用就好

1、创建svn repository
```
svnadmin create /path/svn/pro
```
<!-- more -->
仓库位置，svn是svn的目录，pro是一个版本库的目录
```
PS：这里有个歧义，按这样的方式添加SVN后，在启动SVN服务的时候，记得要用/path/svn这个路径，而不能用/path/svn/pro这个路径，不然会报doesn't exist
```

2、配置svn用户权限。
```
/path/svn/pro/conf/目录下存在3个文件：authz,passwd,svnserve.conf
```

（1）配置svnserve.conf
将里面的
```
＃anon-access = read
＃auth-access = write
＃password-db = passwd
＃authz-db = authz
```
四行前的＃号去掉，再将 `anon-access = read` 改为 `anon-access = none`，这样禁止匿名访问
PS：这里要注意的，在＃号后是有空格的，得去掉这个空格，上文字顶格，不然也有错误


（2）配置passwd里面存的是用户与密码，有示例，直接按照它的格式添加用户和密码就可以了
```
test1=123
test2=456
```

（3）配置authz
[groups] 后面跟的是用户组设置，可以将你在passwd里设置的用户添加到不同的用户组里，那么之后，可以对不同用户组设置不同的权限，以免多用户设置麻烦，多个用户用,号分隔。可按它的示例做
```
[groups]
testgroups=test1,test2
```
之后，可以对不同的版本库进行权限设置，底下有一个示例，按它的写法写就可以了，如果需要对所有的版本库设置，利用[/]就可以了。如：

```
[/]
 @testgroups=rm
//PS：用户组前要用@符号，如果是用户，直接写用户名就可以了。rm代表可读写，显然只读就是r了
```
3、启动SVN服务
```
svnserve -d -r /path/svn
```
特别注意，路径一定是SVN的目录，不是其中一个版本库的目录，不然，能正常启动，就是访问有问题
没有任何输出，则启动成功

4、启止服务/重启
```
kill -9 svnserve
```
直接删除进程，再启动服务就可以了

5、测试
```
svn checkout svn://127.0.0.1/pro --username=test1 --password=123 ./pro
```
转载自: [http://www.cnblogs.com/onlyfu/archive/2012/05/08/2489814.html](http://www.cnblogs.com/onlyfu/archive/2012/05/08/2489814.html)